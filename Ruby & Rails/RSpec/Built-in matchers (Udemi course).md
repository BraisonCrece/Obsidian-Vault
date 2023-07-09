to --> que  
not_to  --> que no

<span style='color:#2d98da'>eq </span>-->igual => Compara valor <span style='color:#0fb9b1'>==</span>
<span style='color:#3867d6'>eql</span> -->igual => Compara valor y tipo <span style='color:#3867d6'>===</span>
<span style='color:#8854d0'>equal</span> /<span style='color:#8854d0'> be</span> -->  Compara dirección de memoria
![[Pasted image 20230504012657.png]]
<span style='color:#fa8231'>contain_exactly</span> -->El elemento contiene exactamente los valores indicados
<span style='color:#eb3b5a'>start_with</span> --> Pues eso
<span style='color:#4b6584'>end_with</span> --> Pues eso


## Predicate methods and predicate matchers
<span style='color:#20bf6b'>be_even</span> --> es par
<span style='color:#fa8231'>be_odd</span> --> es Impar
<span style='color:#8854d0'>be_zero</span> --> es cero 
<span style='color:#0fb9b1'>be_empty</span> --> está vacío
<span style='color:#20bf6b'>be_truthy</span> --> es truthy
<span style='color:#eb3b5a'>be_falsy</span> --> es falsy
be_nil -->es concretamente nil

## All matcher

Podemos usar el método `all()` para hacer referencia a todos los elementos de una colección. 
Como argumento le pasaremos un matcher:
```rb
# This:
[5, 7, 9].each do |value|
	expect(value).to be_odd
end
# is the same as this:
expect([5, 7, 9]).to all(be_odd)
```

## Change matcher
Sirve para comprobar que hubo cambios sobre un objetos, en la condición indicada:
```rb
RSpec.describe "change matcher" do
	subject { [1, 2, 3] }
	
	it "checks that a method changes object state" do
		expect { subject.push(4) }.to change { subject.length }
	end
end
```

Se puede definir todavía más, si usamos los métodos from() y to():
```rb
expect { subject.push(4) }.to change { subject.length }.from(3).to(4)
```

Y también podemos usar el método by() para definir en cuanto cambió y no desde donde hasta donde:
```rb
expect { subject.push(4) }.to change { subject.length }.by(1)
```

## <span style='color:#eb3b5a'>Doubles</span>
Los dobles son exactamente eso, definiciones concretas de objetos, que emulan el comportamiento de un objeto real.
En RSpec podemos construir un doble del siguiente modo:

```rb
it "only allows defined methods to be invoked" do
	stuntman = double(
		"Mr. Danger", 
		fall_off_ladder: "OUCH!", 
		light_on_fire: true
	 )

	expect(stuntman.fall_off_ladder).to eq("OUCH!")
	expect(stuntman.light_on_fire).to eq(true)
end
```

```zsh
a random double
  only allows defined methods to be invoked

Finished in 0.00209 seconds (files took 0.0704 seconds to load)
1 example, 0 failures	
```

Otro modo de definir y asociar métodos al doble:
```rb
stuntman = double("Mr.Danger")
allow(stuntman).to receive(:fall_off_ladder).and_return("OUCH!")
allow(stuntman).to receive(:light_on_fire).and_return(true)
```

Y otro:
```rb
stuntman = double("Mr.Danger")

allow(stuntman).to receive_messages(
	fall_off_ladder: "OUCH",
	light_on_fire: true
)
```

