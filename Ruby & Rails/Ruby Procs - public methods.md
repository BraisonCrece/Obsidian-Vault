
## Public instance methods

### <<
The `<<` method on a Proc object is used for **function composition**. This means you can "chain" two procs, `f` and `g`, such that when you call the resulting proc, `g` is called first, and then `f` is called with the result of `g`.

Example:
1. First, two procs, `f` and `g`, are defined:

   ```ruby
   f = proc {|x| x * x }  # This proc takes a number and returns its square.
   g = proc {|x| x + x }  # This proc takes a number and returns its double.
   ```

2. Then, the `<<` method is used to compose `f` and `g` into a new proc:

   ```ruby
   h = f << g  # This is the same as proc {|x| f.call(g.call(x)) }
   ```

   The proc `h` takes a number, passes it to `g` (which doubles it), and then passes the result to `f` (which squares it).

3. Finally, the composed proc is called with a number:

   ```ruby
   p h.call(2)  #=> 16
   ```


## >>

The `>>` method on a Proc object is used for function composition, similar to the `<<` method. However, the order of operation is reversed: when you call the resulting proc, `f` is called first, and then `g` is called with the result of `f`.

Here's a step-by-step breakdown of the example you provided:

1. First, two procs, `f` and `g`, are defined:

   ```ruby
   f = proc {|x| x * x }  # This proc takes a number and returns its square.
   g = proc {|x| x + x }  # This proc takes a number and returns its double.
   ```

2. Then, the `>>` method is used to compose `f` and `g` into a new proc:

   ```ruby
   h = f >> g  # This is the same as proc {|x| g.call(f.call(x)) }
   ```

   The proc `h` takes a number, passes it to `f` (which squares it), and then passes the result to `g` (which doubles it).

3. Finally, the composed proc is called with a number:

   ```ruby
   p h.call(2)  #=> 8
   ```

The `>>` method can also be used to compose a Proc with **any object that responds to the `call` method**, such as a Method or a custom object. Here's an example:

```ruby
class Parser
  def self.call(text)
     # ...some complicated parsing logic...
  end
end

pipeline = File.method(:read) >> Parser >> proc { |data| puts "data size: #{data.count}" }
pipeline.call('data.json')
```

In this example, `File.method(:read)`, `Parser`, and a proc are composed into a pipeline that reads a file, parses the content, and then prints the size of the data.


## Calling a Proc and Argument handling

1. **Calling a Proc**: There are several ways to call a Proc in Ruby. You can use:
	- `call` 
	- `[]`, 
	- `.` (dot) 
	- `yield`
	
	All of these do the same thing: they invoke the block of code in the Proc, passing the arguments to the block.

   Here's an example:

   ```ruby
   a_proc = Proc.new {|scalar, *values| values.map {|value| value*scalar } }
   a_proc.call(9, 1, 2, 3)    #=> [9, 18, 27]
   a_proc[9, 1, 2, 3]         #=> [9, 18, 27]
   a_proc.(9, 1, 2, 3)        #=> [9, 18, 27]
   a_proc.yield(9, 1, 2, 3)   #=> [9, 18, 27]
   ```

   In this example, `a_proc` is a Proc that takes a scalar and an array of values, and returns a new array where each value has been multiplied by the scalar. The four calls to `a_proc` all do the same thing and produce the same result.

2. **Argument Handling**: Procs handle arguments differently depending on how they were created.

   - Procs created with `Proc.new` or `Kernel.proc` are lenient with arguments. If you pass too many arguments, the extra ones are ignored. If you pass too few, the missing ones are set to `nil`.

     ```ruby
     a_proc = proc {|a,b| [a,b] }
     a_proc.call(1)   #=> [1, nil]
     ```

     In this example, `a_proc` expects two arguments, but only one is passed. The missing argument is set to `nil`.

   - Procs created with `lambda` or `->` are strict with arguments. If you pass the wrong number of arguments, an error is thrown.

     ```ruby
     a_proc = lambda {|a,b| [a,b] }
     a_proc.call(1)   
     # ArgumentError: wrong number of arguments (given 1, expected 2)
     ```

     In this example, `a_proc` expects two arguments, but only one is passed. This causes an `ArgumentError`.


## Arity

The `arity` method on a Proc object returns the number of mandatory arguments that the Proc takes. Here's how it works:

1. If the Proc takes no arguments, `arity` returns `0`.

   ```ruby
   proc {}.arity                  #=>  0
   proc { || }.arity              #=>  0
   ```

2. If the Proc takes a specific number of arguments, `arity` returns that number.

   ```ruby
   proc { |a| }.arity             #=>  1
   proc { |a, b| }.arity          #=>  2
   proc { |a, b, c| }.arity       #=>  3
   ```

3. If the Proc takes a variable number of arguments (indicated by `*`), `arity` returns `-n-1`, where `n` is the number of mandatory arguments.

   ```ruby
   proc { |*a| }.arity            #=> -1
   proc { |a, *b| }.arity         #=> -2
   proc { |a, *b, c| }.arity      #=> -3
   ```

4. If the Proc takes keyword arguments, they are considered as a single additional argument. If any keyword argument is mandatory, that argument is also mandatory.

   ```ruby
   proc { |x:, y:, z:0| }.arity   #=>  1
   proc { |*a, x:, y:0| }.arity   #=> -2
   ```

5. If the Proc has optional arguments (indicated by `=`), the behavior of `arity` depends on whether the Proc is a lambda or not.

   - For non-lambda Procs, `arity` returns the number of mandatory arguments.

     ```ruby
     proc { |a=0| }.arity         #=>  0
     proc { |a=0, b| }.arity      #=>  1
     proc { |a=0, b=0| }.arity    #=>  0
     proc { |a, b=0| }.arity      #=>  1
     proc { |(a, b), c=0| }.arity #=>  1
     proc { |a, x:0, y:0| }.arity #=>  1
     ```

   - For lambda Procs, `arity` returns `-n-1`, where `n` is the number of mandatory arguments.

     ```ruby
     lambda { |a=0| }.arity        #=> -1
     lambda { |a=0, b| }.arity     #=> -2
     lambda { |a=0, b=0| }.arity   #=> -1
     lambda { |a, b=0| }.arity     #=> -2
     lambda { |(a, b), c=0| }.arity #=> -2
     lambda { |a, x:0, y:0| }.arity #=> -2
     ```



## binding

The `binding` method on a Proc object returns the context in which the Proc was defined. This context includes the values of local variables, constants, and methods that were in scope when the Proc was defined.

Here's a step-by-step example:

1. First, a method `fred` is defined that takes a parameter `param` and returns a new Proc.

   ```ruby
   def fred(param)
     proc {}
   end
   ```

   The Proc doesn't do anything—it's an empty block of code. However, because it's defined inside `fred`, it has access to `fred`'s local variables, including `param`.

2. Then, `fred` is called with an argument `99`, and the resulting Proc is stored in `b`.

   ```ruby
   b = fred(99)
   ```

   At this point, `b` is a Proc that doesn't do anything, but it remembers that `param` was `99` when it was defined.

3. Finally, `eval` is used to evaluate the string `"param"` in the context of `b`.

   ```ruby
   eval("param", b.binding)   #=> 99
   ```

   `eval` takes a string of Ruby code and evaluates it. Normally, it evaluates the code in the current context, but you can pass a second argument to `eval` to specify a different context. In this case, the context is `b.binding`, which is the context in which `b` was defined. In that context, `param` is `99`, so that's what `eval` returns.


## curry, curry(arity)

First of all, WTH is "currying" **?**:

**"Currying"** is a concept in functional programming. It's named after Haskell Curry, a mathematician and logician. When a function is "curried", it means that **the function has been transformed into a sequence of functions, each with a single argument**.

Let's take an example of a simple function that adds two numbers:

```ruby
def add(a, b)
  a + b
end
```

You would typically call this function with two arguments, like so: `add(1, 2)  # => 3`.

Now, let's create a curried version of this function:

```ruby
def curried_add(a)
  proc { |b| a + b }
end
```

The `curried_add` function takes one argument, `a`, and returns a new function (in this case, a Proc) that takes another argument, `b`. You would call this function like so: `curried_add(1).(2)  # => 3`.

As you can see, the curried version of `add` allows you to "fix" the first argument and create a new function that only needs the second argument. <span style='color:#eb3b5a'>This can be useful in a variety of scenarios, such as when you want to create a suite of similar functions without repeating code</span>.

In the context of Ruby's `Proc#curry` method, it transforms a Proc that takes multiple arguments into a sequence of Procs that each take one argument.


**Proc.new.curry**
The `curry` method on a Proc object returns a new Proc that is a curried version of the original Proc. 

Currying is a technique in functional programming where a function with multiple arguments is transformed into a sequence of functions, each with a single argument.

EXAMPLE
1. First, a Proc `b` is defined that takes three arguments `x`, `y`, and `z`, and returns their sum. If an argument is not provided, it defaults to `0`.

   ```ruby
   b = proc {|x, y, z| (x||0) + (y||0) + (z||0) }
   ```

2. Then, `curry` is called on `b` to create a curried version of `b`.

   ```ruby
   c = b.curry
   ```

   The curried Proc `c` can be called with one argument at a time. Each time it's called, it returns a new Proc that takes the next argument.

3. Finally, the curried Proc is called with arguments `1`, `2`, and `3`.

   ```ruby
   p c[1][2][3]  #=> 6
   ```

   Here's what happens in this step:

   - First, `c[1]` returns a new Proc that takes the next argument. Let's call this new Proc `d`.
   - Then, `d[2]` returns another new Proc that takes the last argument. Let's call this new Proc `e`.
   - Finally, `e[3]` calls the original Proc `b` with arguments `1`, `2`, and `3`, and returns the result `6`.

The `curry` method can also take an optional `arity` argument that specifies the number of arguments. If the curried Proc is called with too many arguments, it ignores the extra ones. If it's called with too few arguments, it returns a new Proc that takes the rest of the arguments.

**<span style='color:#eb3b5a'>EXAMPLE</span>:

```ruby
p b.curry(5)[1, 2][3, 4][5]  #=> 6
```

- `b.curry(5)` turns `b` into a curried version that expects 5 arguments. Although `b` originally only takes 3 arguments, by specifying `5` in `curry(5)`, we're saying that we want to be able to provide up to 5 arguments.

- `b.curry(5)[1, 2]` provides the first two arguments, `1` and `2`. Since there are still missing arguments (we're expecting 5 in total), this returns a new Proc that expects the remaining 3 arguments.

- `b.curry(5)[1, 2][3, 4]` provides the next two arguments, `3` and `4`. Now only one argument is missing, so this returns another new Proc that expects the last argument.

- Finally, `b.curry(5)[1, 2][3, 4][5]` provides the last argument, `5`. Now that we've provided all the arguments, the original Proc `b` is called with the arguments `1`, `2`, `3`. The <span style='color:#3867d6'>arguments</span> `4` <span style='color:#3867d6'>and</span> `5` <span style='color:#3867d6'>are ignored because</span> `b` <span style='color:#3867d6'>only takes</span> 3 <span style='color:#3867d6'>arguments</span>.

Therefore, the result of `b.curry(5)[1, 2][3, 4][5]` is `1 + 2 + 3 = 6`.
