---
banner: https://www.rabbitmq.com/img/logo-rabbitmq.svg
sticker: 1f407
tag: PIranha-Client
---
RabbitMQ es una solución de mensajería de código abierto que permite a las aplicaciones **conectarse y escalar**. Proporciona una forma de <span style='color:#3867d6'>intercambiar datos entre procesos</span>, <span style='color:#3867d6'>aplicaciones</span>, <span style='color:#3867d6'>servicios</span> y <span style='color:#3867d6'>servidores</span> en una variedad de sistemas operativos y lenguajes de programación.

RabbitMQ implementa el protocolo<span style='color:#4b6584'> Advanced Message Queuing Protocol</span> (AMQP) y ofrece varias características como mensajería confiable, enrutamiento flexible, soporte para patrones de mensajería múltiple y escalabilidad, entre otros.

En esencia, RabbitMQ funciona como un <span style='color:#3867d6'>intermediario para el manejo de mensajes</span>. Las aplicaciones pueden poner mensajes en una <span style='color:#3867d6'>cola de mensajes</span>, que RabbitMQ gestionará hasta que otros servicios o aplicaciones estén listos para procesarlos. 

Esto significa que los<span style='color:#fa8231'> productores</span> (generadores de mensajes) y los <span style='color:#20bf6b'>consumidores</span> (procesadores de mensajes) no necesitan interactuar directamente entre sí y **pueden continuar funcionando normalmente incluso si el otro está inactivo o no está disponible por algún motivo**.

Algunos usos comunes de RabbitMQ incluyen:

- ### **Desacoplar procesos**: 
	- RabbitMQ puede usarse para desacoplar procesos o servicios en un sistema distribuido. Los servicios pueden publicar mensajes en una cola sin preocuparse de cuándo o cómo serán procesados.
- ### Balanceo de carga: 
	- RabbitMQ puede distribuir mensajes de manera uniforme entre múltiples trabajadores o servicios, lo que permite una fácil escalabilidad horizontal.
- ### Confiabilidad: 
	- RabbitMQ ofrece confiabilidad a través de características como la **persistencia de mensajes** y la **confirmación de entrega**.
- ### Patrones de mensajería: 
	- RabbitMQ soporta varios patrones de mensajería, incluyendo **pub/sub**, **enrutamiento**, **tópicos**, y **colas de trabajo**.

---

![[Pasted image 20230608132523.png]]

### <span style='color:#2d98da'>AMQP Modelo de Mensajería</span>

- Exchanges:
	- Acepta mensajes de clientes productores de mensajes
	- Enruta los mensajes a las colas apropiadas
- Bindings:
	- Desacopla el mensaje del cliente productor
	- Crea las reglas de enrutamiento de mensajes hacia la cola correspondiente
- Colas de Mensajes:
	- Vinculados a un exchange
	- almacena en un búfer los mensajes hasta que son consumidos por los clientes
- Conexión:
	- Una conexión TCP física entre cliente y broker
	- consiste de varios canales
- Channel:
	- Un tuner virtual que conecta clientes con brokers