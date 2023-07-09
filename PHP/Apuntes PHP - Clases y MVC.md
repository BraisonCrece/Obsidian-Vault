## Sesiones
Al crear la sesión se genera y asigna un identificados o **Session ID**. Este SID debe ser propagado de una pagina a otra dentro de nuestro sitio web para poder recuperar los datos asociados a esa sesión en concreto.

Para inicializar sesión utilizaremos la siguiente función

```php
	session_start()
```

Para guardar datos del usuario en la sesión, usamos el array asociativo $_SESSION[] 

```php
	$_SESSION['usuario'] = 'Pepito'
```

Para cerrar sesión

```php
	session_destroy()
```

Para vaciar el array asociativo $_SESSION

```php
	session_unset()
```


## Autenticación con Bases de Datos
Las contraseñas se guardarán siempre cifradas en la base de datos. Para esto usaremos los siguientes mecanismos de cifrado / descifrado:
Cifrado:
```php
	password_hash('contraseñaSegura', PASSWORD_DEFAULT)
```

Descifrado:
```php
	password_verify('contraseñaSegura', $hashAComprobar)
```


## Cookies
Una cookie es un fragmento de texto que se envía desde os servidores de un sitio web y se guarda en el navegador del cliente. La idea es solucionar el problema del stateless de HTTP.
Para trabajar con cookies utilizaremos a función `setcookie()`

```php
	setcookie('clave', 'valor')
```

Para definir una duración de cookie usaremos la siguiente sintaxis:

```php
	setcookie('idioma', 'gallego', time() + 7200)
	// os segundos de una hora
```

Para acceder a los datos de la cookie, simplemente debemos utilizar el array asociativo `$_COOKIE['clave']` 

```php
	$_COOKIE['idioma']
```

Para eliminar una cookie podemos hacer lo siguiente:

```php
	setcookie('claveAEliminar', time() - 1800)
	// le pasamos como tiempo de expiración segundos en negativo
```


# Ataques maliciosos y como prevenirlos
## Fijación de sesión
Para evitar este tipo de ataque debemos regenerar el SID en las sesiones nuevas

```php
	session_start();
	if(!isset($_SESSION[‘marcadecontrol’])
	{ 
		session_regenerate_id(true); //borrarmos o ficheiro da ID da sesión
		$_SESSION[‘marcadecontrol’]= true; 
	}
```

y cambiar este SID cada vez que el usuario se loguee

```php
	if ($usuario_logueado === true)
	{
		session_regenerate_id(true);
		$_SESSION['logueado'] = true;
	}
```


## Ataques XSS (Cross Site Scripting)
Para evitar este tipo de ataques usaremos una de las siguientes funciones:

```php
	htmlspecialchars()
	htmlentities()
	strip_tags()
```


# Clases
Métodos máxicos:
```php
	__clone()
	__get()
	__set()
	__isset()
	__unset()
	__toString()
	__invoke()
	__call()
	__sleep()
	__wakeup()
	
```

Constantes predefinidas:
```php
	__CLASS__ // Devuelve el nombre de la clase
	__METHOD__ // Devuelve el nombre del método
	__FILE__ // Ruta completa
	__DIR__ // Directorio del archivo
	__LINE__ // Línea actual del archivo
```

Mantenimiento del estado, serialización de objetos:
```php
	$miguel = new Persoa(); 
	$miguelSerializado = serialize ($miguel);

	$miguel = unserialize($miguelSerializado);
```


Herencia:
```php
	public function __construct()
	{ 
		parent::__construct(); //EXECÚTASE PRIMEIRO O CONSTRUTOR DE EMPREGADO
		$this->$turno=”non especificado”; 
	}
```