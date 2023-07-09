---
sticker: 1f913
---
MostraDatos
```php
try {
    $response = file_get_contents($URL);

    if ($response === false) {
        throw new Exception('Erro obtendo datos de ' . $URL);
    }

    $resultado = json_decode($response);

    if ($resultado !== null) {
        echo json_encode($resultado -> datos);
    } else {
        echo json_encode(['error' => 'Erro decodificando o JSON']);
    }
} catch (Exception $e) {
    header('Content-Type: application/json');
    echo json_encode(['error' => $e->getMessage()]);
}
```

JS
```js
const getData = async (option = null) => {
    const url = `devolveDatos.php`
    const formData = new FormData($form)
    fetch(url, {
        method: 'POST',
        body: formData
    }).then(response => response.json())
        .then(data => {
            if (option) {
                saveData(JSON.stringify(data))
            }
            printData(data)

            if (data.error) {
                alert(`houbo un erro ${data.error}`)
            }
        })
        .catch(error => {
            alert(`houbo un erro ${error}`)
        })
}
```

SaveData
```php
$server = 'dbXdebug';
$db = 'tarefa';
$user = "root";
$pass = "ManuBrais2022.,ROOT";

$conexion = new PDO("mysql:host=$server;dbname=$db;charset=utf8mb4", $user, $pass);
$conexion->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

$data = json_decode($_POST['data']);
try {
    foreach($data  as $medicion) {
        [$fecha, $hora] = explode(" ", $medicion->fechahora);
        $pdoStm = $conexion->prepare('INSERT INTO datos(variable, data, hora, valor) values(:variable, :data, :hora, :valor)');
        $pdoStm->execute(["variable"=>$_POST['sensor'], "data"=>$fecha, "hora"=>$hora, "valor"=>$medicion->valor]);
    }
    echo json_encode(['success' => 'Todo correcto bro']);
} catch(Exception $e) {
    echo json_encode(['erro'=> 'MERDA!']);
}
```
