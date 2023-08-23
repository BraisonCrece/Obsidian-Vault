# sequence()
`sequence` toma una lista de "cosas" (como resultados de operaciones asíncronas), y devuelve una sola "cosa" que contiene una lista de los resultados. Esto es útil cuando tienes varias operaciones asíncronas y quieres esperar a que todas ellas terminen y recoger todos los resultados.

```rb
# sequence :: [f a] -> f [a]

def sequence(*list)
# Allow for `sequence([a, b])` as well as `sequence(a, b)`.
	list = list.first if list.size == 1 && list.first.is_a?(Array)
	return Raxl::Fetch.pure([]) if list.empty?
	cons = ->(x, y) { [x] + y }
	first = list.first
	first.fmap(&cons.curry) * sequence(list[1..])
end
```

La biblioteca **Raxl**, es una biblioteca de **programación funcional** para Ruby.

1. Si la función `sequence` recibe un solo argumento que es un array, lo desempaqueta para trabajar con él. Esto permite que la función sea llamada con `sequence([a, b])` o `sequence(a, b)`.

2. Si la lista está vacía, devuelve un objeto `Raxl::Fetch` que contiene una lista vacía. `Raxl::Fetch.pure([])` es una forma de crear un objeto `Raxl::Fetch` que ya ha "terminado" con el resultado de una lista vacía.

3. Define una función `cons` que toma dos argumentos, `x` e `y`, y devuelve una nueva lista que es `x` seguido de `y`.
  
4. Toma el primer elemento de la lista y lo mapea (`fmap`) con la función `cons.curry`. Esto crea una nueva función que toma un solo argumento y devuelve una lista que contiene ese argumento seguido de los resultados de llamar a `sequence` en el resto de la lista.

**EJEMPLO DE USO DE LA FUNCIÓN SEQUENCE:**

```rb
def current_values(portfolio)
	@cash_value, @depot_value, @entire_total = run_query(
		@wallet.bank_type,
		proc { 
			[portfolio.present_cash_value,
			 portfolio.present_depot_value,
			 portfolio.total_current_value] 
		},
		proc { 
			run_raxl(
			### AQUI SE USA SEQUENCE ###
				sequence(
					portfolio.current_cash_value_,
					portfolio.current_depot_value_, 
					portfolio.current_total_value_
				)
			) 
		}
	)
end
```


En este caso, parece que `portfolio.current_cash_value_`, `portfolio.current_depot_value_`, y `portfolio.current_total_value_` son operaciones asíncronas o que podrían fallar. 

Al pasar estas operaciones a `sequence`, nos aseguramos de que todas las operaciones se completen antes de continuar. 

**Si alguna de las operaciones falla, entonces toda la secuencia fallará**.

El resultado de `sequence` es entonces pasado a `run_raxl`.