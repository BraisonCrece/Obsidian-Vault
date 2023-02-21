**pluck()** --> recibe como parámetro el atributo de la consulta o colección sobre la cual lo estamos instanciando, devolviendonos una colección con los registros pero solo con el atributo que se le indica.
Ejemplo:
```ruby
Post.where("title LIKE ?", "%Sr%").pluck(:id)
# output => [1, 2, 4, 23, 64, 67, 87]
```

