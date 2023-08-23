```rb
require 'dry-initializer'

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

Los `param` no pueden ser nombrados, es decir, no podemos referirnos a ellos por su nombre al crear el objeto:
```rb
User.new name: "manu"
(eval):2:in `__dry_initializer_initialize__': wrong number of arguments (given 0, expected 1+) (ArgumentError)
```

```rb
b = User.new "manu"
#<User:0x00007f38f8df2bd0
```

Los `option` por el contrario, deben de ser nombrados explícitamente.

