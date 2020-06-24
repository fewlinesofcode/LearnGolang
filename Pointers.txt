# Pointers

## Creating pointers
Pointer types use an asterisk (`*`) as a prefix to type pointed to. e. g. `*int` - a pointer to integer.

Use the `addressoff` operator (`&`) to get address of variable

In this case `b` copies the value of `a`

```
a := 42
b := a
fmt.Println(a, b) // 42 42
a = 27
fmt.Println(a, b) // 27 42
```

```
var a int = 42
var b *int = &a // `&` read as "address of ..."
fmt.Println(a, b) // 42 42
a = 27
fmt.Println(a, b) // 27 27
```

## Dereferencing pointers
Dereference a pointer by preceding with asterisk (`*`). Complex types like structs are automatically dereferenced.

## The `new` function
Create pointers to objects 
	Can use the `addressoff` operator (`&`) if value type already exists
	
```
	ms := myStruct{foo: 42}
	p := &ms
``` 
	
Use the `addressoff` operator (`&`) before the initialiser:

```
&myStruct{foo: 42}
```	
	
Use `new` keyword to initialise an object. But you can't initialise fields using `new` at the same time.

## Working with `nil`
Zero value for pointer is `<nil>`

## Types with internal pointers
Types with internal pointers are basically a reference types

In this case `b` copies the value of `a`

```
func main() {
	a := 42
	b := a
	fmt.Println(a, b) // 42 42
	a = 27
	fmt.Println(a, b) // 27 42
}
```

In this case we will see the memory address printed instead of `b`. 
Use "dereferencing" operator `*` to get actual value:

```
func main() {
	var a int = 42
	var b *int = &a   // `&` read as "address of ..."
	fmt.Println(a, b) // 42 0xc000014098
	a = 27
	fmt.Println(a, b) // 27 0xc000014098
	// NB! b is dereferenced
	fmt.Println(a, *b) // 27 27
	*b = 14
	fmt.Println(a, *b) // 14 14
}
```

Go does NOT allow Pointer arithmetic!
Use `unsafe` package if you (which is not likely) need pointer arithmetics

You can use pointers when working with structures:

```
func main() {
	// var ms myStruct
	// ms = myStruct{foo: 42}
	// fmt.Println(ms) // {42}

	var ms *myStruct
	ms = &myStruct{foo: 42}
	fmt.Println(ms) // &{42}
}

type myStruct struct {
	foo int
}
```

Consider the following example.
Here we have to dereference pointers manually. Yet when we are
using pointers, compiler will help us out and we can omit dereferencing:

```
type myStruct struct {
	foo int
}
```

This:

```
func main() {
	var ms *myStruct
	ms = new(myStruct) // Initializes empty struct
	(*ms).foo = 42
	fmt.Println((*ms).foo) // 42
}
```

Will be equal to this:

```
func main() {
	var ms *myStruct
	ms = new(myStruct) // Initializes empty struct
	ms.foo = 42
	fmt.Println(ms.foo) // 42
}
```
