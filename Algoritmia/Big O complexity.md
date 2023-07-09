### What is Big O
Big O is a way to categorize your algorithms time or memory requirements based on input.

### Why do we use it?
It could help us to make decisions about what data structures and algorithms to use.

#### A small example
```ts
function sum_char_codes(n: string): number {
	let sum = 0;
	for (let i = 0; i < n.length; i++) {
		sum += n.charCodeAt(i);
	}
	return sum;
}
```

This algorithm has an **O(N)** complexity. Because it depends directly on the input size. 1 more char = 1 more loop, and so on.


Now, take a look at this algorithm, it should have an O(2N) right?
```ts
function sum_char_codes(n: string): number {
	let sum = 0;
	for (let i = 0; i < n.length; i++) {
		sum += n.charCodeAt(i);
	}
	
	for (let i = 0; i < n.length; i++) {
		sum += n.charCodeAt(i);
	}
	return sum;
}
```

well, **not really**. Has the same complexity! 

### Other kind of
Take a look at this algo:

```ts 
function sum_char_codes(n: string): number {
	let sum = 0;
	for (let i = 0; i < n.length; i++) {
		const charCode = n.charCodeAt(i); 
		if (charCode === 69) {
			return sum;
		}
		sum += charCode;
	}
	return sum;
}
```

Even though the algorithm in some cases could finish sooner, we have to comtemplate **the worst scenario possible** (the letter we are looking for is not found in the string or is at the end)

So the Big O in this case is **O(N)** again.

![[big-o-chart-tutorial-bazar-aymptotic-notations-1.png]]