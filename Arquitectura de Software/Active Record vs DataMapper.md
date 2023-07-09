---
sticker: 1f503
---
## ActiveRecord 📼

Este enfoque se basa en que las propias<span style='color:#eb3b5a'> entidades</span> de nuestro dominio contengan la <span style='color:#eb3b5a'>infraestructura</span> necesaria a <span style='color:#eb3b5a'>nivel de persistencia</span> para poder ser persistidas, actualizadas, y recuperadas.

Es por tanto un patrón ligado a la [Layered Architecture](https://herbertograca.com/2017/08/03/layered-architecture/). Es decir, **la capa de dominio es la que se comunica con la base de datos**.

Esto se refleja en el código a través de:

- **Las entidades extienden de una clase base que permite la gestión de persistencia**:

```rb
class Product < ApplicationRecord
	#
end
```

- **Los métodos de interacción con la base de datos están en la propia entidad**:

```rb
# Crea una nueva instancia de User y a la vez la persiste en BD:
user = User.create(name: "David", occupation: "Code Artist")

# Guarda una instancia ya creada a través del constructor new:
user.save

# Busca un usuario en base de datos en base a un criterio de selección:
david = User.find_by(name: 'David')

# Actualiza el estado de una instancia en base de datos:
david.update(name: 'Pepe')
```

## DataMapper 🗺️

<span style='color:#3867d6'>Doctrine</span> es el ORM más extendido en el ecosistema PHP, como lo podría ser Hibernate en Java. Y en este caso nos encontramos con que a diferencia del patrón ActiveRecord, implementa el patrón <span style='color:#3867d6'>DataMapper</span>.

El patrón DataMapper lo que permite es justamente que **nuestras entidades no conozcan nada relativo a cómo éstas son persistidas en la base de datos**. Es decir:

- Por un lado tendremos nuestra entidad `Video`  (CAPA DE  <span style='color:#f7b731'>DOMINIO</span>): 

```php
// Ejemplo simplificado

// Entidad Video, de la capa de DOMINIO
final class Video
{
    private $id;
    private $title;

    public function __construct(VideoId $id, VideoTitle $title)
    {
        $this->id       = $id;
        $this->title    = $title;
    }

    public function id(): VideoId
    {
        return $this->id;
    }

    public function title(): VideoTitle
    {
        return $this->title;
    }
}
```

- Por otro lado tendremos el <span style='color:#3867d6'>fichero de definición</span> (PHP, YAML, o XML) que establece el <span style='color:#3867d6'>mapeo</span> entre cada una de las **entidades y atributos de nuestro sistema, y las tablas y columnas en las que son persistidas**:

```yaml
CodelyTv\Context\Video\Module\Video\Domain\Video:
  type:  entity
  table: video

  id:
    id:
      type: video_id
      column: id
      length: 36

  fields:
    courseId:
      type: course_id
      column: course_id

  embedded:
    title:
      class: CodelyTv\Context\Video\Module\Video\Domain\VideoTitle
      columnPrefix: false
```

- También será necesario definir la implementación necesaria para **interaccionar con la base de datos** (CAPA DE <span style='color:#3867d6'>INFRAESTRUCTURA</span>):

```php
namespace CodelyTv\Context\Video\Module\Video\Infrastructure\Persistence; // ℹ️ Destacar namespace de capa de Infraestructura

final class VideoRepositoryMySql implements VideoRepository
{
    private $entityManager;

    public function __construct(EntityManager $entityManager)
    {
        $this->entityManager = $entityManager;
    }

    public function save(Video $video): void
    {
        $this->entityManager()->persist($entity);
        $this->entityManager()->flush($entity);
    }

    public function search(VideoId $id): ?Video
    {
        return $this->repository(Video::class)->find($id);
    }
}
```

Como se puede apreciar a través de estos mismos ejemplos, **el trabajo que requiere a nivel de configuración e implementación el enfoque DataMapper es mayor**. Con lo cuál, deberemos tener una buena razón que compense ese trabajo de más 🙂

Justamente **el por qué optar por DataMapper se basa en el pilar de la Arquitectura Hexagonal**: **<span style='color:#eb3b5a'>Partimos de la premisa de que los detalles de infraestructura deben ser altamente tolerantes al cambio</span>**, con lo cuál, **<span style='color:#eb3b5a'>nuestro dominio no debe estar acoplado a esta infraestructura</span>** como sí lo estaría en Layered Architecture.

Para lograr ese desacoplamiento, **establecemos la interface `VideoRepository` a nivel de contrato de Dominio** (lo que denominaríamos como puerto en términos de Ports  & Adapters):
(CAPA DE <span style='color:#f7b731'>DOMINIO</span>)
```php
namespace CodelyTv\Context\Video\Module\Video\Domain; 
// ℹ️ Destacar namespace de capa de Dominio

interface VideoRepository
{
    public function save(Video $video): void;

    public function search(VideoId $id): ?Video;
}
```

Y **declaramos la citada implementación `VideoRepositoryMySql` en la capa de infraestructura** (lo que denominaríamos como <span style='color:#eb3b5a'>adaptador</span> en términos de Ports & Adapters).

**Esto permite que a la hora de necesitar recuperar Videos o persistirlos, podamos [aplicar el Principio de Inversión de Dependencias (DIP)](https://pro.codely.tv/library/principios-solid-aplicados/77070/about/)**:

```php
namespace CodelyTv\Context\Video\Module\Video\Application\Find; 
// ℹ️ Destacar namespace de capa de Aplicación (estamos en el caso de uso de buscar un vídeo)

// ℹ️ Destacar que no tenemos ningún import/use de la capa de Infraestructura.

// Aplicación sólo conoce Dominio gracias al haber aplicado el DIP en los puertos y adaptadores:
use CodelyTv\Context\Video\Module\Video\Domain\Video;
use CodelyTv\Context\Video\Module\Video\Domain\VideoId;
use CodelyTv\Context\Video\Module\Video\Domain\VideoNotFound;
use CodelyTv\Context\Video\Module\Video\Domain\VideoRepository;

final class VideoFinder
{
    private $repository;

    public function __construct(VideoRepository $repository) 
    // ℹ️ Nos acoplamos al contrato de dominio, no a la implementación concreta.
    {
        $this->repository = $repository;
    }

    public function __invoke(VideoId $id): Video
    {
        return $this->repository->search($id);
    }
}
```

Como se puede ver en el caso de uso `VideoFinder`, la ganancia de optar por un patrón DataMapper que separe la lógica de gestión de persistencia de nuestras entidades es que **en ningún caso nos estaremos acoplando a detalles de infraestructura**. Pudiendo cambiar la implementación de la interface `VideoRepository` que recibamos desde el exterior sin que tengamos que cambiar ni la capa de Aplicación ni la de 
Dominio. SOLID powah! 🎉






