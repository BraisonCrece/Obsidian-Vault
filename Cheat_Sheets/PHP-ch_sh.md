****# ACCESO A BASES DE DATOS:

### Para MySql existen 2 versiones:
- Estilo procedimental
  - `mysql_connect (servidor, usuario, password, bbdd, …)`
  - `mysqli_connect (servidor, usuario, password, bbdd, …)`
  
    **Se utiliza la que tiene la “i” de improved**  
- Estilo orientado a objetos
  - `new mysqli (servidor, usuario, password, bbdd, …)`
  - PDO (PHP Data Objects)
    - `new PDO (servidor, usuario, password, bbdd, …)`
    - `new PDO (dsn, usuario, password, opciones)`
      - dsn: Data Source Name
      - opciones: array de opciones


### PROCEDIMENTAL:

`mysqli_conect()`, és un alias de `mysql::_construct`, la cual, al ser instanciada devuelve un **identificador / objeto** que representa una conexión a una bbdd MySql o bien false, si hubo algún error.

```php $conexion = mysqli_connect ("db", "usuario", "abc123.", "test");```


Una vez tenemos la conexión lista, podemos enviar consultas con la función `mysqli_query()`:

``` php 
mysqli_query( $conexión, "SELECT * FROM test WHERE proto = 1");
```


___OJO: Valores devueltos de `mysqli_query()`___

➢ Para SELECT, SHOW, DESCRIBE, EXPLAIN... retornan un conjunto de resultados o false

➢ Para INSERT, UPDATE, DELETE, DROP, ... devuelve true o false (fué correcto, o no)

Una vez realizada la consulta tenemos que acceder al conjunto de resultados.
Para eso se pueden utilizar varias funciones, entre otras: 

- `mysqli_result()`
  ```php
  $resultado = mysqli_query($conexion, "SELECT * FROM test WHERE proto = 1");
  $fila = mysqli_result($resultado, 0, "nombre");  // $fila = "Juan";
  ```
- `mysqli_fetch_row()`
  ```php
  $resultado = mysqli_query($conexion, "SELECT * FROM test WHERE proto = 1");
  $fila = mysqli_fetch_row($resultado);  // $fila = ["Juan", "Perez", "1"];
  ```
- `mysqli_fetch_array()`
  ```php
  $resultado = mysqli_query($conexion, "SELECT * FROM test WHERE proto = 1");
  $fila = mysqli_fetch_array($resultado);  
  // $fila = ["nombre" => "Juan", "apellido" => "Perez", "proto" => "1"];
  ```
- `mysqli_num_rows()`
  ```php
  $resultado = mysqli_query($conexion, "SELECT * FROM test WHERE proto = 1");
  $num_filas = mysqli_num_rows($resultado);  // $num_filas = 1;
  ```
- `mysqli_affected_rows()`
  ```php
  $resultado = mysqli_query($conexion, "DELETE FROM test WHERE proto = 1");
  $num_filas = mysqli_affected_rows($resultado);  // $num_filas = 1;
  ```
Principalmente utilizamos 
- `mysqli_fetch_array()` para obtener los datos de una consulta en forma de array asociativo.

EJEMPLO COMPLETO DE CONEXION Y CONSULTA:

```php
<?php
$conexion=mysqli_connect("dbXdebug","usuario","abc123.", "proba");

if ($conexion) {
    mysqli_set_charset($conexion,"utf8");
    $resultado = mysqli_query($conexion,"SELECT codCliente,nombre,apellidos FROM cliente");
    if ($resultado != false) {
        while($fila = mysqli_fetch_array($resultado))
            echo $fila["codCliente"]," ",$fila["nombre"]," ", $fila["apelidos"],"<br>";
    }
} else {
    echo "Falló la conexión a la base de datos";
}

mysqli_close($conexion);
?>
```


## ORIENTADO A OBJETOS - SENTENCIAS PREPARADAS: 
Se utilizan para evitar inyecciones SQL.
Para las sentencias preparadas és conveniente conectarnos a la BD con estilo orientado a objetos.
En PHP se utiliza la palabra reservada new.
`$conexion = new mysqli(...);`
Y accedemos a los métodos de la clase mysqli con el operador ->

```php
$conexion = new mysqli("dbXdebug","usuario","abc123.", "proba");
if ($conexion->connect_errno) {
    echo "Falló la conexión a la base de datos";
    exit();
}
$conexion->set_charset("utf8");
```

Para preparar una sentencia utilizamos el método `prepare()` de la clase mysqli.

Las sentencias solo necesitan ser preparadas una vez, y luego pueden ser ejecutadas tantas veces como sea necesario y con diferentes parámteros.

Las sentencias preparadas siguen 3 pasos:
1. Preparar la sentencia -> `prepare()`
2. Enlazar los parámetros -> `bind_param()`
3. Ejecutar la sentencia -> `execute()`

Ejemplo:
```php
$conexion = new mysqli("dbXdebug","usuario","abc123.", "proba");
if ($conexion->connect_errno) {
    echo "Falló la conexión a la base de datos";
    exit();
}
$conexion->set_charset("utf8");

$sentencia = $conexion->prepare("SELECT codCliente,nombre,apellidos FROM cliente WHERE nombre = ? AND apellidos = ?");

// bind_param() recibe como primer parámetro una cadena
// con los tipos de datos de los parámetros y como segundo
// parámetro los valores de los parámetros.
// i: integer
// s: string
// d: double
// b: blob
$sentencia->bind_param("ss", $nombre, $apellidos);

$nombre = "Juan";
$apellidos = "Perez";

$sentencia->execute();

$resultado = $sentencia->get_result();

while($fila = $resultado->fetch_assoc())
    echo $fila["codCliente"]," ",$fila["nombre"]," ", $fila["apelidos"],"<br>";
```

## PDO - PHP DATA OBJECTS
Es una extensión de PHP que permite acceder a bases de datos de forma independiente del SGBD. (igual que Active Record en Ruby on Rails, o Hibernate en Java).

Es como un ORM (Object Relational Mapping) pero sin la capa de mapeo.

La extension PDO de php no es sufienciente para acceder a la BD, hay que instalar un driver para cada SGBD que queramos utilizar.

Por ejemplo, algunos controladores son:
- `PDO_MYSQL`
- `PDO_SQLITE`
- `PDO_PGSQL`
- `PDO_OCI`
- `PDO_ODBC`
- `PDO_FIREBIRD`
- `PDO_4D`
- `PDO_INFORMIX`
- etc.

Para saber los controladores instalados en nuestro servidor podemos utilizar la función `PDO::getAvailableDrivers()`

### Ejemplo de conexion con MySQL
```php
<?php
$dsn = 'mysql:host=dbXdebug;dbname=proba';
$usuario = 'usuario';
$contraseña = 'abc123.';
$opciones = array(
    PDO::MYSQL_ATTR_INIT_COMMAND => 'SET NAMES utf8',
);

try {
    $conexion = new PDO($dsn, $usuario, $contraseña, $opciones);
    foreach($conexion->query('SELECT * from FOO') as $fila) {
        print_r($fila);
    }
    $conexion = null;
} catch (PDOException $e) {
    print "¡Error!: " . $e->getMessage() . "<br/>";
    die();
}
?>
```

### PDO y excepciones
Cuando se produce un error en una consulta PDO, se lanza una excepción de tipo PDOException.

Para capturar las excepciones podemos utilizar el bloque try-catch.

PDO puede gestionar los errores de 3 maneras distintas, que podemos indicar con el método `setAttribute`:

✗ `PDO::ERRMODE_SILENT`: Es el modo por defecto. Tenemos que gestionar los errores con `errorCode( ) e ->errorInfo( )`.

✗ `PDO::ERRMODE_WARNING`: genera errores warning PHP y la ejecución continúa.

✗ `PDO::ERRMODE_EXCEPTION`: produce una excepción que podemos gestionar

```php
try {
    $conexion = new PDO($dsn, $usuario, $contraseña, $opciones);
    foreach($conexion->query('SELECT * from FOO') as $fila) {
        print_r($fila);
    }
    $conexion = null;
} catch (PDOException $e) {
    print "¡Error!: " . $e->getMessage() . "<br/>";
    die();
}
```

## PDO y sentencias preparadas
Para preparar una sentencia utilizamos el método `prepare()` de la clase PDO.

Ejemplo:
```php
$conexion = new PDO($dsn, $usuario, $contraseña, $opciones);
$sentencia = $conexion->prepare("SELECT codCliente,nombre,apellidos FROM cliente WHERE nombre = ? AND apellidos = ?");
// existen 2 formas de enlazar los parámetros:
// 1. con bindParam()
$sentencia->bindParam(1, $nombre);
$sentencia->bindParam(2, $apellidos);
// 2. con bindValue()
$sentencia->bindValue(1, $nombre);
$sentencia->bindValue(2, $apellidos);
```


