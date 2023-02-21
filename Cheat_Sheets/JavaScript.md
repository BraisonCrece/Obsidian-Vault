# Chuleta de JavaScript

## Funciones de Array

### **`map()`**

Crea un nuevo array con los resultados de la llamada a la función indicada aplicados a cada uno de sus elementos.

```javascript
// lista de parametros que acepta la funcion de callback:
// 1. elemento actual
// 2. indice
// 3. array

const array1 = [1, 4, 9, 16];

const map1 = array1.map(x => x * 2);
console.log(map1); // expected output: Array [2, 8, 18, 32]
```

### **`filter()`**

Crea un nuevo array con todos los elementos que cumplan la condición implementada por la función dada.

```javascript
// lista de parametros que acepta la funcion de callback:
// 1. elemento actual
// 2. indice
// 3. array

const words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];
const result = words.filter(word => word.length > 6);
console.log(result);
// expected output: Array ["exuberant", "destruction", "present"]
```

### **`reduce()`**

Aplica una función a un acumulador y a cada valor de un array (de izquierda a derecha) para reducirlo a un único valor.

```javascript
// lista de parametros que acepta la funcion de callback:
// 1. acumulador
// 2. valor actual
// 3. indice actual
// 4. array original

const array1 = [1, 2, 3, 4];

// podemos definir una funcion de callback
const reducer = (accumulator, currentValue) => accumulator + currentValue;

console.log(array1.reduce(reducer)); // expected output: 10      // 1 + 2 + 3 + 4

// tambien podemos inicializar el acumulador, como por ejemplo en 5
console.log(array1.reduce(reducer, 5));
// expected output: 15
```

### **`forEach()`**

Ejecuta la función indicada una vez por cada elemento del array.

```javascript
// lista de parametros que acepta la funcion de callback: 
// 1. elemento actual, 
// 2. indice 
// 3. propio array

const array1 = ['a', 'b', 'c'];
array1.forEach(element => console.log(element));
// expected output: "a"
// expected output: "b"
// expected output: "c"
```

### **`every()`**

Comprueba si todos los elementos del array cumplen la condición implementada por la función dada.

```javascript
// lista de parametros que acepta la funcion de callback:
// 1. elemento actual -> solo se usa este parametro normalmente
// 2. indice
// 3. array

const isBelowThreshold = (currentValue) => currentValue < 40;

const array1 = [1, 30, 39, 29, 10, 13];

console.log(array1.every(isBelowThreshold)); // expected output: true
```

### **`some()`**

Comprueba si al menos un elemento del array cumple la condición implementada por la función dada.

```javascript
// lista de parametros que acepta la funcion de callback:
// 1. elemento actual -> solo se usa este parametro normalmente
// 2. indice
// 3. array

const array = [1, 2, 3, 4, 5];

// comprobar si algun elemento es mayor que 10
const isBiggerThan10 = element => element > 10;

console.log(array.some(isBiggerThan10)); // expected output: false
```

### **`find()`**

Devuelve el valor del primer elemento del array que cumple la función de prueba proporcionada. En caso contrario devuelve undefined.

```javascript
// lista de parametros que acepta la funcion de callback:
// 1. elemento actual -> solo se usa este parametro normalmente
// 2. indice
// 3. array

const array1 = [5, 12, 8, 130, 44];

const found = array1.find(element => element > 10);

console.log(found); // expected output: 12
```

### **`Math.max()`**

Devuelve el mayor de cero o más números. Debemos usar el operador `spread (...)` para pasar un array como argumento.
Este operador se llama __spread porque "esparce" los elementos del array como argumentos de la función__.
Variante de `Math.min()`.
```javascript
const array1 = [1, 2, 3];

console.log(Math.max(...array1)); // expected output: 3
```

## tipos de bucles

### **`for`**

```javascript
for (let i = 0; i < 10; i++) {
  console.log(i);
}
```

### **`for...in`**
Se utiliza para recorrer las propiedades de un objeto.
```javascript
// for...in se diferencia de for...of en que:
// - for...in --> itera sobre las propiedades enumerables de un objeto, en ORDEN ARBITRARIO. 
// - for...of --> itera sobre los valores de los objetos iterables, en ORDEN DEFINIDO.
const object = { a: 1, b: 2, c: 3 };

for (const property in object) {
  console.log(`${property}: ${object[property]}`);
}
```

### **`for...of`**
Se utiliza para recorrer los valores de un objeto iterable.
```javascript

const array1 = ['a', 'b', 'c'];

for (const element of array1) {
  console.log(element);
}
```

### **`while`**

```javascript

let i = 0;

while (i < 10) {
  console.log(i);
  i++;
}
```

### **`do...while`**

```javascript

let i = 0;

do {
  console.log(i);
  i++;
} while (i < 10);
```




### generar un array de numeros aleatorios
  
  ```javascript
  const array = Array.from({length: 100}, () => Math.floor(Math.random() * 100));
  ```

### generar un array de numeros aleatorios, dentro de un rango
  
  ```javascript
  const array = Array.from({length: 100}, () => Math.floor(Math.random() * (max - min + 1) + min));
  ```

# FUNCIONES DE #STRING

### **`padStart()`**

Rellena la cadena actual con otra cadena (repetida, si es necesario) hasta que alcance la longitud especificada. El relleno se aplica desde el inicio (izquierda) de la cadena actual.

```javascript
// parametros que acepta la funcion:
// 1. longitud total de la cadena
// 2. cadena de relleno (si no se especifica, se rellena con espacios en blanco)
const str1 = '5';

console.log(str1.padStart(2, '0')); // expected output: "05"

console.log('5'.padStart(2)); // expected output: "05"
```

### **`padEnd()`**

Rellena la cadena actual con otra cadena (repetida, si es necesario) hasta que alcance la longitud especificada. El relleno se aplica desde el final (derecha) de la cadena actual.

```javascript
// parametros que acepta la funcion:
// 1. longitud total de la cadena
// 2. cadena de relleno (si no se especifica, se rellena con espacios en blanco)
const str1 = '5';

console.log(str1.padEnd(2, '0')); // expected output: "50"

console.log('5'.padEnd(2)); // expected output: "50"
```

### **`charAt()`**

Devuelve el carácter en la posición especificada.

```javascript
// parametros que acepta la funcion:
// 1. indice del caracter
const str = 'Mozilla';

console.log(str.charAt(1)); // expected output: "o"
```

### **`charCodeAt()`**

Devuelve un número que indica el valor Unicode del carácter en la posición especificada.

```javascript
// parametros que acepta la funcion:
// 1. indice del caracter
const str = 'Mozilla';

console.log(str.charCodeAt(1)); // expected output: 111
```

### **`concat()`**

Devuelve una nueva cadena que contiene el texto de las cadenas de texto pasadas como parámetros.

```javascript
// parametros que acepta la funcion:
// 1. cadena de texto a concatenar
// 2. cadena de texto a concatenar
// 3. puedes agregar tantas cadenas de texto como quieras
const str1 = 'Hello';
const str2 = 'World';

console.log(str1.concat(' ', str2)); // expected output: "Hello World"
```

### **`endsWith()`**

Comprueba si la cadena de texto termina con los caracteres de una cadena de texto determinada, devolviendo true o false según corresponda.

```javascript

const str1 = 'Cats are the best!';

console.log(str1.endsWith('best', 17)); // expected output: true
```

### **`startsWith()`**

Comprueba si la cadena de texto comienza con los caracteres de una cadena de texto determinada, devolviendo true o false según corresponda.

```javascript
// parametros que acepta la funcion:
// 1. cadena de texto a buscar
// 2. posicion de inicio de la busqueda (opcional)
const str1 = 'Saturday night plans';

console.log(str1.startsWith('Sat')); // expected output: true

console.log(str1.startsWith('Sat', 3)); // expected output: false
```


### **`includes()`**

Comprueba si una cadena de texto contiene los caracteres de una cadena de texto determinada, devolviendo true o false según corresponda.

```javascript
// parametros que acepta la funcion:
// 1. cadena de texto a buscar
// 2. posicion de inicio de la busqueda (opcional)
const str1 = 'To be, or not to be, that is the question.';

console.log(str1.includes('To be')); // expected output: true

console.log(str1.includes('question')); // expected output: true

console.log(str1.includes('nonexistent')); // expected output: false

console.log(str1.includes('To be', 1)); // expected output: false

console.log(str1.includes('TO BE')); // expected output: false
```

### **`indexOf()`**

Devuelve el índice dentro de la cadena de la primera aparición del valor especificado, o -1 si no se encuentra.

```javascript
// parametros que acepta la funcion:
// 1. cadena de texto a buscar
// 2. posicion de inicio de la busqueda (opcional)
const paragraph = 'The quick brown fox jumps over the lazy dog. If the dog barked, was it really lazy?';

const searchTerm = 'dog';

console.log(`The index of the first "${searchTerm}" from the beginning is ${paragraph.indexOf(searchTerm)}`); // expected output: "The index of the first "dog" from the beginning is 40"

console.log(`The index of the 2nd "${searchTerm}" is ${paragraph.indexOf(searchTerm, (paragraph.indexOf(searchTerm) + 1))}`); // expected output: "The index of the 2nd "dog" is 52"

```

### **`lastIndexOf()`**

Devuelve el índice dentro de la cadena de la última aparición del valor especificado, o -1 si no se encuentra.

```javascript
// parametros que acepta la funcion:
// 1. cadena de texto a buscar
// 2. posicion de inicio de la busqueda (opcional)
const paragraph = 'The quick brown fox jumps over the lazy dog. If the dog barked, was it really lazy?';

const searchTerm = 'dog';

console.log(`The index of the last "${searchTerm}" is ${paragraph.lastIndexOf(searchTerm)}`); // expected output: "The index of the last "dog" is 52"

console.log(`The index of the 2nd last "${searchTerm}" is ${paragraph.lastIndexOf(searchTerm, (paragraph.lastIndexOf(searchTerm) - 1))}`); // expected output: "The index of the 2nd last "dog" is 40"

```

### **`slice()`**

Extrae una sección de una cadena y devuelve una nueva cadena.

```javascript
// parametros que acepta la funcion:
// 1. indice de inicio
// 2. indice de fin (opcional)

const str = 'The quick brown fox jumps over the lazy dog.';
console.log(str.slice(31)); // expected output: "the lazy dog."

console.log(str.slice(4, 19)); // expected output: "quick brown fox"

console.log(str.slice(-4)); // expected output: "dog."

console.log(str.slice(-9, -5)); // expected output: "lazy"

```

### **`split()`**

Divide una cadena de texto en un array de cadenas de texto.

```javascript
// parametros que acepta la funcion:
// 1. separador (opcional)
// 2. limite (opcional) (numero de elementos que se devuelven en el array)

const str = 'The quick brown fox jumps over the lazy dog.';
const words = str.split(' ');
console.log(words[3]); // expected output: "fox"

const chars = str.split('');
console.log(chars[8]); // expected output: "k"

// con split podemos hacer copias de cadenas de texto (dentro de un array), sin afectar a la cadena original
const strCopy = str.split();
console.log(strCopy); // expected output: Array ["The quick brown fox jumps over the lazy dog."]

```

### **`match()`**

Busca una coincidencia entre una expresión regular y una cadena de texto, y devuelve la coincidencia encontrada.

```javascript
// parametros que acepta la funcion:
// 1. expresion regular

const paragraph = 'The quick brown fox jumps over the lazy dog. It barked.';
const regex = /[A-Z]/g;
const found = paragraph.match(regex);

console.log(found);
// expected output: Array ["T", "I"]

```

### **`matchAll()`**

Busca todas las coincidencias entre una expresión regular y una cadena de texto, y devuelve un iterador con las coincidencias encontradas.

```javascript
// parametros que acepta la funcion:
// 1. expresion regular

const str = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz';
const regexp = /[A-E]/gi;
const matches = [];

for (const match of str.matchAll(regexp)) {
  matches.push(match);
}

console.log(matches);
// expected output: Array ["A", "B", "C", "D", "E", "a", "b", "c", "d", "e"]
```


### **`replace()`**

Busca una coincidencia entre una expresión regular y una cadena de texto, y devuelve una nueva cadena de texto donde se reemplazan las coincidencias.

```javascript
// parametros que acepta la funcion:
// 1. expresion regular
// 2. cadena de texto que reemplaza a la expresion regular

const p = 'The quick brown fox jumps over the lazy dog. If the dog reacted, was it really lazy?';

console.log(p.replace('dog', 'monkey'));
// expected output: "The quick brown fox jumps over the lazy monkey. If the dog reacted, was it really lazy?"
```

### **`replaceAll()`**

Busca todas las coincidencias entre una expresión regular y una cadena de texto, y devuelve una nueva cadena de texto donde se reemplazan las coincidencias.

```javascript
// parametros que acepta la funcion:
// 1. expresion regular
// 2. cadena de texto que reemplaza a la expresion regular

const p = 'The quick brown fox jumps over the lazy dog. If the dog reacted, was it really lazy?';

console.log(p.replaceAll('dog', 'monkey'));
// expected output: "The quick brown fox jumps over the lazy monkey. If the monkey reacted, was it really lazy?"
```

### **`search()`**

Busca una coincidencia entre una expresión regular y una cadena de texto, y devuelve el índice de la coincidencia. Si no se encuentra ninguna coincidencia, devuelve -1.

```javascript
// parametros que acepta la funcion:
// 1. expresion regular

const paragraph = 'The quick brown fox jumps over the lazy dog. If the dog barked, was it really lazy?';

const regex = /dog/;
console.log(paragraph.search(regex));
// expected output: 40

const regex2 = /bird/;
console.log(paragraph.search(regex2));
// expected output: -1
```

### **`trim()`**

Elimina los espacios en blanco de los extremos de una cadena de texto. Variantes: `trimStart()` y `trimEnd()`.

```javascript
// parametros que acepta la funcion:
// ninguno

const greeting = '   Hello world!   ';
console.log(greeting);
// expected output: "   Hello world!   ";

console.log(greeting.trim());
// expected output: "Hello world!";
```


