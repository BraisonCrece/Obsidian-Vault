# Problema query N+1
Ejemplo practico del problema query N+1:

Digamos que un usuario hace un GET /posts, esta request llega a nuestra app y esta hace una query a la base de datos para obtener todos los posts. Digamos que tenemos 10 posts, entonces la query nos devuelve 10 posts. Ahora, como en nuestra aplicación hemos definido un serializer para los posts que nos devuelve información referente al usuario que ha creado el post, 
la aplicación deberá de iterar por cada post de la respuesta y hacer una query extra por cada uno de estos posts... Multiplicando el núnero de queries que se hacen a la BBDD y en consecuencia multiplicando el performance.
Esto no es relevante si tenemos pocos registros, pero convierte la aplicacion es inescalable si tenemos muchos registros.

Pongamos que el tiempo de respuesta para obtener los 10 posts es de 100ms, y el tiempo de respuesta para obtener el usuario que ha creado cada post es de 10ms. Entonces, el tiempo total de respuesta para obtener los 10 posts es de 100ms + 10ms * 10 = 200ms. Si tenemos 1000 posts, el tiempo total de respuesta es de 100ms + 10ms * 1000 = 10.100ms. 

Esto es un problema exponencial y queremos evitarlo. Para esto podemos usar el concepto de eager loading. 

Eager loading es una forma de pre-cargar los datos relacionados con los registros que estamos obteniendo de la base de datos. Esto nos permite evitar el problema N+1. 

Con solo 2 queries podemos obtener los 10 posts y los usuarios que han creado cada uno de estos posts. (1 query para obtener los posts y otra para obtener los Ids de los usuarios que han creado los posts)

Para lograr esto, podemos usar el metodo `includes` de ActiveRecord. Este metodo recibe como argumento un array de símbolos que representan los modelos relacionados con el modelo que estamos obteniendo de la base de datos:
  
  ```ruby
  # app/controllers/posts_controller.rb
  def index
    @posts = Post.includes(:user)
    render json: @posts
  end
  ```

  ## Que es un `CDN`?
Un CDN es un Content Delivery Network, que es un servicio especializado en distribuir assets. 
Se utiliza para servir los assets más rápido a los navegadores.
Estos servicios suelen tener varios servidores distribuidos por todo el mundo para poder servir los assets de un modo ágil a cualquier navegador que lo solicite independientemente de la localización de este.

## Caching
Las búsquedas en BBDD relaccionales no están pensada ni optimizadas para hacer búsquedas con strings.
En bases de datos con muchos registros esto se convierte en un problema, debido al escaso rendimiento.
Para evitar este tipo de problemas en producción, podemo usar catching. Ejemplo:
```ruby
class PostSearchService
	def self.search(curr_post, query)
		# key => value
		posts_id = Rails.cache.fetch("posts_search/# 
                       {query}", expires_in: 1.hours) do
			curr_post.where("title LIKE ?", "%# 
                                      {query}%").pluck(:id)
		end
		curr_post.where(id: posts_id)
	end
end
```
Esto lo que hará, es que en la primera consulta que se haga, se harán dos consultas en el background. Una para traer los datos de la consulta, buscando por el campo que pasamos como string (lo cual es lento), y otra buscando solamente los id's de estos registros que cumplen la query.
De este modo, este arreglo con los id's que cumplan la query, quedará guardado en caché durante 1 hora (porque es lo que se le especificó en el código `expires_in: 1.hours `). Y en este tiempo, si se vuelve a hacer la consulta, la operación de búsqueda será mucho más rápida y solo se hará una query, que será la siguiente:
```sql
SELECT * FROM "posts" WHERE id IN (1,2,4,5,6,45)
```


## La clase Struct en Ruby
Una clase `Struct` es una forma de organizar y acceder a datos de manera similar a una estructura de datos en lenguajes de programación como C.

Cuando se llama a `Struct.new()`, se le pasa una lista de nombres de atributos para la estructura. Luego, se puede crear una nueva instancia de la clase `Struct` pasándole valores para cada uno de esos atributos. Por ejemplo:

```ruby
Person = Struct.new(:name, :age) 
person1 = Person.new("Alice", 30) 
person2 = Person.new("Bob", 35)  
puts person1.name  # imprime "Alice" 
puts person2.age   # imprime 35
```

La clase `Struct` también incluye un conjunto de métodos para acceder y modificar los atributos de la estructura. Por ejemplo, se puede usar el método `[]` para acceder a un atributo por su nombre y el método `[] =` para asignar un valor a un atributo.

```ruby
person1[:name] = "Carol" 
puts person1[:name]  # imprime "Carol"

person2.age = 40 
puts person2.age    # imprime 40
```

Por así decirlo es como una clase que contiene por defecto un constructor con los atributos que le definamos.


## JWT

JWT o Json Web Token es un estándar definido por la el IETF (Internet Engineering Task Force) para transmitir digitalmente información relacionada a autenticación y autorización entre dos partes usando un token (que es simplemente un string) resultado de codificar un objeto JSON con la información que se desea transmitir. Según el estándar, este token fue pensado para transmitir “poca” información y adicionalmente para ser compatible con URLs lo que quiere decir que un JWT puede ser incluido en una petición HTTP o en un link. Esto facilita por ejemplo enviar links que dan acceso a un recurso.

Un JWT se ve así:

![[jwt.jpg]]

Un JWT está dividido en 3 partes separadas por un punto:

-   Header (Encabezado): `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9`
-   Payload (Contenido): `eyJzdWIiOiJ1c2VyMSIsIm5hbWUiOiJQZXBlIFBlcmV6IiwiZW1haWwiOiJwZXBlcGVyZXpAbWFpbGluYXRvci5jb20iLCJhZG1pbiI6dHJ1ZX0`
-   Signature (Firma): `4rkTevJa1WW5OE-JXD8RITsia4XC5jn_6V1ZjAfbrYU`

El Header incluye información sobre cómo está construido el token. En el ejemplo anterior, el header dice “`alg`”: “`HS256`” haciendo referencia al algoritmo que se utiliza para generar la firma o signature.

El Payload incluye la información que se quiere transmitir. En el ejemplo se ve que se hace referencia a un usuario, su nombre, su email y su rol como admin.

Finalmente, la firma o signature, es la parte que se utiliza para verificar que la información contenida en el payload y en el header no ha sido modificada.

Los 2 tipos de token JWT más comunes son:

-   tokens firmados pero no encriptados.
-   tokens firmados y encriptados.

Ambos tipos proveen garantías diferentes. Por un lado, el contenido de los tokens firmados puede ser decodificado por cualquiera, pues no está encriptado. Sin embargo, al contener una firma, esta firma se utiliza por la parte que recibe el token para verificar que el contenido no ha sido modificado. Por otro lado, los token que están encriptados, no pueden ser decodificados a menos de que la parte que reciba el token tenga la llave para poder desencriptarlo.


