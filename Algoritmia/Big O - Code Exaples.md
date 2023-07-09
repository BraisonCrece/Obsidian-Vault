## O(N)
```ts
function sum_char_codes(n: string): number {
	let sum = 0;
	for (let i = 0; i < n.length; i++) {
		sum += n.charCodeAt(i);
	}
	return sum;
}
```

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

## O(N²)
```ts
function sum_char_codes(n: string): number {
	let sum = 0;
	for (let i = 0; i < n.length; i++) {
		for (let i = 0; i < n.length; i++) {
			sum += n.charCodeAt(i);
		}
	}
	return sum;
}
```

## O(N³)
```ts
function sum_char_codes(n: string): number {
	let sum = 0;
	for (let i = 0; i < n.length; i++) {
		for (let i = 0; i < n.length; i++) {
			for (let i = 0; i < n.length; i++) {
				sum += n.charCodeAt(i);
			}
		}
	}
	return sum;
}
```
