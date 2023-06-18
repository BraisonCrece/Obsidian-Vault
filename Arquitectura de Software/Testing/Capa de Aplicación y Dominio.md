# Tipos de tests

## Test de aceptación

**Simulan ser un cliente de nuestra aplicación**. Entrarán en juego todas las implementaciones reales para comprobar que todo el flujo y la integración con la infraestructura se producen satisfactoriamente. Con lo cuál, las características principales serían:

- El objetivo de estos tests es el de **asegurar que la aplicación funciona correctamente** y el flujo completo de las peticiones se puede realizar satisfactoriamente.
- **Son los test más lentos** de ejecutar ya que tienen un alcance mayor y sí ejecutan operaciones de entrada/salida como inserts en base de datos ya que usan las implementaciones reales de estos componentes.
- **Aportan mayor valor** debido al alcance que tienen (nos asegura que absolutamente todo está ejecutandose como esperamos)
- En nuestro caso, al implementar una API HTTP, **simularemos peticiones HTTP** y comprobaremos que las respuestas tienen el código HTTP y el contenido del cuerpo esperados.
- Al ser los test más lentos de ejecutar, **sólo implementaremos una pequeña muestra de las distintas ramificaciones** que pueden tomar nuestros casos de uso. Dejando para los test unitarios la responsabilidad de probar cada una de las casuísticas. Así evitaremos incurrir en el [anti-patrón de test del cono de helado](https://www.youtube.com/watch?v=kXhXBzvxUsM).

### Ejemplo

Aquí vemos el ejemplo de un test de aceptación con Behat. Behat es un framework de testing para PHP que implementa Gherkin, con lo cuál, es muy similar a lo que podríamos encontrarnos con tests de Cucumber 🙂

```
// Extracto de: https://github.com/CodelyTV/cqrs-ddd-php-example/blob/af67faf454e29b608d57ea3ca7156e2b25696512/applications/api/features/video/video_post.feature

Feature: Create video
  In order to be the best youtuber ever
  As a codelyver
  I want to create a video

  Scenario: Create an screencast video
    Given  I send a POST request to "/video" with body:
    """
    {
      "request_id": "170cfccd-869d-414b-a521-9cce9e0e67a2",
      "id": "465892a1-5a77-4cee-9450-46ecd6b68f69",
      "title": "Exprimiendo los tipos de PHP7",
      "url": "https://codely.tv/screencasts/tipos-php-7/",
      "type": "screencast",
      "course_id": "9c8a481a-0fe2-49cf-ab8a-79bcc2965d00"
    }
    """
    Then the response should be empty
    And the response status code should be 201
```

## Test unitarios

Los test unitarios son los que usaremos para comprobar que la lógica de negocio de nuestros casos de uso (capa de aplicación) y modelos o servicios de dominio se comportan como esperamos. Características principales:

- El objetivo de estos tests es el de **validar que la implementación de nuestra lógica de negocio es correcta**.
- **Son los test más rápidos de ejecutar**. En estos tests falsearemos la implementación a usar de todo componente de infraestructura. Es decir, allá donde definamos un puerto en nuestros casos de uso, inyectaremos un doble de test para que no hagan operaciones de entrada/salida pero poder validar la interacción del dominio con estos componentes. Importante falsear la interface de dominio y no el cliente final para evitar incurrir en [el anti-patrón de Infrastructure Mocking.](https://www.youtube.com/watch?v=XbkjUz20pOk)
- **El test unitario será independiente del punto de entrada**. Desde el momento en el que encapsulamos nuestros casos de uso en servicios de aplicación para poderlos reaprovechar desde múltiples puntos de entrada (controlador API HTTP o CLI), el test unitario invocará directamente al caso de uso para desacoplarse también del controlador.
- Al ser los más rápidos de ejecutar y estar centrados en la lógica de negocio, es en estos test donde ubicamos las **comprobaciones más exhaustivas** en cuanto a las distintas ramificaciones de nuestros casos de uso.

### Ejemplo

Aquí os dejamos especificado con aclaraciones el primero de los ejemplos que vemos en el vídeo de la lección, el test del caso de uso de crear vídeo en el repo del [curso de API HTTP en Scala](https://pro.codely.tv/library/api-http-con-scala-y-akka/66747/about/). No obstante, si preferís la [implementación en PHP](https://github.com/CodelyTV/cqrs-ddd-php-example/blob/af67faf454e29b608d57ea3ca7156e2b25696512/src/Context/Video/Module/Video/Tests/Behaviour/CreateVideoTest.php), también la tenéis disponible gracias al repo del [curso de CQRS](https://pro.codely.tv/library/cqrs-command-query-responsibility-segregation-3719e4aa/62554/about/).

```
// Extracto de: https://github.com/CodelyTV/scala-http-api/blob/59b9d3281a904f01e483e5b7aa1c0af49df069f8/src/test/tv/codely/scala_http_api/module/video/application/create/VideoCreatorShould.scala
// Simplificando herencia con: https://github.com/CodelyTV/scala-http-api/blob/59b9d3281a904f01e483e5b7aa1c0af49df069f8/src/test/tv/codely/scala_http_api/module/video/infrastructure/repository/VideoRepositoryMock.scala y https://github.com/CodelyTV/scala-http-api/blob/59b9d3281a904f01e483e5b7aa1c0af49df069f8/src/test/tv/codely/scala_http_api/module/shared/infrastructure/MessagePublisherMock.scala

final class VideoCreatorShould extends UnitTestCase with MockFactory {

  // ℹ️ Falseamos las dependencias de infraestructura (adapters) creando un doble de test de la interface de dominio (ports)
  private val repository: VideoRepository = mock[VideoRepository]
  private val messagePublisher: MessagePublisher = mock[MessagePublisher]

  // ℹ️ Instanciamos el caso de uso inyectando las dependencias falseadas
  private val creator = new VideoCreator(repository, messagePublisher)

  "create a video" in {
    val video        = VideoStub.random
    val videoCreated = VideoCreatedStub(video)

    // ℹ️ Definimos la comprobación de que el caso de uso interacciona con el repositorio guardando nuestro vídeo
    repositoryShouldSave(video)

    // ℹ️ Definimos la comprobación de que el caso de uso interacciona con el publicador de eventos publicando el evento de vídeo creado
    publisherShouldPublish(videoCreated)

    // ℹ️ Invocamos al caso de uso directamente sin pasar por la capa del controlador y esperamos que se ejecute satisfactoriamente y con las interacciones previamente definidas
    creator.create(video.id, video.title, video.duration, video.category).shouldBe(())
  }

  private def repositoryShouldSave(video: Video): Unit =
    (repository.save _)
      .expects(video)
      .returning(Future.unit)

  private def publisherShouldPublish(message: Message): Unit =
    (messagePublisher.publish _)
      .expects(message)
      .returning(())
}
```

# Test de integración

Por último queda hablar de los test de integración. Estos serían un tipo de test unitario donde **el objeto de test es alguna implementación de uno de nuestros puertos**.

Es decir, en el caso del test unitario, habríamos falseado mediante un doble de test la interface de dominio `UserRepository`, mientras que en el test de integración lo que haremos será justamente testear la implementación de `MySqlUserRepository` para validar que se comporta como esperamos.

En la siguiente lección y en [el curso de testing](https://pro.codely.tv/library/testing-introduccion-y-buenas-practicas/90916/about/) veremos más ejemplos al respecto 🙂

# Mapeo entre tipos de test y capas de la arquitectura

En el siguiente diagrama podemos apreciar claramente qué tipo de test cubre cada una de nuestras capas de la Arquitectura Hexagonal:

- Aceptación: Todo
- Unitario: Aplicación y dominio
- Integración: Infraestructura

![](https://s3.amazonaws.com/pathwright-uploads/09EkzTi5Sg2O1LmchmN0_03%252BQue%2525CC%252581%252Bes%252Bla%252BArquitectura%252BHexagonal.key%252B2017-12-15%252B12-10-33.png)