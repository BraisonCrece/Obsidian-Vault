# Que están haciendo los specs por ti?
- Generan <span style='color:#2d98da'>confianza</span>
- Eliminan el miedo
- Habilitan una <span style='color:#2d98da'>buena refactorización</span>
- Guían en el <span style='color:#2d98da'>diseño</span>
- Aumenta la <span style='color:#2d98da'>sostenibilidad del código</span>
- Actúan como <span style='color:#2d98da'>documentación</span>
- Transforman tu flujo de trabajo
- Son <span style='color:#2d98da'>divertidos</span>!

## Para definir un bloque de ejemplos usamos <span style='color:#fa8231'>Rspec</span>.<span style='color:#3867d6'>describe</span>:
```rb
Rspec.describe 'Lo que sea que se testee en el bloque' do
	# bloque de codigo de test
end
```
Un bloque de ejemplo define que es lo que estamos testeando, y mantiene *specs* relaccionados juntos.

## <span style='color:#20bf6b'>Test</span> vs <span style='color:#f7b731'>Spec</span> vs <span style='color:#8854d0'>Example</span>
Cual es la diferencia entre test spec y example? 
Los tres se refieren a codigo escrito para testear el comportamiento de un programa. 
Los tres son semi-intercambiables, pero cada uno de ellos carga un enfasis diferente:
• Un <span style='color:#20bf6b'>test</span> valida que un trozo de codigo funcione correctamente.
• Un <span style='color:#f7b731'>spec</span> describe el comportamimento deseado de un trozo de codigo.
• Un <span style='color:#8854d0'>ejemplo</span> describe como se pretende que sea usada una API.

## <span style='color:#eb3b5a'>it</span>
Crea un ejemplo, osea, un **test individual**

## <span style='color:#eb3b5a'>expect</span>
Es como un **assertion**, verifica que la salida es la esperada.

## Como evitar la duplicidad de código cuando dos o más test comparten *setup*
Tenemos varia alternativas:
• Los **<span style='color:#2d98da'>Hooks</span>** RSpec se ejecutan automáticamente en momentos específicos durante la prueba.
• Los **<span style='color:#3867d6'>helpers</span>** son métodos regulares de Ruby; usted controla cuándo se ejecutan.
• La construcción **<span style='color:#4b6584'>let</span>** de RSpec inicializa los datos a pedido.

## **<span style='color:#eb3b5a'>Hooks</span>**
before, para definir un bloque de código que se ejecutará antes del bloque de tests, por ejemplo:
```rb
before { @sandwitch = Sandwitch.new('delicious', []) }
```

RSpec mirará antes de ejecutar un bloque de tests si hay algún hook **before**, y lo ejecutará previamente. da igual en que lugar se encuentre, será ejecutado **antes**

## <span style='color:#eb3b5a'>Helper methods</span>
Los RSpec.describe son clases de Ruby, por lo tanto podemos definir métodos dentro de estas. En este caso, los usaremos como helper methods:
```rb
def sandwitch
	@sandwitch ||= Sandwitch.new('delicious',[])
end
```

Esto generará el mismo efecto que el hook anterior, teniendo disponible una variable de instancia en cada test (utilizando memoización, para que dentro de un mismo test, solo se instancie una vez, pues siempre se llama al helper en lugar de a la variable)

## <span style='color:#eb3b5a'>let</span>
Let nos permite definir variables que guardan el resultado del codigo que se le pasa como bloque:
```rb
let(:sandwitch) { Sandwitch.new('delicious',[]) }
```

Esta línea hará los mismo que los dos ejemplos anteriores, pero de un modo menos verboso y elegante.

## <span style='color:#eb3b5a'>context()</span>
Es un alias de **describe**, se suele utilizar cuando el quid del test modifica lo que estamos testeando, por ejemplo:
```rb
RSpec.describe "A cup of coffee" do
	let(:coffee) { Coffee.new }
	
	it "costs $1" do
		expect(coffee.price).to eq(1.00)
	end

	context "with milk" do
		before { coffee.add :milk }
		it "costs $1.25" do
			expect(coffee.price).to eq(1.25)
		end
	end
end
```
En este caso, la prueba en el context "with milk", modifica el test principal "A cup of coffee" . Podríamos intercambiar context por describe, pero sintácticamente le pega más decir que "con leche" es el contexto del test a la taza de café.

## <span style='color:#eb3b5a'>--profile</span>
Es una bandera de opciones que podemos pasarle al comando rspec. Al hacerlo definiendole como parámetro un número, nos mostrará los n test más lentos, por ejemplo:

```shell
brais@braisoncrece:~/Documentos/entrenando_RSPEC/spec$ rspec . --profile 2
.........

Top 2 slowest examples (0.903 seconds, 59.0% of total time):
  The sleep() method can sleep for 0.5 second
    0.50138 seconds ./coffee_spec.rb:37
  The sleep() method can sleep for 0.4 second
    0.40163 seconds ./coffee_spec.rb:36

Top 2 slowest example groups:
  The sleep() method
    0.30168 seconds average (1.51 seconds / 5 examples) ./coffee_spec.rb:32
  An ideal sandwitch
    0.00473 seconds average (0.00946 seconds / 2 examples) ./sandwitch_spec.rb:3

Finished in 1.53 seconds (files took 0.0676 seconds to load)
9 examples, 0 failures
```

## <span style='color:#eb3b5a'>--example</span> o <span style='color:#eb3b5a'>-e</span>
Nos permite ejecutar solamente un conjunto de test alojados bajo un mismo ***example***, además podemos pasar una de las palabras que coinciden con el nombre del example y RSpec lo buscará y ejecutará todos los examples que coincidan. (case sensitive)

## <span style='color:#eb3b5a'>Comando para abrir el último test que falló</span>
Por rapided, RSpec nos facilita el comando exacto que deberíamos escribir en la terminal para ejecutar el test que falle:
![[Pasted image 20230203003037.png]]

## <span style='color:#eb3b5a'>--only-failures</span>
Esta bandera, como su nombre indica, hará que se ejecuten solo los test que fallaron.
Para poder acceder a esta opción, necesitamos habilitar un fichero txt para que RSpec persista los datos del resultado de los test y pueda en la siguiente iteración ejecutar solamente los que fallaron.
Para ello debemos colocar el siguiente código en el archivo a ejecutar ( o en un archivo de configuración)
```rb
RSpec.configure do |config|
	config.example_status_persistence_file_path = 'spec/examples.txt'
end
```

## <span style='color:#eb3b5a'>Configurar RSpec para que acepte hacer focus a uno o varios test añadiendo 'f' delante de 'context' 'it' o 'describe'</span>
Para configurarlo deberemos añadir esta opcion en el archivo de configuracion:
```rb
RSpec.configure do |config|
	config.filter_run_when_matching :focus
end
```

Todos estos filtros, al igual que cuando ejecutamos el ultimo en fallar o solo los que fallaron, podemos aplicarlos a la hora de ejecutar RSpec pasándole en la cmd la bandera de opciones `--tag` y el filtro especificado. Por ejemplo
```bash
rspec coffee_spec.rb --tag last_run_status:failed
```

## <span style='color:#eb3b5a'>Como marcamos tests como pendientes?</span>
Podemos utilizar la palabra `pending` delante del propio test, o bien dejar its sin bloque de código. Es decir, definimos en primera instancia los requisitos (lo que esperamos que cumpla la aplicación), y posteriormente vamos avanzando en el proyecto cubriendo y pasando tests. Además podemos pasar un string al método `pending` con el que describiremos el por qué está pendiente el test.

## <span style='color:#eb3b5a'>Podemos marcar un test en concreto para que se salte con <span style='color:#3867d6'>skip</span> en vez de <span style='color:#2d98da'>pending</span> o añadiendo una <span style='color:#8854d0'>x </span>delante de 'context' 'it' o 'describe'</span>

## <span style='color:#eb3b5a'>Los tres tipos principales de test</span>:
• <span style='color:#2d98da'>Aceptación</span>: ¿Funciona todo el sistema?
• <span style='color:#2d98da'>Unidad</span>: ¿Nuestros objetos hacen lo correcto, son convenientes para trabajar con ellos?
• <span style='color:#2d98da'>Integración</span>: ¿Nuestro código funciona contra el código que no podemos cambiar?

### <span style='color:#4b6584'>Specs de aceptacion</span>:
Los specs de aceptación describen una característica en un estilo de caja negra de extremo a extremo que ejercita todo el sistema. Estos tests son difíciles de escribir, comparativamente frágiles y lentos. Pero también brindan una gran confianza de que las partes del sistema están trabajando juntas como un todo. También son sumamente útiles para la refactorización a gran escala.

### <span style='color:#4b6584'>Specs de unitarios</span>:
En el otro extremo del espectro, los specs unitarios se centran en unidades de código individuales, a menudo tan pequeñas como un único objeto o método. Comprueban el comportamiento de un fragmento de código en relación con el entorno que se construye para él. 

### <span style='color:#4b6584'>Specs de integración</span>
Los specs de integración se encuentran en algún lugar entre estos dos extremos. El código que interactúa con un servicio externo, como una base de datos o una API REST de terceros, debe tener specs de integración.  
Cualquier proyecto de software no trivial dependerá de otras bibliotecas. Una interpretación estricta de estas definiciones requeriría que separe su unidad y las especificaciones de integración de la siguiente manera: 
• Los specs de su unidad tendrían que aislar su código de cualquier dependencia de terceros. 
• Se permitiría que sus specs de integración (comparativamente lentos) accedan indirectamente al código de terceros.


## <span style='color:#eb3b5a'>Mocks, Stubs, Fakes y Spies</span>
los términos "mocks", "stubs", "fakes" y "spies" se refieren a objetos que se utilizan en pruebas unitarias para simular el comportamiento de otros objetos.

-   Un "<span style='color:#3867d6'>mock</span>" es un objeto simulado que se utiliza para verificar el comportamiento de un objeto en una prueba unitaria. Los mocks se utilizan para asegurarse de que un objeto llama a los métodos correctos de otro objeto en la forma esperada.

-   Un "<span style='color:#3867d6'>stub</span>" es un objeto simulado que se utiliza para proporcionar datos predefinidos a un objeto durante una prueba unitaria. Los stubs se utilizan para simular los datos que un objeto normalmente recibiría de otro objeto o de una base de datos.

-   Un "<span style='color:#3867d6'>fake</span>" es un objeto simulado que se utiliza para reemplazar un objeto real en una prueba unitaria. Los fakes se utilizan cuando un objeto real es difícil de utilizar o requiere recursos costosos (como una base de datos real).

-   Un "<span style='color:#3867d6'>spy</span>" es un objeto simulado que se utiliza para verificar el comportamiento de un objeto en una prueba unitaria. A diferencia de los mocks, los spies no se utilizan para asegurarse de que un objeto llama a los métodos correctos de otro objeto, sino para verificar que el objeto ha realizado ciertas acciones durante la prueba.

Estos objetos simulados se utilizan comúnmente en pruebas unitarias para aislar una parte específica del código y probarla de forma aislada. Al proporcionar estos objetos simulados, se pueden controlar los datos y el comportamiento que se utilizan durante la prueba, lo que hace que sea más fácil identificar y solucionar errores en el código.

## <span style='color:#eb3b5a'>Que es <span style='color:#3867d6'>rack/test</span></span>
"Rack/Test" es una biblioteca de pruebas unitarias para Ruby que se utiliza para probar aplicaciones web construidas en la arquitectura Rack, que es un estándar común para construir aplicaciones web en Ruby.

Rack/Test proporciona una interfaz de prueba para simular solicitudes <span style='color:#8854d0'>HTTP</span> y respuestas HTTP, lo que permite a los desarrolladores probar sus aplicaciones web de forma automatizada. Esto permite a los desarrolladores asegurarse de que su aplicación web responde de la manera correcta a diferentes solicitudes HTTP, como GET, POST, PUT, DELETE, etc.

## <span style='color:#eb3b5a'>Haz pasar tus tests primero, luego refactoriza</span>
Es tentador comenzar a refactorizar mientras escribes tus specs. Evita esa tentación:
Consigue que tus tests pasen primero, y luego refactoriza. De este modo, podrás usar tus specs para comprobar que la refactorización fué exitosa.

# Métodos de la librería de <span style='color:#20bf6b'>aserciones</span> de RSpec:
-> **<span style='color:#8854d0'>A hash including</span>** 
Devuelde true si el hash incluye esos datos.
```rb
a_hash_including(:some => 'some', :data => 'data') 
```


-> **<span style='color:#8854d0'>Contain exactly</span>**
Supongamos que tenemos una colección de frutas y queremos probar que la colección contiene exactamente las frutas "manzana", "plátano" y "naranja", sin importar su orden. Podríamos hacer lo siguiente.
```rb
frutas = ["manzana", "naranja", "plátano"]
expect(frutas).to contain_exactly("manzana", "plátano", "naranja")
```
 En este caso devolverá <span style='color:#3867d6'>true</span>

# Métodos de comparación (matchers):
lista de algunos de los matchers más comunes y útiles en RSpec para Ruby:

-   **<span style='color:#fa8231'>eq</span>**: Compara dos objetos por igualdad (`==`).
-   **<span style='color:#fa8231'>be</span>**: Compara dos objetos por identidad (`equal?`).
-   **<span style='color:#fa8231'>be_within</span>**: Compara dos números para asegurarse de que difieren en una cantidad dentro de un margen.
-   **<span style='color:#fa8231'>be_true</span>**, **<span style='color:#fa8231'>be_false</span>**: Compara un valor booleano a true o false.
-   **<span style='color:#fa8231'>be_nil</span>**: Compara un valor a nil.
-   **<span style='color:#fa8231'>match</span>**: Compara una cadena de texto con una expresión regular.
-   **<span style='color:#fa8231'>include</span>**: Verifica si una colección incluye un elemento determinado.
-   **<span style='color:#fa8231'>respond_to</span>**: Verifica si un objeto responde a un determinado método.
-   **<span style='color:#fa8231'>have_attributes</span>**: Verifica si un objeto tiene atributos específicos.
-   **<span style='color:#fa8231'>have_key</span>**: Verifica si un hash (diccionario) tiene una clave específica.
-   **<span style='color:#fa8231'>raise_error</span>**: Verifica si se ha lanzado una excepción específica.