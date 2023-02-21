![[ruby 1.svg|15]] Ruby 

## <span style='color:#2d98da'><span style='color:#eb3b5a'>rake</span> la utilidad de Ruby para manejar tareas</span>
Su nombre viene de "Ruby make". 
Rake, lee y ejecuta tareas definidas en un archivo llamado "Rakefile".
Un ejemplo de `rakefile`:
```ruby
# Definir un `namespace` no es necesario
# es como el nombre de la clase que contiene el método
# "task", pero podemos no incluirlo
namespace :admin do
  # descripción
  desc 'Borrado interactivo de los ficheros en /tmp'
    # definición de la tarea (como una función)
    task :clean_tmp do
      Dir['/tmp/*'].each fo |file|
        next unless File.file?(file)
        print "Delete #{file}? "
        answer = $stdin.gets
        case answer
        when /^y/
          File.unlink(file)
        when /^q/
          break
        end
      end
    end
  end
end	
```

Luego para ejecutar la acción `:clean_tmp` usaremos el siguiente comando:
```bash
rake admin:clean_tmp
```

Como comento en el código, no es necesario definir `namespace` pero en caso de acerlo, podemos incluso definir una jerarquía:

```ruby
namespace :admin do
	namespace :clean do
		task :tmp do
			# etc.
		end
	end
end
```

En este caso para llamar al `task` :tmp, usaremos el siguiente comando:

```bash
rake admin:clean:tmp
```


## <span style='color:#2d98da'>Todo en Ruby son <span style='color:#eb3b5a'>objetos</span>, incluídas las clases!</span>
Todos los objetos creados en Ruby "nacen" con ciertas habilidades innatas. Ciertos métodos que ellos saben como ejecutar porque son objetos Ruby 😃.
Para ver que métodos trae por defecto todo objeto que es instanciado en Ruby podemos usar esta sintaxis:
```ruby
p Object.new.methods.sort
#=> [:!, :!=, :!~, :<=>, :==, :===, :=~, :__id__, :__send__, :class, :clone, :define_singleton_method, :display, :dup, :enum_for, :eql?, :equal?, :extend, :freeze, :frozen?, :hash, :inspect, :instance_eval, :instance_exec, :instance_of?, :instance_variable_defined?, :instance_variable_get, :instance_variable_set, :instance_variables, :is_a?, :itself, :kind_of?, :method, :methods, :nil?, :object_id, :pp, :private_methods, :protected_methods, :public_method, :public_methods, :public_send, :remove_instance_variable, :respond_to?, :send, :singleton_class, :singleton_method, :singleton_methods, :taint, :tainted?, :tap, :to_enum, :to_s, :trust, :untaint, :untrust, :untrusted?, :yield_self]
```


## <span style='color:#2d98da'>Podemos obviar la keyword <span style='color:#eb3b5a'>return</span> cuando queremos devolver un resultado de un método</span>

Por ejemplo:
```ruby
obj = Object.new

def obj.test(a)
	a
end

=> obj.test(12)
# output --> 12
```

Pero si tenemos que devolver <span style='color:#fa8231'>varios resultados</span> a la vez, si o si tendremo que utilizarlo:
```ruby
def obj.multiple(a, b, c)
	return a, b, c
end

=> obj.multiple(1,2,3)
# output --> [1,2,3]
```


## <span style='color:#2d98da'>Podemos <span style='color:#eb3b5a'>formatear</span> un float, para que se impriman cierto numero de decimales</span>:
```ruby
price = 12.356
puts "%.2f" % price
# output => 12.35
puts "%.1f" % price
# output => 12.3
puts "%.0f" % price
# output => 12
puts "%i" % price
# output => 12
```


## <span style='color:#2d98da'><span style='color:#eb3b5a'>Truthy</span> y <span style='color:#eb3b5a'>Falsy</span> en Ruby</span>:

En Ruby solo es evaluado a "false":
- <span style='color:#eb3b5a'>false</span>
- <span style='color:#fa8231'>nil</span>

El resto, TODO es evaluado a "true":
- Cualquier número, incluído el <span style='color:#eb3b5a'>0</span>
- Cualquier String, incluída la string vacía <span style='color:#eb3b5a'>""</span>
- Cualquier objeto
- <span style='color:#3867d6'>true</span>


## <span style='color:#2d98da'>Diferencia entre los métodos <span style='color:#eb3b5a'>dup</span> y <span style='color:#eb3b5a'>clone</span></span>:
La diferencia principal entre el método `dup` y el método `clone` en Ruby es que <span style='color:#fa8231'>dup</span> realiza una <span style='color:#fa8231'>copia superficial</span> del objeto receptor, mientras que <span style='color:#eb3b5a'>clone</span> realiza una <span style='color:#eb3b5a'>copia profunda</span>. 

Esto significa que, al utilizar `dup`, se crea una nueva copia del objeto con una nueva identidad en memoria, pero <span style='color:#20bf6b'>compartiendo </span>los mismos atributos y valores que el objeto original. 

Sin embargo, al utilizar `clone`, se crea una nueva copia del objeto con una nueva identidad en memoria y con <span style='color:#fa8231'>copias </span>de todos los atributos y valores del objeto original.

Ejemplo
```ruby
class Person 
	attr_accessor :name, :age, :address 
	
	def initialize(name, age, address) 
		@name = name 
		@age = age 
		@address = address 
	end 
end 

class Address 
	attr_accessor :street, :city, :state 
	def initialize(street, city, state)
		@street = street 
		@city = city 
		@state = state 
	end
end 
# Creamos un objeto de la clase Person y un objeto de la clase Address 
person1 = Person.new("Alice", 30, Address.new("1st Street", "New York", "NY")) 

# Copia superficial con DUP
person2 = person1.dup 

# Modificamos el atributo street del objeto address de person1 
person1.address.street = "2nd Street" 

# Mostramos los valores de los atributos street de ambos objetos 
puts person1.address.street # => "2nd Street" 
puts person2.address.street # => "2nd Street" 
# como podemos ver, el atributo :address de la copia, se define con un puntero al objeto address que define la dirección del objeto 2.

# Copia profunda con CLONE
person1 person3 = person1.clone 

# Modificamos el atributo street del objeto address de person1 
person1.address.street = "3rd Street" 
# Mostramos los valores de los atributos street de ambos objetos 
puts person1.address.street # => "3rd Street" 
puts person3.address.street # => "2nd Street"
```


## <span style='color:#eb3b5a'>respond_to?</span>(<span style='color:#20bf6b'>"method_name"</span>)           <span style='color:#2d98da'>Método para comprobar si un objeto tiene un determinado método disponible</span>.

Nos devolverá true o false dependiendo de si el objeto "conoce" ese método. por ejemplo:

```rb
obj = Object.new

if obj.respond_to("walk")
	obj.walk
else
	p "Sorry this object doesn't know how to walk"
end
```

Este método tiene este nombre tan curioso porque los objetos se comunican. Reciben y emiten mensajes, muchas veces a través del operador "*<span style='color:#3867d6'>.</span>*", por lo tanto podemos preguntar si vamos a recibir respuesta por parte de ellos antes de nada.


## <span style='color:#2d98da'>Método <span style='color:#eb3b5a'>send</span>(<span style='color:#20bf6b'>"name_method"</span>), nos permite enviar "mensajes" osea, métodos que todavía no sabemos cual va a ser. Por ejemplo</span>:

```rb
# Imaginémonos que la clase ticket 
# tiene diferentes métodos disponibles:
# - asiento
# - fecha
# - fila
request = gets.chomp
if ticket.respond_to?(request)
	# dado que la variable request
	# puede tomar cualquiera de estos valores
	# no podemos enviar la solicitud
	# del método así |ticket.asiento| porque
	# no sabemos cual de los métodos va a ser
	ticket.send(request)
else
	p "No such information avaliable"

# podríamos hacer una lista de condicionales
# pero así nos ahorramos trabajo
```

En Ruby también existe el método `__send__` y el `public_send`. Estos métodos nacieron para evitar conflictos con otros lenguajes de programación, es decir para diferenciar.
Son muy parecidos, aunque con algunas diferencias sutiles, como que `public_send` no se puede usar para llamar a metodos privados de las clases.

La mayoría de la veces vamos a utilizar el operador punto para enviar mensajes, pero es bueno saber que existe y su utilidad.


## <span style='color:#2d98da'>Operador estrella <span style='color:#eb3b5a'>*</span> para pasar <span style='color:#eb3b5a'>n</span> argumentos a un método</span>
Cuando no sabemos cuantos argumentos va a recibir un método usaremos la siguiente sintaxis:

```rb
def two_or_more(a, b, *c)
	puts "I require at least two, but also more!"
	puts "This is what I recieved: "
	p a, b, c
end

# irb => two_or_more(1, 2, 3, 4, 5, 6, 7, 8)
#
# => I require at least two, but also more!
#    This is what I received: 
#    1, 2, [3, 4, 5, 6, 7, 8]
```

## <span style='color:#2d98da'> Búsquedas de texto avanzadas y eficientes gracias a <span style='color:#eb3b5a'>pg_search</span></span>
**<span style='color:#fa8231'>:tsearch</span>** --> es un módulo de PostgreSQL que permite realizar búsquedas de texto completo con alta eficiencia en grandes bases de datos de texto.

Ejemplo de uso:
```rb
class Article < ApplicationRecord   
	include PgSearch::Model   
	pg_search_scope :search_by_title_and_content,
		against: [:title, :content],
		using: {tsearch: {any_word: true}} 
end
```

**<span style='color:#fa8231'>:trigram</span>** --> es un módulo de PostgreSQL que permite realizar **búsquedas de texto completo** basadas en <span style='color:#3867d6'>trigramas</span>, que son fragmentos de texto de tres caracteres.

Ejemplo de uso:
```rb
class Article < ApplicationRecord   
	include PgSearch::Model
	pg_search_scope :search_by_title_and_content,
		against: [:title, :content],
		using: {trigram: {threshold: 0.3}} 
end
```

Un trigrama es un fragmento de texto de tres caracteres que se utiliza para identificar patrones en una cadena de texto. Por ejemplo, supongamos que tenemos la frase "ruby on rails". Los trigramas de esta frase serían "rub", "uby", "by ", "y o", " on", "on ", "n r", " ra", "rai", "ail", "ils".

Estos trigramas se utilizan en la búsqueda de texto basada en trigramas para encontrar coincidencias aproximadas en el texto, lo que puede ser útil para la búsqueda de texto completo en grandes bases de datos de texto.

**<span style='color:#fa8231'>:dmetaphone</span>** --> es una implementación de **Double Metaphone** en PostgreSQL, que permite realizar <span style='color:#3867d6'>búsquedas fonéticas</span> en lugar de búsquedas basadas en texto.

Ejemplo de uso:

```rb
class Article < ApplicationRecord   
	include PgSearch::Model
	pg_search_scope :search_by_title_and_content,
	against: [:title, :content],
	using: {dmetaphone: {any_word: true}} 
end
```

# Como definimos un método `equals()` dentro de una clase?
```rb
def ===(other_object)
	self.age == other_object.age
end
```

# Que es <span style='color:#fa8231'>yield</span> ??
Yield es como una varialbe sobre la cual se volcará el código que se le pase a un método contenido dentro de un bloque. por ejemplo:
```rb
def my_loop
	yield while true
end

my_loop { puts "This is a code block" }
```
El ejemplo anterior ejecutará un bucle infinito imprimiendo en consola <span style='color:#20bf6b'>"This is a code block"</span>

# Que es la inyección de dependencias?
La dependencia de inyección (o "Inversion of Control" en inglés) en Ruby es un patrón de diseño que permite la creación de aplicaciones más flexibles y fáciles de mantener. En resumen, se trata de una técnica que se utiliza para "inyectar" (suministrar) dependencias a una clase en lugar de que la clase tenga que crearlas por sí misma.

En lugar de que una clase cree directamente todas las dependencias que necesita, la dependencia de inyección permite que la clase reciba esas dependencias como parámetros de su constructor o de sus métodos. Esto hace que la clase sea más flexible y fácil de probar, ya que las dependencias pueden ser fácilmente sustituidas por objetos falsos (conocidos como "mocks" o "stubs") durante las pruebas.

Un ejemplo sencillo sería el siguiente:
```rb 
class Alarma   
	def initialize(tono)
		@tono = tono
	end
	
	def sonar
		puts @tono
	end 
end  

tono = "Ring, ring!" 
alarma = Alarma.new(tono) 
alarma.sonar
```
En este ejemplo, la clase `Alarma` toma una dependencia (`tono`) en su constructor. En lugar de que `Alarma` tenga que crear su propio tono, se le proporciona uno desde fuera de la clase. Esto hace que sea más fácil cambiar el tono de la alarma, y también hace que la clase sea más fácil de probar, ya que se puede proporcionar un tono falso durante las pruebas.

La dependencia de inyección es un patrón muy útil en Ruby y en muchos otros lenguajes de programación. Puede ayudar a mejorar la calidad del código y hacer que sea más fácil de mantener y de probar.