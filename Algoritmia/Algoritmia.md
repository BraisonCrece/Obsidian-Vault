<span style='color:#eb3b5a'>Progresión Geométrica</span>
Es una función matemática que nos permite calcular una progresión en el tiempo.
Por ejemplo, imaginemos que una empresa está creciendo a un ritmo de 1.3x cada mes. El primer mes la empresa tiene un valor de 80.000€.

Si queremos averiguar el valor que la empresa obtendrá en 10 meses, podemos aplicar la siguiente fórmula:
![[Pasted image 20230212104133.png]]

la **n** hace referencia a el mes que se quiere calcular
La a1 hace referencia al valor inicial
La **r** es el número de incremento mensual, en este caso 1.3x

Por ejemplo si queremos calcular los beneficios de la empresa para dentro de 8 meses haremos el siguiente cálculo:

valorEn8Meses = 80.000 x 1,3 elevado a (8 - 1)

<span style='color:#eb3b5a'>Factoriales</span>
En matemáticas, el factorial de un entero positivo n, escrito n!, es el producto de todos los enteros positivos menores que e iguales a n.
```
5! = 5 * 4 * 3 * 2 * 1
```

<table style='text-align: center'>
	<thead>
		<tr style='border: 1px solid white'>
			<th></th>
			<th style='border: 1px solid white; padding: 1rem'>n!</th>
			<th style='border: 1px solid white; padding: 1rem'>2^n</th>
		</tr>
	</thead>
	<tbody>
		<tr style='border: 1px solid white'>
			<td style='border: 1px solid white; padding: 1rem'>2</td>
			<td style='border: 1px solid white; padding: 1rem'>2</td>
			<td style='border: 1px solid white; padding: 1rem'>4</td>
		</tr>
		<tr style='border: 1px solid white'>
			<td style='border: 1px solid white; padding: 1rem'>3</td>
			<td style='border: 1px solid white; padding: 1rem'>6</td>
			<td style='border: 1px solid white; padding: 1rem'>8</td>
		</tr>
		<tr style='border: 1px solid white'>
			<td style='border: 1px solid white; padding: 1rem'>4</td>
			<td style='border: 1px solid white; padding: 1rem'>24</td>
			<td style='border: 1px solid white; padding: 1rem'>16</td>
		</tr>
		<tr style='border: 1px solid white'>
			<td style='border: 1px solid white; padding: 1rem'>5</td>
			<td style='border: 1px solid white; padding: 1rem'>120</td>
			<td style='border: 1px solid white; padding: 1rem'>32</td>
		</tr>
		<tr style='border: 1px solid white'>
			<td style='border: 1px solid white; padding: 1rem'>6</td>
			<td style='border: 1px solid white; padding: 1rem'>720</td>
			<td style='border: 1px solid white; padding: 1rem'>64</td>
		</tr>
</tbody>
</table>

como podemos apreciar, crecimiento factorial es mucho más rapido que exponencial.

<span style='color:#eb3b5a'>Binary Search</span>
![[binary-and-linear-search-animations.gif]]
Un algoritmo de búsqueda binaria es un ejemplo común de un algoritmo O(log(n)). Las búsquedas binarias funcionan en una <span style='color:#0fb9b1'>lista ordenada de elementos</span>.

```python
def binary_search(target, arr):
    low = 0
    high = len(arr) - 1
    while low <= high:
        median = (low + high) // 2
        if arr[median] < target:
            low = median + 1
        else:
            high = median - 1
    return low != len(arr) and arr[low] == target
```


<span style='color:#eb3b5a'>Complejidad Big-O</span>
![[bigOChart.jpeg]]

<span style='color:#8854d0'>O(n) </span>-> Tiene un crecimiento lineal
por ejemplo:
```
find_min(1 item) = 2 milliseconds
find_min(2 items) = 4 milliseconds
find_min(3 items) = 6 milliseconds
find_min(4 items) = 8 milliseconds
find_min(5 items) = 10 milliseconds
```
tiene un crecimiento O(2), lineal.

  
<span style='color:#8854d0'>O(n^2)</span> -> Orden "n al cuadrado", tiene un crecimiento exponencial
Una forma común en que un algoritmo caerá en O(n^2) es usando un bucle anidado, donde el número de iteraciones de cada bucle es igual al número de elementos en la entrada.
Esto quiere decir que a cuanto mayor sea n (el numero de entrada ), la salida tendrá un retraso exponencial cuadrático.

<span style='color:#8854d0'>O(n^m)</span> -> Es muy similar a O(n^2). La diferencia es que en lugar de una sola entrada que nos interesa, hay 2. Si n y m aumentan al mismo ritmo, entonces O(nm) es efectivamente lo mismo que O(n^2). Sin embargo, si n o m aumentan más rápido o más lento, es útil realizar un seguimiento de su complejidad por separado.

<span style='color:#8854d0'>O(1)</span> -> significa que no importa el tamaño de la entrada, no hay crecimiento en el tiempo de ejecución del algoritmo.

<span style='color:#8854d0'>O(log(n))</span> son solo un poco más lentos que O(1). Crecen según el tamaño de la entrada, pero solo según el logaritmo de la entrada. 

	O(n): 
	8 elementos -> 8 milisegundos 
	64 elementos -> 64 milisegundos 
	1024 elementos -> 1024 milisegundos 
	1048576 elementos -> 1048576 milisegundos 
	O(log(n)): 
	8 elementos -> 3 milisegundos 
	64 elementos -> 6 milisegundos 
	1024 elementos -> 10 milisegundos 
	1048576 elementos -> 20 milisegundos

<span style='color:#eb3b5a'>Recursividad en arbol</span>
![[recursion_example.svg]]

## <span style='color:#eb3b5a'>Polinomial vs Exponencial </span>
El tiempo polinomial describe cualquier algoritmo con un tiempo de ejecución que no aumenta más rápido que n^k, donde k es cualquier constante y n es el tamaño de la entrada. 

<span style='color:#0fb9b1'>Tiempo polinomial </span>
* O(1) 
* O(n) 
* O(n*log(n)) 
* O(n^2) 
* O(n^3) 

Las funciones polinómicas <span style='color:#3867d6'>escalan bien</span>. Son lo suficientemente rápidas para ser útiles en general. Pequeños cambios en el tamaño de la entrada generalmente no induce cambios enormes en el tiempo de ejecución general. 

<span style='color:#0fb9b1'>Tiempo exponencial </span>
* O(2 ^ n) 
* O(n ^ n) 
* O(n!) (técnicamente, este es el tiempo factorial, pero agrupémoslos juntos para simplificar) 

Las funciones exponenciales <span style='color:#8854d0'>escalan terriblemente</span>.Pequeños cambios en el tamaño de la entrada inducen grandes cambios en el tiempo de ejecución general.

## <span style='color:#eb3b5a'>Big 0 Notation</span>
<table>
<thead>
<tr>
<th>Big-O</th>
<th>Name</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>O(1)</strong></td>
<td>constant</td>
<td><strong><span style='color:#20bf6b'>Best</span></strong> The algorithm always takes the same amount of time, regardless of how much data there is. Example: Looking up an item in a list by index</td>
</tr>
<tr>
<td><strong>O(log n)</strong></td>
<td>logarithmic</td>
<td><strong><span style='color:#20bf6b'>Great</span></strong> Algorithms that remove a percentage of the total steps with each iteration. Very fast, even with large amounts of data. Example: Binary search</td>
</tr>
<tr>
<td><strong>O(n)</strong></td>
<td>linear</td>
<td><strong><span style='color:#20bf6b'>Good</span></strong> 100 items, 100 units of work. 200 items, 200 units of work. This is usually the case for a single, non-nested loop. Example: unsorted array search.</td>
</tr>
<tr>
<td><strong>O(n log n)</strong></td>
<td>"linearithmic"</td>
<td><strong><span style='color:#20bf6b'>Okay</span></strong> This is slightly worse than linear, but not too bad. Example: mergesort and other "fast" sorting algorithms.</td>
</tr>
<tr>
<td><strong>O(n^2)</strong></td>
<td>quadratic</td>
<td><strong><span style='color:#f7b731'>Slow</span></strong> The amount of work is the square of the input size. 10 inputs, 100 units of work. 100 Inputs, 10,000 units of work. Example: A nested for loop to find all the ordered pairs in a list.</td>
</tr>
<tr>
<td><strong>O(n^3)</strong></td>
<td>cubic</td>
<td><strong><span style='color:#fa8231'>Slower</span></strong> If you have 100 items, this does 100^3 = 1,000,000 units of work. Example: A doubly nested for loop to find all the ordered triples in a list.</td>
</tr>
<tr>
<td><strong>O(2^n)</strong></td>
<td>exponential</td>
<td><strong><span style='color:#eb3b5a'>Horrible</span></strong> We want avoid this kind o algorithm at all costs. Adding one to the input <em>doubles</em> the amount of steps. Example: Brute-force guessing results of a sequence of <code>n</code> coin flips.</td>
</tr>
<tr>
<td><strong>O(n!)</strong></td>
<td>factorial</td>
<td><strong><span style='color:#eb3b5a'>Even More Horrible</span></strong> The algorithm becomes so slow so fast, that is is practically unusable. Example: Generating all the permutations of a list</td>
</tr>
</tbody>
</table>

<span style='color:#eb3b5a'>Bubble Sort</span>
Bubble sort es famoso por lo fácil que es escribir. Es uno de los algoritmos de clasificación más lentos, pero puede ser útil para un script rápido o cuando se garantiza que la cantidad de datos que se clasificarán es pequeña.

```python
def bubble_sort(nums):
    swapping = True
    while swapping:
        swapping = False
        for i in range(1, len(nums)):
            if nums[i - 1] > nums[i]:
                nums[i-1], nums[i] = nums[i], nums[i-1]
                swapping = True
    return nums
```

<https://youtu.be/lyZQPjUT5B4>


<table style="border-collapse: collapse; width: 100%;">
  <thead>
    <tr style="border-bottom: 2px solid #ddd; background-color: #grey;">
      <th style="text-align: left; padding: 8px;">Algoritmo</th>
      <th style="text-align: left; padding: 8px;">Peor caso</th>
      <th style="text-align: left; padding: 8px;">Uso recomendado</th>
    </tr>
  </thead>
  <tbody>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 8px;">Radix sort</td>
      <td style="padding: 8px;">O(d(n+k))</td>
      <td style="padding: 8px;">Para arreglos de enteros con un rango acotado y longitud fija</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 8px;">Quicksort</td>
      <td style="padding: 8px;">O(n^2) en el peor caso, O(n log n) en promedio</td>
      <td style="padding: 8px;">Para arreglos grandes y aleatorios</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 8px;">Mergesort</td>
      <td style="padding: 8px;">O(n log n)</td>
      <td style="padding: 8px;">Para arreglos grandes y no tan aleatorios</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 8px;">Heapsort</td>
      <td style="padding: 8px;">O(n log n)</td>
      <td style="padding: 8px;">Para arreglos grandes</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 8px;">Counting sort</td>
      <td style="padding: 8px;">O(n+k)</td>
      <td style="padding: 8px;">Para arreglos de enteros no negativos con un rango acotado</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 8px;">Burbuja (Bubble sort)</td>
      <td style="padding: 8px;">O(n^2)</td>
      <td style="padding: 8px;">Solo para pequeñas cantidades de datos</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 8px;">Selección (Selection sort)</td>
      <td style="padding: 8px;">O(n^2)</td>
      <td style="padding: 8px;">Solo para pequeñas cantidades de datos</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 8px;">Inserción (Insertion sort)</td>
      <td style="padding: 8px;">O(n2)</td>
<td style="padding: 8px;">Solo para pequeñas cantidades de datos o para datos casi ordenados</td>
</tr>

  </tbody>
</table>





