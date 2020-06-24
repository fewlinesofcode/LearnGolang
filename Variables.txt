# Variables

### Declaration
```
var i int // When var should be initialised later
var j int = 42 // Wneh me have to make sure exact type
anotherVar := 42 // The := syntax is shorthand for declaring and initializing a variable at the same time

// NB: When declaring a var on package level Short declaration won't work. Full declaration style should be used

var somePackageLevelVariable string = "hello"
```

Variables can be declared in groups. It prevents repeating of `var` and may show that variables are related

```
var (
	name string = "Jon"
	surname string = "Doe"
	id int = 42
)
```

Variables of the same type may be declared in one line

```
var i, j, k int = 1, 2, 3
```

### Redeclaration and shadowing
Variable **cannot** be **redeclared**!

```
var i int = 42
i = 20
// i now equals 20
```

If variable was declared in the local scope and has the same name, as variable declared on *package* level, then it will **shadow** package level variable. 

```
package main

import "fmt"

var i int = 21 // Package level variable

func main() {
	fmt.Println(i)	
	i := 42 // Variable with the same name declared in the local scope
	fmt.Println(i)
	
	printPackageLevelI()
}

func printPackageLevelI() {
	fmt.Println(i)	
}

/*
21
42
21
*/
```

### Note on unused variables
Unused variables produce compile time errors

### Visibility
There is only three levels of variable visibility in Go:
- Block scope - starts from **lowercase**, declared in the **block scope** (e. g. loop, condition or function). Visible only inside the block
- Package level - starts from **lowercase**, declared on **package** level. Visible from within the package.
- Globally visible - Starts from **uppercase**, declared on **package** level. Visible globally.

Lowercase variables are NOT visible outside the package!
```
var name string = "Rupert" // Not visible outside the package
``` 

To make variable visible outside of the package its name should start from uppercase letter

```
var VisibleOutsideVariable string = "Rupert"
```

### Naming convention
The length of the variable name should reflect its lifespan.
The longer the name is, the longer is the lifespan. 

```
var i, j int = 1, 2 // Good for local scope
var userSessionIdentifier = "34djferlj34ll3jl" // Good for longer lifespan variables
```

Acronyms are always typed uppercase:

```
var theHTTPS string = "https://fewlinesofcode.com"
```

Use camel case for naming:

```
numberOne :s= 1
var longNameVariable = "long name variable"
```

### Type conversions

Type conversions are performed using conversion function:

```
package main

import "fmt"

func main() {
	var i int = 42
	fmt.Printf("%v, %T\n", i, i)
	
	j := float32(i) // `float32` is conversion function
	fmt.Printf("%v, %T\n", j, j)
}

/*
42, int
42, float32
*/
```

**NB:** Type conversion may cause information loss

```
var i float32 = 42.5
j := int(i) // j now equals 42. We lost 0.5!
```

When converting to and from `strings` **use `strconv` package!**. Otherwise it will convert integer value to appropriate unicode character. This example prints `þ, string`, because `þ` is 254 in unicode.
```
k := string(254)
fmt.Printf("%v, %T\n", k, k)
```

Correct implementation is:
```
package main

import (
	"fmt"
	"strconv"
	)

func main() {
	var i int = 42
	fmt.Printf("%v, %T\n", i, i)
	
	j := float32(i)
	fmt.Printf("%v, %T\n", j, j)
	
	k := string(254)
	fmt.Printf("%v, %T\n", k, k)
	
	l := strconv.Itoa(254)
	fmt.Printf("%v, %T\n", l, l)
}

/*
42, int
42, float32
þ, string
254, string
*/
```

