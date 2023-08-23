[Proc documentation](https://docs.ruby-lang.org/en/3.0/Proc.html)
A `Proc` object is an encapsulation of a block of code, which can be stored in a local variable, passed to a method or another [`Proc`](https://docs.ruby-lang.org/en/3.0/Proc.html), and can be called. [`Proc`](https://docs.ruby-lang.org/en/3.0/Proc.html) is an essential concept in Ruby and a core of its functional programming features.

```rb
square = Proc.new {|x| x**2 }

square.call(3)  #=> 9
# shorthands:
square.(3)      #=> 9
square[3]       #=> 9
```

[`Proc`](https://docs.ruby-lang.org/en/3.0/Proc.html) objects are _closures_, meaning they remember and can use the entire context in which they were created.

```rb
def gen_times(factor)
  # remembers the value of factor at the moment of creation
    Proc.new {|n| n*factor } 
end

times3 = gen_times(3)
times5 = gen_times(5)

times3.call(12)               #=> 36
times5.call(5)                #=> 25
times3.call(times5.call(4))   #=> 60
```


## Proc & Lambda differences:

**Procs and Lambdas**: These are two types of <span style='color:#eb3b5a'>closure objects</span> in Ruby. They are similar, but have some key differences.

1. **Differences in argument handling**:

   - **Procs**: They don't complain about the number of arguments. If an argument is missing, `nil` is used.
     ```ruby
     p = proc {|x, y| "x=#{x}, y=#{y}" }
     p.call(1)  #=> "x=1, y=", nil substituted instead of error
     ```

   - **Lambdas**: They check the number of arguments. If they don't match, an error is thrown.
     ```ruby
     l = lambda {|x, y| "x=#{x}, y=#{y}" }
     l.call(1)  # ArgumentError: wrong number of arguments (given 1, expected 2)
     ```

2. **Differences in the use of `return`, `break`, and `next`**:

   - **Procs**:
     - `return`: Exits the current method, not just the proc.
     - `break`: Exits the nearest block in a method, can cause an error if not in a block.
     - `next`: Moves to the next iteration of the nearest block.

   - **Lambdas**:
     - `return`: Only exits the lambda, not the method containing it.
     - `break`: Only exits the lambda, not the method containing it.
     - `next`: Moves to the next iteration of the nearest block.

   Here's an example of how `return` works differently in procs and lambdas:

   ```ruby
   def test_return
     -> { return 3 }.call      # Just returns from lambda, method continues
     proc { return 4 }.call    # Returns from entire method
     return 5
   end

   test_return # => 4, because the proc caused the entire method to return
   ```


## Conversion of other objects to procs

1. **Conversion to Procs**: Any object that implements the `to_proc` method can be converted into a proc using the `&` operator. This is useful because it allows these objects to be used by iterators, like `map` or `each`.

2. **Custom `to_proc` method**: You can define a `to_proc` method in your own classes. This method should return a proc that does something useful with the object. Here's an example:

   ```ruby
   class Greeter
     def initialize(greeting)
       @greeting = greeting
     end

     def to_proc
       proc {|name| "#{@greeting}, #{name}!" }
     end
   end

   hi = Greeter.new("Hi")
   ["Bob", "Jane"].map(&hi)    #=> ["Hi, Bob!", "Hi, Jane!"]
   ```

   In this example, the `Greeter` class has a `to_proc` method that returns a proc. This proc takes a name and returns a greeting for that name. The `&` operator is used to convert the `Greeter` object to a proc, which is then passed to the `map` method.

3. **Core Ruby classes with `to_proc`**: Some core Ruby classes, like `Symbol`, `Method`, and `Hash`, have their own `to_proc` methods.

   - `Symbol#to_proc`: Converts the symbol to a proc that calls the method with the same name as the symbol on its argument.
     ```ruby
     :to_s.to_proc.call(1)           #=> "1"
     [1, 2].map(&:to_s)              #=> ["1", "2"]
     ```

   - `Method#to_proc`: Converts the method to a proc that calls the method on its argument.
     ```ruby
     method(:puts).to_proc.call(1)   # prints 1
     [1, 2].each(&method(:puts))     # prints 1, 2
     ```

   - `Hash#to_proc`: Converts the hash to a proc that looks up its argument in the hash.
     ```ruby
     {test: 1}.to_proc.call(:test)       #=> 1
     %i[test many keys].map(&{test: 1})  #=> [1, nil, nil]
     ```



## Orphaned Proc

1. **Orphaned Proc**: A proc is considered "orphaned" when it survives beyond the method in which it was created. This can cause issues with `return` and `break`.

   ```ruby
   def create_proc
     proc { return "Proc return" }
   end

   def test_proc
     my_proc = create_proc
     my_proc.call
     "End of test_proc"
   end

   puts test_proc
   # This will give an error: unexpected return (LocalJumpError)
   ```

   In this example, `create_proc` creates a proc that contains a `return`. When `test_proc` tries to call this proc, a `LocalJumpError` occurs because the proc is trying to return to `create_proc`, but `create_proc` has already finished.

2. **Differences between `return` and `break`**: `return` and `break` behave differently in an orphaned proc.

   ```ruby
   def create_proc_with_break
     proc { break "Proc break" }
   end

   def test_proc_with_break
     my_proc = create_proc_with_break
     my_proc.call
     "End of test_proc_with_break"
   end

   puts test_proc_with_break
   # This will also give an error: break from proc-closure (LocalJumpError)
   ```

   In this example, `create_proc_with_break` creates a proc that contains a `break`. When `test_proc_with_break` tries to call this proc, a `LocalJumpError` occurs because the proc is trying to `break` out of any iteration, as it's not within an iteration block.

3. **Lambdas**: Unlike procs, <span style='color:#eb3b5a'>lambdas cannot be orphaned</span>. This is because `return` and `break` in a lambda only exit the lambda, not the method containing it.

   ```ruby
   def create_lambda
     lambda { return "Lambda return" }
   end

   def test_lambda
     my_lambda = create_lambda
     puts my_lambda.call
     "End of test_lambda"
   end

   puts test_lambda
   # This will print:
   # Lambda return
   # End of test_lambda
   ```

   In this example, `create_lambda` creates a lambda that contains a `return`. When `test_lambda` calls this lambda, there's no error. The lambda simply returns "Lambda return", and `test_lambda` continues to the end.



## Numbered parameters

1. **Numbered Parameters**: These are a way to refer to the parameters of a block without having to explicitly name them. You can use `_1` to refer to the first parameter, `_2` for the second, and so on.

   For example, if you have an array of numbers and you want to print each number, you could do it like this:

   ```ruby
   [1, 2, 3].each { |num| puts num }  # Traditional way 
									  # (explicit)
   
   [1, 2, 3].each { puts _1 }         # Using numbered parameters
									  # (implicit)
   ```

   In both cases, the numbers 1, 2, and 3 will be printed.

2. **Multiple Parameters**: If your block takes more than one parameter, you can use `_1`, `_2`, etc., to refer to each one.

   For example, if you have two arrays and you want to add the corresponding elements, you could do it like this:

   ```ruby
   [1, 2, 3].zip([4, 5, 6]).map { |a, b| a + b }  # Traditional way
   [1, 2, 3].zip([4, 5, 6]).map { _1 + _2 }        # Using numbered parameters
   ```

   In both cases, you will get the array `[5, 7, 9]`.

3. <span style='color:#eb3b5a'>Restrictions</span>: There are some rules you need to follow when using numbered parameters.

   - You can't use numbered parameters and named parameters in the same block. For example, this will give an error:

     ```ruby
     [1, 2, 3].map { |num| _1 * 2 }  # SyntaxError
     ```

   - You can't use the same parameter number in nested blocks. For example, this will give an error:

     ```ruby
     [1, 2, 3].each { [4, 5, 6].each { p _1 } }  # SyntaxError
     ```


