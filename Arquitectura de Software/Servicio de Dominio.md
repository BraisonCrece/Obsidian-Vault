---
sticker: 1f9f1
---
Los servicios de domino representan una <span style='color:#eb3b5a'>agrupación de lógica de negocio que podremos reutilizar desde múltiples Servicios de Aplicación</span>.

Vamos a poner un ejemplo para poder explicarnos mejor. Tenemos dos casos de uso en nuestra aplicación:

- Obtener un vídeo en base a su identificador
- Modificar el título de un determinado vídeo

En ambos casos de uso, necesitaremos la lógica de negocio para:

- Ir al repositorio de vídeos a buscar un vídeo determinado en base a su identificador
- Lanzar una excepción de dominio tipo VideoNotFound en caso de no encontrar el vídeo. Importante destacar que quien lanza esta excepción, como comentamos en el vídeo, no es la implementación del repositorio.
- Retornar el vídeo en caso de encontrarlo

Para evitar duplicar esta lógica de negocio en los 2 Servicios de Aplicación lo que solemos hacer es extraerla a un Servicio de Dominio que invocaremos desde ambos casos de uso.

Es importante destacar que los servicios de dominio **en ningún caso publicarán los eventos de dominio** que se puedan producir ni gestinonarán transacciones. Eso se lo dejamos al Application Service que nos invoca para evitar duplicidades ya que es realmente él quién establece la “atomicidad” del caso de uso.

# Diferencias entre usar Servicios de Dominio y de Infraestructura desde Aplicación

## Servicio de Aplicación -> Servicio de Infraestructura

Cuando interaccionamos con un servicio de infraestructura desde uno de aplicación, lo que hacemos como vimos en la lección anterior es hacer uso del Principio de Inversión de Dependencias.

Es decir, **el Servicio de Aplicación recibe por constructor el colaborador de infraestructura** que, para evitar acoplamiento, lo hace a través de una interface de dominio.

Esto es así para **aislarnos de los posibles cambios por terceros** en esas implementaciones, y poder **falsear esos colaboradores a la hora de ejecutar nuestros tests** (más información sobre esto en el [curso de testing](https://pro.codely.tv/library/testing-introduccion-y-buenas-practicas/90916/about/)). En test, en vez de inyectar el repositorio de MySQL, inyectaremos uno en memoria para que se ejecuten más rápido (por ejemplo).

## Servicio de Aplicación -> Servicio de Dominio

A la hora de invocar un Servicio de Dominio desde uno de Aplicación, nos encontramos una situación diferente y por lo tanto trataremos este caso de forma distinta.

Los servicios de dominio, por definición, sólo contendrán lógica de negocio. Con lo cuál, **no necesitaremos desacoplarnos de ellos** como sí necesitábamos desacoplarnos de los servicios de infraestructura.

Además, **al no tocar entrada/salida, tampoco nos interesará inyectar una implementación diferente** de nuestro servicio de dominio durante la ejecución de nuestros tests.

Es más, nos interesará que nuestros tests pasen por el servicio de dominio a la hora de testear el caso de uso para así poder cubrirlo de forma indirecta.

Con lo cuál, los servicios de dominio **no tendrán una interface por encima** ya que es totalmente innecesaria. No aportaría más que complejidad a través del nivel de indirección adicional que suponen en nuestro sistema.

Algo que es discutible es si la lógica de instanciación de ese Servicio de Dominio le pertenece al Servicio de Aplicación tal y como mostramos en el vídeo o, a pesar de no tener una interface por encima, lo inyectamos ya instanciado al Servicio de Aplicación. Esto lo dejamos al gusto del consumidor ya que cada alternativa tiene sus pros y sus contras como comentamos en el vídeo 🙂