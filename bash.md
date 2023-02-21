Para definir el patrón de tu prompt en el archivo `.bashrc`, puedes usar la siguiente línea:

Copy code

`PS1='\[\e[1;32m\]\u@\[\e[1;34m\]\w\[\e[1;32m\] \$ \[\e[0m\]~ '`

Esto configurará tu prompt para que se muestre de la siguiente manera:

Copy code

`braisoncrece@<la ruta actual> $ ~`

Además, el prompt estará en color verde.

Aquí te explico cada parte de la línea:

-   `\[\e[1;32m\]`: indica el inicio de una secuencia de escape ANSI que cambiará el color del texto a verde.
-   `\u`: muestra el nombre de usuario actual.
-   `\[\e[1;34m\]`: indica el inicio de una secuencia de escape ANSI que cambiará el color del texto a azul claro.
-   `\w`: muestra la ruta actual (working directory).
-   `\[\e[1;32m\]`: vuelve a cambiar el color del texto a verde.
-   `\$`: muestra el símbolo del sistema (`$` para usuarios normales y `#` para el usuario root).
-   `\[\e[0m\]`: indica el final de la secuencia de escape ANSI, volviendo al color del texto a su valor por defecto.
-   `~`: muestra el símbolo de la carpeta personal del usuario.


## Que es `curl`
`curl` es una herramienta de línea de comandos utilizada para transferir datos desde o hacia un servidor. En este caso, se utiliza para hacer una petición HTTP al servidor web que se está ejecutando en `localhost` en el puerto `9292`.

`localhost` se refiere al servidor local, es decir, al equipo donde se está ejecutando el comando. El número de puerto `9292` se utiliza para indicar al servidor web en qué puerto se espera la solicitud.

`/expenses/2017-06-10` es una parte de la URL que indica al servidor web qué recurso se está solicitando. En este caso, la solicitud se está realizando para el recurso `/expenses` y se le está pasando un parámetro de fecha `2017-06-10`.

`-w "\n"` es una opción de curl que indica que se debe imprimir una nueva línea después de la respuesta del servidor.

En resumen, el comando `curl localhost:9292/expenses/2017-06-10 -w "\n"` está haciendo una solicitud HTTP GET al servidor web que se está ejecutando en `localhost` en el puerto `9292`, para obtener el recurso `/expenses` con el parámetro de fecha `2017-06-10`, e imprime la respuesta del servidor con una nueva línea al final.

