## <span style='color:#3867d6'>Fan-in y Fan-out</span>
**<span style='color:#f7b731'>Fan-out</span>** es el grado en que un método o clase llama a otros métodos o clases. 
**<span style='color:#eb3b5a'>Fan-in</span>** es lo contrario: el grado en que otros llaman a un método o clase.

Lo que esto significa es que los errores en las clases o los métodos con una alta <span style='color:#eb3b5a'>fan-in </span>(clases utilizadas ampliamente en todo el sistema) pueden tener un impacto mucho más amplio en el sistema general que los errores en las clases con una baja <span style='color:#eb3b5a'>fan-in</span>.

En el siguiente diagrama podemos ver que WidgetFaxOrder tiene un <span style='color:#eb3b5a'>fan-in</span> bajo, mientras que Widget tiene uno alto.
WidgetFaxOrder solo tiene una flecha entrante de "usos" que apunta hacia él. Por otro lado, Widget tiene dos flechas de "usos" entrantes, pero también está relacionado a través de Active Record con otras dos clases.

![[fan-in.png]]

Diagrama de que dependencias quedan dañadas por un bug en `WidgetFaxOrder` : 
![[Pasted image 20230222125248.png]]
Como podemos apreciar, al tener un bajo <span style='color:#eb3b5a'>fan-in</span> (solamente es utilizado por el controlador `OrdersController`), el efecto de cualquier bug dentro de la clase WidgetFaxOrder tendrá un impacto leve en el sistema.

Efectos colaterales de un bug en un módulo con un alto <span style='color:#eb3b5a'>fan-in</span>:
![[Pasted image 20230222125929.png]]

