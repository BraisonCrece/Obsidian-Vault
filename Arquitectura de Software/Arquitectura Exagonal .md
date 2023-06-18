---
sticker: 1f3db-fe0f
banner: https://javiervelezreyes.com/content/images/2016/02/post-160220.jpg
---
# ¿Que <span style='color:#eb3b5a'>NO</span> es la arquitectura de software?
- No es arquitectura de hardware

# ¿Que <span style='color:#20bf6b'>SI</span> es la arquitectura de software?
- Conjunto de reglas auto-impuestas al definir como diseñamos software
- Forma parte de las consideradas Arquitecturas Limpias
![[clean-architecture.jpg]]

Ninguna de las capas internas conocen a las de fuera, por lo tanto siempre podremos cambiar lo de fuera sin que lo de dentro se entere.

¿Que se actualiza nuestro framework de turno, y tenemos que cambiar todos nuestros controladores?
NO PASA NADA, porque al usar arquitectura exagonal (Clean Architecture) el ámbito de actuación está delimitado. Solo tendría impacto en la propia capa de controladores.

Que sea hexagonal, no quiere decir que tenga forma de hexágono o esté limitado a 6 elementos.

También es conocida como **PORTS & ADAPTERS**.

Capas de la arquitectura hexagonal:
![[ge22DKPbSOWYkS8Cj1eF_03%2BQue%25CC%2581%2Bes%2Bla%2BArquitectura%2BHexagonal.key%2B2017-12-15%2B12-04-45.png]]

### <span style='color:#2d98da'>Infraestructura</span>: 
Se refiere a todos los detalles técnicos que están **fuera del núcleo de la lógica de negocio**. Esto podría incluir, por ejemplo, **bases de datos**, **bibliotecas de terceros**, **frameworks web**, **sistemas de archivos**, etc. La idea es que estos componentes son **periféricos y pueden cambiarse sin afectar a la lógica de negocio**.

### <span style='color:#eb3b5a'>Aplicación</span>: 
Se refiere a la capa que coordina la aplicación del modelo de dominio (reglas de negocio) a los problemas del mundo real. Se encarga de orquestar el flujo de datos hacia y desde las interfaces externas y hacia y desde el dominio. **Actúa como un intermediario entre la lógica de negocio y el mundo exterior**.
Contiene todos los casos de uso (registrar usuario, publicar producto, añadir producto al carrito, etc).
`Caso de uso === Application Service`

### <span style='color:#f7b731'>Dominio</span>: 
El dominio es el núcleo del sistema y **contiene toda la lógica de negocio**. En la arquitectura hexagonal, esto está completamente separado de la infraestructura y las preocupaciones de la aplicación. **El dominio no debería tener conocimiento de nada fuera de sí mismo**, como la base de datos o la interfaz de usuario. Esencialmente, el dominio debería poder funcionar de manera independiente, lo que significa que debería ser posible probar toda la lógica de negocio sin interactuar con ninguna otra capa.
![[09EkzTi5Sg2O1LmchmN0_03%2BQue%25CC%2581%2Bes%2Bla%2BArquitectura%2BHexagonal.key%2B2017-12-15%2B12-10-33.png]]


## <span style='color:#f7b731'>Regla de Oro de la Arquitectura Hexagonal</span> 📝
Esta regla nos dice que el código que viva en cada una de nuestras capas <mark style='background:#eb3b5a'>sólo deberá conocer las clases que se ubican en la capa inmediatamente siguiente</mark>. Entendemos el orden de las capas <mark style='background:#eb3b5a'>desde fuera hacia dentro</mark> del círculo: 
#### Infraestructura -> Aplicación -> Dominio.

## PUERTOS Y ADAPTADORES
Los **puertos** son las interfaces o API que define el dominio de la aplicación. Son abstracciones que desacoplan la lógica de negocio de los detalles de la implementación de servicios externos como bases de datos, UI, etc.

-> ejemplo: 
`UserRepository` sería una interfaz que define cómo se deben hacer las operaciones de base de datos relacionadas con los usuarios. 
<span style='color:#eb3b5a'>Esta interfaz no tiene ninguna implementación específica</span>, simplemente define un contrato o un conjunto de operaciones que se pueden realizar.

Los **adaptadores** son implementaciones específicas de esos puertos. Un adaptador toma la entrada de un puerto y la traduce a una forma que la infraestructura (por ejemplo, una base de datos MySQL, un servicio web, una interfaz de usuario) pueda entender y procesar. 

-> ejemplo:
`MySqlUserRepository` sería un adaptador que implementa la interfaz `UserRepository` para una base de datos MySQL específica. 
Aquí, los detalles sobre cómo interactuar con MySQL se manejan, mientras que <span style='color:#eb3b5a'>la lógica de negocio sigue sin conocer nada sobre MySQL</span>.

La belleza de este enfoque es que<span style='color:#eb3b5a'> se puede cambiar la implementación de infraestructura</span> (por ejemplo, cambiando de MySQL a PostgreSQL) <span style='color:#eb3b5a'>sin tener que modificar la lógica de negocio</span>. Solo necesitas proporcionar un nuevo adaptador que implemente la interfaz del puerto para el nuevo sistema de infraestructura.






## Principales beneficios de usar arquitectura exagonal
- Mantenibilidad
	- Es mucho más fácil implementar o modificar una feature en una aplicación que utilice arquitectura hexagonal que una que no.
	- Evitamos el crecimiento de la complejidad accidental:
		- La complejidad accidental son todas estas implementaciones no necesarias para el dominio principal. O bien liadas innecesarias por x o y motivo. Utilizando arquitectura exagonal evitamos que la complejidad accidental nos coma.
		- Complejidad esencial:
			- *if users want a program to do 30 different things, then those 30 things are essential and the program must do those 30 different things.*
		![[Pasted image 20230608183846.png]]
- Tolerancia a cambios
	- Por ejemplo si estamos utilizando una base de datos SQL y necesitamos más performance, podremos implementar otras tecnologías de un modo más rápido, sencillo y eficaz.
	- 
- Testing
	- Se facilita mucho el testing
	- Test unitarios: Capa de <mark style="background: #FF5582A6;">Aplicación</mark> y <mark style="background: #FFF3A3A6;">Dominio</mark>
	- Test de integración: <mark style="background: #ABF7F7A6;">Capa de Infraestructura</mark>
	- Test de Aceptación: **Todas las capas**
- Simplicidad




