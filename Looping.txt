# Looping

### For statement
For statement in Go has three basic syntax representations:

Simple `for` loop:

```
for initializer; test; incrementer {
}
```

While loop:

```
for test {
}
```

Infinite loop:

```
for {
}
```

There are three ways to break the loop:
- `continue` to break current iteration
- `break` to break current loop
- `break SomeLabel` to break specific loop (not only the nearest to the `break` keyword.

### Examples

Basic `for` loop example:	

```
	for i := 0; i < 5; i++ {
		fmt.Println(i)
	}
```

You can omit counter variable initialization in a loop if it was initialized before.	

**NB!** in this case `i` will be scoped to a **function** (`main()` in this case), not to the `for` loop!

```
	i := 2
	for ; i < 5; i++ {
		fmt.Println(i)
	}
```

While loop has no specific keyword reserved and uses `for` with condition like follows:

```
j := 2
for j < 5 {
	fmt.Println(j)
	j++
}
```

To create infinite loop we omit logical test from the for loop. Use `continue` and `break` keywords as usual:

```
for {
	// executes infinite number of times.
	// `continie` to proceed to next iteration
	// `break` to break the loop
}
```

Nested loops are used as expected:

```
for i := 0; i < 5; i++ {
	for i := 0; i < 5; i++ {
		fmt.Println(i * j)
	}
}
```

`break` keyword breaks only the closest loop! Use loop labels to break all enclosing loops from within nested loop:

```
LoopLabel:
	for i := 0; i < 5; i++ {
		for j := 0; j < 5; j++ {
			fmt.Println("->", i*j)
			if i*j >= 3 {
				break LoopLabel
			}
		}
	}
```

### Working with slices

This syntax works for `Slices`, `Arrays` and `Maps`. Exactly like in other programming languages:
		
```
s := []int{1, 2, 3}
for k, v := range s {
	fmt.Println(k, v)
}
```

Use underscore `_` to ignore key or value as follows:

```
	for _, v := range s {
		// key is ignored
		fmt.Println(v)
	}
}
```

> For loops also work with channels, yet we will be back to it during channels learning
