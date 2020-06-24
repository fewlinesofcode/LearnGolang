# Functions

## Syntax

Syntax is similar to variables:
- camelCase notation
- start from Capital letter to make it visible from outside a package

Function with arguments:

- `sayMessage` - func name
- `msg`, `others`, `index` - parameters. NB: Parameters of the same types can be grouped as `msg` and `other`
- `(int, error)` multiple return values

```
the same types can be grouped as `msg` and `others`

func sayMessage(msg, other string, index int) (int, error) {
	return ...
}
```

## Parameters
Parameters are passed by **Value** except for types with internal pointers (Reference types like Map and Slice)

We can also pass by pointer. Passing by pointer has usual drawback - **Shared mutable state**, yet it is much more efficient than copying when passing by value because we don't need to copy large structs.


```
func main() {
	greeting := "Hello"
	name := "Alice"
	sayGreeting(&greeting, &name)
	mainMethodsSample()
}

func sayGreeting(greeting, name *string) {
	fmt.Println(*greeting, *name)
	*name = "Bob" // We change the value of `name`
	fmt.Println(*name)

	// Prints:
	// Hello Alice
	// Bob
}
```

### Variadic parameters. 

There can be only one variadic parameter per function and it can be only **last** parameter. Variadic parameters are accessed as **`slice`**:

```
func sum(values ...int) {
	fmt.Println(values)
	result := 0
	for _, v := range values {
		result += v
	}
	fmt.Println(result)
}
```

## Return values
Type of return value is declared before the curly brace in function declaration. `int` is a return value of the following function:

```
func sumAndReturn(values ...int) int {
	fmt.Println(values)
	result := 0
	for _, v := range values {
		result += v
	}
	fmt.Println(result)
	return result // explicitly return value
}
```

In Go it is safe to return a pointer to the result of a function. Compiler will automatically promote return pointer to Heap so it can be accessed. See the following example:

```
func sumAndReturnPointer(values ...int) *int {
	fmt.Println(values)
	result := 0
	for _, v := range values {
		result += v
	}
	fmt.Println(result)
	return &result // Go will automatically promote this variable to the Heap!
}
```

Named return values can free us from the work on declarin variables inside.

**NB!** Better don't do it if it is not really needed!

```
func sumNamedReturn(values ...int) (result int) {
	for _, v := range values {
		result += v
	}
	return
}
```


Multiple return values are implemented as typles. It is quite common to replace `panic` calls. We return expected value and error if something unwanted happened (similar to `ResultType` in Swift)

Use pattern matching to read multiple return parameters as follows:

```
func someFunc() {
	d, error := divide(5.0, 0.0)
	if error != nil {
		fmt.Println("Error happened!")
		return
	}
	fmt.Println(d)
}

func divide(a, b float64) (float64, error) {
	if b == 0 {
		return 0.0, fmt.Errorf("Cannot divide by zero")
	}
	return a / b, nil
}
```

## Functions are first class citizens

Here are some examples of anonymous functions

```
func someFunc1() {
	for i := 0; i < 5; i++ {
		func() { // Anonymous function
			fmt.Println("Hello, Go!")
		}() // `()` immediately invokes anonymous functions.

		func(i int) { // Anonymous function with parameter
			fmt.Println("Hello, Go!", i)
		}(i) // `(i)` immediately invokes anonymous functions passing `i` as a parameter

		// The same as above but we use f as a function variable.
		// func(int) is a function type. Similar to other languages
		var f func(int) = func(i int) {
			fmt.Println("Hello, ", i)
		}
		f(i)
	}
}
```

## Methods

Method is a function that executes in a context of a type   
greet() wil copy the context provided in `(g greeter)`. Context is passed as value!
The value of the `greeter` is called "value receiver"

```
func mainMethodsSample() {
	g := greeter{
		greeting: "Hello",
		name:     "Go",
	}
	g.greet()
}

type greeter struct {
	greeting string
	name     string
}

func (g greeter) greet() {
	fmt.Println(g.greeting, g.name) // Hello Go
}
```

Example with pointer receiver

```
func (g *greeter) greet() {
	fmt.Println(g.greeting, g.name) // Hello Go
}
```