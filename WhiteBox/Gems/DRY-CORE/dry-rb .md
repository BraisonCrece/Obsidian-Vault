# What is <span style='color:#2d98da'>dry-rb</span>? 🔷
**dry-rb** is a collection of next-generation Ruby libraries
**dry-rb** helps you write <span style='color:#3867d6'>clear</span>, <span style='color:#3867d6'>flexible</span>, and more <span style='color:#3867d6'>maintainable</span> Ruby code. Each **dry-rb** gem fulfills a common task, and together they make a powerful platform for any kind of Ruby application.

**Dry.rb** is also the foundation for ambitious projects like **ROM** and **Hanami**, which provide <span style='color:#3867d6'>alternatives to ActiveRecord</span> and <span style='color:#3867d6'>Rails</span>, showcasing sound engineering principles and innovative ideas.

## Gems 💎
- ### [dry-auto_inject](https://dry-rb.org/gems/dry-auto_inject/1.0)
    Container-agnostic constructor injection mixin
- ### [dry-cli](https://dry-rb.org/gems/dry-cli/1.0)
    General purpose Command Line Interface (CLI) framework
- ### [dry-configurable](https://dry-rb.org/gems/dry-configurable/1.0)
    Thread-safe configuration mixin
- ### [dry-container](https://dry-rb.org/gems/dry-container/0.11)
    Simple and thread-safe IoC container
- ### [dry-core](https://dry-rb.org/gems/dry-core/1.0)
    A toolset of small support modules used throughout the dry-rb & rom-rb ecosystems
- ### [dry-effects](https://dry-rb.org/gems/dry-effects/0.4)
    Algebraic effects in Ruby
- ### [dry-equalizer](https://dry-rb.org/gems/dry-equalizer/0.2)
    Simple mixin providing equality methods
- ### [dry-events](https://dry-rb.org/gems/dry-events/1.0)
    Standalone pub/sub API
- ### [dry-files](https://dry-rb.org/gems/dry-files/1.0)
    File utilities
- ### [dry-inflector](https://dry-rb.org/gems/dry-inflector/1.0)
    Standalone inflections
- ### [dry-initializer](https://dry-rb.org/gems/dry-initializer/3.1)
    DSL for defining initializer params and options
- ### [dry-logger](https://dry-rb.org/gems/dry-logger/1.0)
    Logging for Ruby applications
- ### [dry-logic](https://dry-rb.org/gems/dry-logic/1.5)
    Predicate logic with composable rules
- ### [dry-matcher](https://dry-rb.org/gems/dry-matcher/1.0)
    Flexible, expressive pattern matching
- ### [dry-monads](https://dry-rb.org/gems/dry-monads/1.6)
    Useful, common monads in idiomatic Ruby
- ### [dry-monitor](https://dry-rb.org/gems/dry-monitor/1.0)
    Instrumentation for Ruby applications
- ### [dry-rails](https://dry-rb.org/gems/dry-rails/0.7)
    The dry-rb railtie
- ### [dry-schema](https://dry-rb.org/gems/dry-schema/1.13)
    Schema coercion & validation
- ### [dry-struct](https://dry-rb.org/gems/dry-struct/1.6)
    Attribute DSL for struct-like objects
- ### [dry-system](https://dry-rb.org/gems/dry-system/1.0)
    Organize your code into reusable components
- ### [dry-transaction](https://dry-rb.org/gems/dry-transaction/0.15)
    Business transaction DSL
- ### [dry-transformer](https://dry-rb.org/gems/dry-transformer/1.0)
    Data transformation toolkit
- ### [dry-types](https://dry-rb.org/gems/dry-types/1.7)
    Flexible type system with many built-in types
- ### [dry-validation](https://dry-rb.org/gems/dry-validation/1.10)
    Powerful data validation
- ### [dry-view](https://dry-rb.org/gems/dry-view/0.7)
    Functional, standalone view rendering


---

# dry-initializer
Define objects attributes.
```rb
class User
  extend Dry::Initializer

  param  :name,  proc(&:to_s)
  param  :role,  default: proc { 'customer' }
  option :admin, default: proc { false }
  option :phone, optional: true
end

user = User.new 'Vladimir', 'admin', admin: true

user.name  # => 'Vladimir'
user.role  # => 'admin'
user.admin # => true
user.phone # => nil
```

Use `param` to define plain argument.
Use `option` to define named (hash) argument.

modifyers:
```rb
default: proc { some_value }
reader: [:public, :private, :protected, true, false]
type: proc(&:[to_s, to_h, to_i ...]) # coercer
type: Dry::Types['strict.string'] # strong types
# You can use arrays for values that should be wrapped to array
option :emails, [proc(&:to_s)]

# nested structure definition
  option :emails, [] do
    option :address,     proc(&:to_s)
    option :description, proc(&:to_s)
  end
  user = User.new 
			  name: "joe",
              emails: { 
	              address: "joe@example.com", 
	              description: "Job email" 
		      }
```

By default the initializer is tolerant for both params (positional arguments) and options. **All unknown arguments of the initializer are ignored silently**.

By default both **params and options are mandatory**. Use `:default` key to make them optional.

```rb
option :phone, optional: true
```

You cannot define <span style='color:#3867d6'>required</span> **parameter** after optional one. The following example raises `SyntaxError` exception:

```rb
  param :name, default: proc { 'Unknown name' }
  param :email # => #<SyntaxError ...>
```

<span style='color:#eb3b5a'>No writers are defined</span>. Define them using pure ruby `attr_writer` when necessary

The initializer uses special constant `Dry::Initializer::UNDEFINED` to distinguish variables that are set to `nil` from those that are not set at all.

When no value was provided, the constant is assigned to a variable, but hidden in a reader

```rb
class User
  extend Dry::Initializer
  option :email, optional: true
end

user = User.new
user.email
# => nil
user.instance_variable_get :@email
# => Dry::Initializer::UNDEFINED
```

This gives you full control of the real state of the attributes. However, all <span style='color:#eb3b5a'>that checks cost about >30% of instantiation time</span>, and make attribute readers 2 times slower.

To avoid the overhead in cases you don't care about the differences between `nil` and undefined, you can use a light version of the module. Add `[undefined: false]` config to either `extend` or `include` line of code:

```rb
extend Dry::Initializer[undefined: false]
```


# dry-transactions
- A business transaction is a <span style='color:#3867d6'>series of operations</span> where any can fail and stop the processing.
- A business transaction may resolve its operations <span style='color:#3867d6'>using an external container</span>.
- A business transaction can <span style='color:#3867d6'>describe its steps on an abstract level</span> without being coupled to any details about how individual operations work.
- A business transaction doesn’t have <span style='color:#3867d6'>any state</span>.
- Each operation shouldn’t accumulate state, instead it <span style='color:#3867d6'>should receive an input and return an output without causing any side-effects</span>.
- The only interface of an operation is `#call(input)`.
- <span style='color:#3867d6'>Each operation </span>provides a meaningful piece of functionality and <span style='color:#3867d6'>can be reused</span>.
- <span style='color:#3867d6'>Errors in any operation should be easily caught and handled</span> as part of the normal application flow.

### Why?
Allowing a business transaction’s steps to be independent operations directly addressable via a container means that they can be tested in isolation and easily reused throughout your application. The business transaction can then become a series of <span style='color:#3867d6'>declarative steps</span>, which ensures that it’s easy to understand at a glance.

The output of each step is a [dry-monads](https://github.com/dry-rb/dry-monads) `Result` object (either a `Success` or `Failure`). This allows the steps to be chained together and ensures that processing stops in the case of a failure. Returning a `Result` from the overall transaction also allows for error handling to remain a primary concern without it getting in the way of tidy, straightforward operation logic.