---
sticker: 1f309
---
Aspectos a tener en cuenta:

- Las particularidades de cada adaptador/implementación de nuestras interfaces las especificaremos a través de<span style='color:#eb3b5a'> inyección vía constructor</span>. Ejemplos:
	- **Conexión con base de datos en repositorios**
	- **Sender y credenciales SMTP en servicio de notificación vía email**
	- **Canal y API Key en servicio de notificación vía Slack**
	- etc...
- Evitaremos el [acoplamiento estructural en nuestras interfaces](https://codely.tv/screencasts/errores-comunes-interfaces-solid/) no acoplando los contratos, lógica, o flujo de llamadas a conceptos relacionados con alguna de nuestras implementaciones.
- Usaremos implementaciones fake de servicios como el de envío de email para ejecutar nuestros tests.

# Estructura de directorios

Si nos fijamos en la estructura general de la aplicación nos encontramos la siguiente jerarquía de directorios:

```
$ tree -L 3
.
├── entry_point
│   ├── EntryPointDependencyContainer.scala
│   ├── Routes.scala
│   ├── ScalaHttpApi.scala
│   └── controller
│       ├── status
│       ├── user
│       └── video
└── module
    ├── shared
    │   └── infrastructure
    ├── user
    │   ├── application
    │   ├── domain
    │   └── infrastructure
    └── video
        ├── application
        ├── domain
        └── infrastructure
```

En esta jerarquía podemos ver cómo dentro de cada módulo de nuestra aplicación (usuarios y vídeos) tenemos 3 carpetas, una para cada capa de nuestra arquitectura:

![](https://s3.amazonaws.com/pathwright-uploads/P8sajazRLmPg0ZO2IkQ8_ge22DKPbSOWYkS8Cj1eF_03%25252BQue%252525CC%25252581%25252Bes%25252Bla%25252BArquitectura%25252BHexagonal.key%25252B2017-12-15%25252B12-04-45.png)

## Módulos o sub-dominios

Los módulos que comentábamos son agrupaciones de código en base a los conceptos principales de nuestra aplicación. En el ejemplo anterior veíamos el de vídeos y el de usuarios. En estos módulos agruparemos cada uno de los respectivos casos de uso, conceptos de dominio, e infraestructura relacionada.

Esto es un punto importante ya que implica darle la vuelta a la jerarquía tradicional de directorios donde (asumiendo que agrupamos el código por conceptos) nos encontraríamos algo similar a lo siguiente:

```
$ tree -L 3 -d
.
├── application
│   ├── user
│   │   └── search
│   └── video
│       ├── create
│       └── search
├── domain
│   ├── user
│   └── video
├── entry_point
│   └── controller
│       ├── status
│       ├── user
│       └── video
└── infrastructure
    ├── shared
    │   ├── config
    │   ├── dependency_injection
    │   └── persistence
    ├── user
    │   ├── dependency_injection
    │   ├── marshaller
    │   └── repository
    └── video
        ├── dependency_injection
        ├── marshaller
        └── repository
```

Los motivos de este cambio, hacer que los conceptos de nuestro sistema estén en un primer nivel de jerarquía son varios:

- **Cohesión y facilidad de encontrar lo que buscas**:
    - La aplicación **refleja conceptos de dominio antes que conceptos relacionados con la arquitectura** de Software. Esto visibiliza más nuestro dominio haciendo que sea más fácil de encontrar lo que buscas
    - Los conceptos relacionados con un mismo módulo están más cerca unos de otros. Esto hace que sea más fácil moverse entre los distintos componentes necesarios a modificar.
- **Escalabilidad del código/mantenibilidad**
    - Uno de los principales motivos por los que separar nuestra aplicación en módulos o sub-dominios es el de promover la mantenibilidad de la aplicación a lo largo del tiempo. Con lo cuál, podría tener sentido buscar el aislamiento entre los distintos módulos de nuestra aplicación. Desde el momento en el que son unidades atómicas (**cada carpeta de modules + la de shared autocontiene todo lo necesario para su funcionamiento**), esto se torna más fácil.
    - Podemos elevar el aislamiento entre módulos a través de un Command y Query Bus como se ve en [el curso de CQRS](https://pro.codely.tv/library/cqrs-command-query-responsibility-segregation-3719e4aa/62554/about/). Esto haría que los módulos no se conocieran entre sí (no podríamos hacer uso del repositorio del módulo de vídeos desde el módulo de usuarios por ejemplo). La comunicación se haría a través del bus.  
        Esto **facilitaría una eventual promoción de los módulos a Bounded Context**, servicios aislados, o división de los módulos en caso de que alguno empiece a crecer mucho. Si os interesa este aspecto, os recomendamos el citado curso de CQRS 🙂

Volviendo a nuestra estructura, si analizamos el contenido del módulo de vídeos, nos encontramos la siguiente estructura:

```
$ tree module/video
module/video
├── application
│   ├── create
│   │   └── VideoCreator.scala
│   └── search
│       └── VideosSearcher.scala
├── domain
│   ├── Video.scala
│   ├── VideoCategory.scala
│   ├── VideoDuration.scala
│   ├── VideoId.scala
│   ├── VideoRepository.scala
│   └── VideoTitle.scala
└── infrastructure
    ├── dependency_injection
    │   └── VideoModuleDependencyContainer.scala
    ├── marshaller
    │   └── VideoJsonFormatMarshaller.scala
    └── repository
        └── DoobieMySqlVideoRepository.scala
```

Estructura que, recuperando el diagrama que vimos en la [lección “Qué es la Arquitectura Hexagonal”](https://pro.codely.tv/library/arquitectura-hexagonal/66748/path/step/35626564/):

![](https://s3.amazonaws.com/pathwright-uploads/czbyBRpDStOgkno4BYV9_09EkzTi5Sg2O1LmchmN0_03%25252BQue%252525CC%25252581%25252Bes%25252Bla%25252BArquitectura%25252BHexagonal.key%25252B2017-12-15%25252B12-10-33.png)

Podemos ver cómo bajaría al terreno los elementos a nivel conceptual en términos concretos:

- **Controller**: `scala-http-api/src/main/tv/codely/scala_http_api/entry_point/controller/video/VideoGetController.scala`
- **Application Service**: `scala-http-api/src/main/tv/codely/scala_http_api/module/video/application/search/VideosSearcher.scala`
- **Model**: `scala_http_api/module/video/domain/Video.scala`
- **Repository contract**: `scala_http_api/module/video/domain/VideoRepository.scala`
- **Repository implementation**: `scala_http_api/module/video/infrastructure/repository/DoobieMySqlVideoRepository.scala`

## Infraestructura compartida

Cuando empezamos a implementar este enfoque, una de las dudas que nos pueden surgir es: ¿Qué hacemos con los aspectos de infraestructura compartidos entre los distintos módulos?

Hablamos por ejemplo de la configuración de base de datos, conexión a ésta, y otras posibles cositas que tengamos. Lo que hacemos nosotros es ubicarla en un modulo “shared”:

```
$ tree module/shared/infrastructure
module/shared/infrastructure
├── config
│   └── DbConfig.scala
├── dependency_injection
│   └── SharedModuleDependencyContainer.scala
└── persistence
    └── doobie
        ├── DoobieDbConnection.scala
        └── TypesConversions.scala
```

Asumimos que a este módulo compartido sí tendrán acceso todos los distintos módulos de nuestra aplicación, con lo que si nos llevamos alguno de ellos a un servicio externo, tendremos que llevarnos también la parte del módulo shared 🙂

## Dominio compartido

A su vez, otro de los conceptos que compartiremos entre módulos serán nuestros pequeños Value Objects que modelan por ejemplo los **identificadores de nuestras entidades**.

Esto lo haremos ya que, por ejemplo, un Video podría contener el identificador del usuario que lo ha publicado. Con lo cuál, desde el momento en el que la forma de relacionar una entidad de un módulo con otra entidad de un módulo diferente es a través de este identificador y no una relación de asociación para evitar el acoplamiento, no nos queda otra que compartir el UserId entre los dos módulos.

# Bounded Contexts

El concepto de Bounded Context se sale del ámbito de este curso ya que estaría relacionado más con [Domain-Driven Design](https://pro.codely.tv/library/domain-driven-design-ddd/87157/about/), que estrictamente con Arquitectura Hexagonal. Recomendamos el curso de DDD Aplicado una vez acabado éste curso, el de testing, y el de CQRS para poder tener claros los conceptos a nivel de implementación antes. No obstante, vamos a analizarlo a modo de pincelada para que os podáis mover también por [el repositorio de ejemplo en PHP](https://github.com/CodelyTV/cqrs-ddd-php-example).

Básicamente lo que establece este concepto es un nuevo nivel de separación en nuestro código. Es decir, de fuera hacia dentro nuestra aplicación tendrá N contextos donde cada uno de ellos tendrá N módulos.

Cabe destacar que a diferencia de los módulos, cada contexto tendrá su propia infraestructura. Para comunicarnos entre contextos usaremos el mismo método que para comunicarnos entre módulos: Commands o Queries a través del Bus correspondiente 🙂

La estructura de directorios que veríamos por tanto en este otro caso sería similar a:

```
$ tree src -L 5 -d // ℹ️ Output simplificando para ilustrar mejor el objeto de análisis de esta lección
src
├── Context
│   ├── Video
│   │    ├── Infrastructure
│   │    │   ├── Doctrine
│   │    │   └── Symfony
│   │    ├── Module
│   │    │   ├── Notification
│   │    │   │   ├── Application
│   │    │   │   ├── Domain
│   │    │   │   ├── Infrastructure
│   │    │   │   └── ...
│   │    │   └── ...
│   │    └── ...
│   └── ...
├── Infrastructure
│   ├── Bus
│   │   ├── Command
│   │   ├── Event
│   │   └── Query
│   └── ...
├── Shared
│   ├── Domain
│   │   └── Bus
│   │       ├── Command
│   │       ├── Event
│   │       └── Query
│   ├── Infrastructure
│   │   └── Persistence
│   │       └── Course
│   └── ...
└── ...
```

Por lo tanto, al igual que nos pasaba a nivel de módulo, nos encontraremos situaciones a nivel de contexto donde queramos compartir ciertos aspectos de nuestra aplicación, incluída la infraestructura (ejemplo: código relacionado con el Bus). Con lo cuál, tendremos por lo tanto esa carpeta Shared a nivel compartido entre contextos 🙂.