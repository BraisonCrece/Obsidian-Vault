# Raxl

Raxl is a data fetching framework which helps to batch, cache and parallelize accessing remote data. It is a Ruby port of Facebook's [Haxl](https://github.com/facebook/haxl), based on the paper [There is no Fork: an Abstraction for Efficient, Concurrent, and Concise Data Access](https://simonmar.github.io/bib/papers/haxl-icfp14.pdf)

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'raxl'
```

And then execute:

    $ bundle

Or install it globally by:

    $ gem install raxl

## Motivation

[Modularity](https://en.wikipedia.org/wiki/Modular_programming) and performance are often at odds with each other: When calling the two modules `A` and `B` (each performing their own data fetches) from `C` it is easy to accidentally duplicate data fetches when executing `C`. The only solution to avoid that is to extract the requests made in `A` and `B`, execute them in `C` and explicitly pass the results into `A` and `B`. This makes the modules less modular because the parent component needs to know exactly which data is required by each sub component. It might also lead to either unnecessary data fetches or code duplication because some data might only be required under specific conditions.

The following example demonstrates these problems:

```ruby
# Remote requests:
# * get_organization_for
# * get_newest_user
# * get_all_users

def render_newest_user
  u = get_newest_user
  github_organization = get_organization_for(u.id)
  "Newest User: #{u.name} (#{github_organization.name})"
end

def render_user_organizations
  users = get_all_users
  users.map do |u|
    org = get_organization_for(u.id)
    "* #{u.name}: #{org.name}"
  end.join("\n")
end

def render_page
  render_newest_user + "\n-----\n" + render_user_organizations
end
```

This code has at least three potential performance issues and one issue with correctness:

1. `render_user_organizations` and `render_newest_user` could be executed in parallel, saving latency.
2. Several users might belong to the same organization leading to multiple requests for one organization.
3. All organization requests could be executed in parallel or batched into one request (e.g. `GET /organization?user_ids=3,51,...`).
4. Two remote calls to the same organization at differnt ponts in time don't generally yield the same result,
   leading to an inconsistent UI.

Here's the same code written using Raxl:

```ruby
def render_newest_user
  get_newest_user.then do |u|
    get_organization_for(u.id).fmap do |org|
      "Newest User: #{u.name} (#{org.name})"
    end
  end
end

def render_user_organizations
  get_all_users.then do |users|
    # sequence converts a list of computations to
    # a computation evaluating to a list: [t a] -> t [a]
    sequence(users.map do |u|
      get_organization_for(u.id).fmap do |o|
        "* #{u.name}: #{o.name}"
      end
    end).fmap { |list| list.join("\n") }
  end
end

def render_page
  map_n(render_newest_user, render_user_organizations) do |a, b|
    a + "\n-----\n" + b
  end
end

def main
  puts render_page.run
end
```

Vamos a explorar las diferencias entre ambos ejemplos:

1. **Enfoque Imperativo vs. Funcional**:
    
    - **Primera versión**: Es imperativa. Se hacen llamadas secuenciales para obtener información y se construyen strings de manera directa.
    - **Segunda versión**: Es funcional. La estructura y flujo del código se describen mediante mónadas y funciones, y la ejecución real se posterga hasta que se llama a `.run`.
2. **Manejo de Efectos**:
    
    - **Primera versión**: Los efectos (p.ej., llamadas a servicios remotos) se ejecutan inmediatamente cuando se llaman los métodos.
    - **Segunda versión**: Los efectos se "envuelven" en estructuras mónadicas (en este caso, parece una mónada similar a la `Promise` o `Future`). Esto permite describir el flujo del código sin ejecutar los efectos hasta que se llame a `.run`.
3. **Composición**:
    
    - **Primera versión**: La composición se realiza mediante llamadas simples y concatenación de strings.
    - **Segunda versión**: Se utiliza la función `map_n` para componer los resultados de múltiples mónadas. `sequence` se usa para convertir una lista de mónadas en una mónada que produce una lista.
4. **Manejo de errores**:
    
    - **Primera versión**: El manejo de errores no está explicitado. Si una llamada falla, generará un error directamente.
    - **Segunda versión**: Dado que las mónadas suelen tener un mecanismo incorporado para manejar fallos (similar a `Result` en algunos lenguajes), es probable que los errores puedan ser manejados de una manera más limpia y centralizada.
5. **Ejecución**:
    
    - **Primera versión**: La ejecución ocurre mientras el código se ejecuta.
    - **Segunda versión**: Todo el flujo se "construye" primero y luego se ejecuta con `.run`.

The issues mentioned above are now fixed while keeping the modularity of the original code:

1. `render_user_organizations` and `render_newest_user` are executed concurrently.
2. All organization requests are cached and for each individual organization exactly one request is sent.
3. All organization requests are batched together and passed to the appropriate handler where you can
   decide between batching and running the requests concurrently.
4. Due to `2.` the inconsistent UI is fixed: There's exactly one request for each organization.

Internally Raxl passes a cache around which contains all requests and their responses.
Theoretically this cache could be utilized for testing by once running your code against real data sources
and using that data in your tests by explicitly filling this cache in your tests:

```ruby
cache = Raxl::Helpers.set_cache({Request1.new(2) => Response1.new(4)})
result = (cache >> my_program_under_test).run
expect(result).to eq 4
```

This is not yet implemented, though.

## Usage

The fundamental idea behind Raxl is to encode each request as a simple data structure, combine these requests in various ways and interpret that description of your program later. A so called Raxl computation can be roughly thought of as a promise. However, while promises get executed immediately, Raxl computations only get executed once you call `Raxl::Fetch#run` on it.

The following steps are necessary to make use of Raxl:

_Note: I will use a subset of Haskell's type signature syntax throughout this document to describe some functions. Unfortunately there's no concise type signature syntax in Ruby one could use within a document and some of the functions are propably too hard to understand without knowing the types involved. [Here]'s a basic introduction to Haskell's type signatures._

### Define requests as data types

A request object must implement several methods to be used within Raxl:

1. `eql?` and `hash`: These two methods are required for caching to work since
   caching is implemented using a hashmap (`Hash`).
2. `fetch_name`: Requests within one batch are grouped by this name and passed
   to their respective `fetch_proc`. If two requests don't share the same `fetch_name`
   they will never be passed to `fetch_proc` at the same time, making batching impossible.
3. `fetch_proc`: Either an instance of `AsyncFetch` or `SyncFetch` depending on whether
   requests should be executed concurrently or not.

To reduce boilerplate you can move `fetch_name` and `fetch_proc` to a common superclass
for each data source.

Here's a minimal definition of a request:

```ruby
module DataSource1Gateway
  def self.fetch(env, requests)
    Raxl::SyncFetch.new(-> {
      requests.each do |req|
        if req.request.class == Request1
          result = ... # do the remote request
          req.reference.put_success result
        end
      end
    })
  end
end

class DataSource1Request
  def fetch_name
    :data_source_1_request
  end

  def fetch_proc
    DataSource1Gateway.public_method(:fetch)
  end
end

class Request1 < DataSource1Request
  attr_reader :attribute1, :attribute2

  def initialize(attribute1, attribute2)
    @attribute1, @attribute2 = attribute1, attribute2
  end

  def eql?(other)
    self == other
  end

  def ==(other)
    other.is_a?(Request1) &&
      [self.attribute1, self.attribute2] == [other.attribute1, other.attribute2]
  end

  def hash
    [self.attribute1, self.attribute2].hash
  end
end
```

### Embed custom requests in Raxl

All requests are simple data structures. In order to combine them as seen in the motivating example
you first have to wrap them into a `Raxl::Fetch` object. This can be done in two ways depending on your
caching needs.

Generally you want to disable caching for side-effects because otherwise
`set_temperature("Cologne", 12) >> set_temperature("Cologne", 4)` would skip the second request.

```ruby
def get_weather(city)
  # with cache
  Raxl.data_fetch(GetWeather.new(city))
end

def set_temperature(city, degree)
  # without cache
  Raxl.uncached_request(SetTemperature.new(city, degree))
end
```

### Combine requests

_Note: Every time we call `Raxl.data_fetch` we get back a wrapped description of what request to execute.
There won't be any remote calls made until you call `run` on that description
(see [Run/interpret requests](#runinterpret-requests)). In other words: All calls to `data_fetch`,
`fmap`, `then` and `map_n` are pure which makes many refactorings safe to do._

#### Mapping a function over a response (`fmap`, Functor)

```haskell
fmap :: Fetch a -> (a -> b) -> Fetch b`
```

To transform remote data you can use `fmap` which takes a block to define how to manipulate the response.

```ruby
get_temperature('New York City').fmap { |t| (t - 32) * 5/9r }
```

#### Mapping a function over multiple responses (`*`, Applicative Functor)

```haskell
* :: Fetch (a -> b) -> Fetch a -> Fetch b`
```

`fmap` alone is not powerful enough to combine multiple requests in a meaningful way.
There's for example no way to fetch the temperatures from two cities and calculate an average temperature just using `fmap`. The operator `*` solves this:

```ruby
Raxl::Fetch.pure(-> (t1, t2) { (t1 + t2) / 2.0 }.curry) * get_temperature('New York City') * get_temperature('Boston')
```

Since this is pretty cumbersome to write there exists a helper in `Raxl::Helpers` called `map_n`:

```haskell
-- Type signature for the two argument case:
map_2 :: (a -> b -> c) -> Fetch a -> Fetch b -> Fetch c
```

```ruby
Raxl::Helpers.map_n(get_temperature('New York City'), get_temperature('Boston')) do |t1, t2|
  (t1 + t2) / 2.0
end
```

_Note: When using the combinator `*` (or any of its derived functions like `sequence` or `map_n`)
all requests are collected and collectively passed to `fetch_proc` which allows for the requests to be dispatched concurrently or to use a different batch API to save requests. For example the average temperature would only require one request if the weather API supports a batching endpoint along the lines of `/get_temperature?city_id=...&city_id=...`._

#### Sending a request depending on the result of a previous request (`then`, Monad)

```haskell
then :: Fetch a -> (a -> Fetch b) -> Fetch b`
```

`*`/`map_n` is not powerful enough to express requests which depend on previous responses. Therefore `then` is required to express these dependencies:

```ruby
def temperature_at_users_place(user)
  get_current_city(user).then do |city|
    get_temperature(city)
  end
end
```

_Note 1: While the blocks of `fmap` and `map_n` always return pure values the block of `then`
returns a new request. Formally the types are as follows: `fmap :: Fetch a -> (a -> b) -> Fetch b` and `then :: Fetch a -> (a -> Fetch b) -> Fetch b`. The library will warn you (at runtime) when you screw up the type of `then`._

_Note 2: When using `then` you lose the ability to batch or parallize requests since request A needs to be fully evaluated before request B is even known. Therefore: Try to use `fmap`/`map_n` as often as possible. Avoiding `then` also avoids ugly nesting in the absence of do notation._

### Run/interpret requests

In order to execute a Raxl computation you have to call `run` on it. `run` passes the collected requests
for the current batch to `fetch_proc` where it is your responsibility to interpret the requests and make
the appropriate remote calls.
`run` takes an additional parameter which can be used to pass in context specific data (e.g. database
connection or global cache).

```ruby
module A
  def fetch(_env, requests)
    SyncFetch.new -> {
      requests.each do |request_wrapper|
        case request_wrapper.request
        when Request1
          request_wrapper.reference.put_success(SomeData.new(2, "foo"))
        when Request2
          request_wrapper.reference.put_failure(SomeException.new)
        end
      end
    }
  end
end

computation.run({client: postgresql_client})
```

#### Batching

When `fetch_proc` gets passed multiple requests you can potentially take advantage of batch
API requests: e.g. sending one `/foo?ids=1,3,4` HTTP request instead of multiple `/foo/:id` requests.
This can be accomplished by grouping the requests by request class and merging the attributes. See the
[sql example](examples/sql.rb).

#### Concurrent requests

Instead of `SyncFetch` you can also return `AsyncFetch` from `fetch_proc`. In contrast to `SyncFetch`
the lambda wrapped in `AsyncFetch` takes an additional argument: a proc representing the remote fetches
for the other data sources. In doing so you can execute all data sources concurrently:

```ruby
AsyncFetch.new -> (inner) {
  sub_threads = requests.map do |request_wrapper|
    Thread.new do
      response = make_http_request(request_wrapper.request)
      request_wrapper.reference.put_success response
    end
  end
  inner.call
  sub_threads.each(&:join)
}
```

## Examples

All examples in `examples/` should be self-contained:

```sh
> cd examples
> bundle install
> bundle exec ruby sql.rb
I, [2018-07-05T15:14:31.434058 #13134]  INFO -- : (0.000205s) SELECT * FROM `comments`
I, [2018-07-05T15:14:31.434974 #13134]  INFO -- : (0.000181s) SELECT * FROM `users` WHERE (`id` IN (1, 2, 3))
Statistics:
Comments count: 6
Longest comment: second comment

-----

Comment 1 by Peter (42): 'first comment'
Comment 2 by Conny (34): 'second comment'
Comment 3 by Sandi (35): 'third comment'
Comment 4 by Conny (34): 'fourth comment'
Comment 5 by Conny (34): 'fifth comment'
Comment 6 by Sandi (35): 'sixth comment'
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/[USERNAME]/raxl.

## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).
