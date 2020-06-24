# Primitives

## Boolean type
Acts as usual. If not set - default value is `false`
```
var n bool // Initialized with `false`
var flag bool = false
a := true

return 1 == 1 
```

## Numeric types
For all numeric types zero value is `0`.
Numeric types are provided with common arithmetic operators like `+, -, *, /, %, += ...` and so on.

### Integers

**NB:** Integer division looses the information due to dropping the reminder!

#### Signed

Signed integer (`int`) is AT LEAST `int32`. Depending on the platform it can be `int64` or even `int128`

| Type | Description | min | max |
|:--- |:---- |:- |:- |
|`int8` | set of all signed 8-bit integers | -128 | 127 |
| `int16` | set of all signed 16-bit integers | -32768 | 32767 |
| `int32` | set of all signed 32-bit integers | -2147483648 | 2147483647 |
| `int64` | set of all signed 64-bit integers | | -9223372036854775808 | 9223372036854775807 |

`rune` is the alias for `int32`

#### Unsigned
| Type | Description | min | max |
|:--- |:---- |:- |:- |
| `uint8` | set of all unsigned 8-bit integers | 0 | 255 |
| `uint16` | set of all unsigned 16-bit integers | 0 | 65535 |
| `uint32` | set of all unsigned 32-bit integers | 0 | 4294967295 |
| `uint64` | set of all unsigned 64-bit integers | 0 | 18446744073709551615 |

`byte` is the alias for `uint8` 

#### Operations on integers 
```
package main

import (
	"fmt"
)

func main() {
	a := 10 // 1010
	b := 3  // 1011
	
	fmt.Println("Arithmetic operations")
	fmt.Println("a + b =", a + b) // a + b = 13
	fmt.Println("a - b =", a - b) // a - b = 7
	fmt.Println("a * b =", a * b) // a * b = 30
	fmt.Println("a / b =", a / b) // a / b = 3 // NB! Integer division!
	fmt.Println("a % b =", a % b) // a % b = 1
	
	
	fmt.Println("Bit operations")
	fmt.Println("a & b =", a & b)  //    AND 1010 & 1011 = 0010
	fmt.Println("a | b =", a | b)  //     OR 1010 | 1011 = 1011
	fmt.Println("a ^ b =", a ^ b)  //    XOR 1010 ^ 1011 = 1001 
	fmt.Println("a &^ b =", a &^ b) // ANDOR 1010 &^ 1011 = 0100 
	
	c := 8
	fmt.Println("Bit shifting")
	fmt.Println("c << 3 =", c << 3) // 2^3 * 2^3 = 2^6 = 64
	fmt.Println("c >> 3 =", c >> 3) // 2^3 / 2^3 = 2^0 = 1
}
```

### Floating point numbers
Floating point numbers are storing decimal numbers. 

| Type | Description | min | max |
|:--- |:---- |:- |:- |
| `float32` | set of all [IEEE-754](https://en.wikipedia.org/wiki/IEEE_754) 32-bit floating-point numbers | ±1.18E-38 | ±3.4E38 |
| `float64` | set of all IEEE-754 64-bit floating-point numbers | ±2.23E-308 | ±1.8E308 |

Floating point numbers has no `%` operation and Bit operations.

```
package main

import (
	"fmt"
)

func main() {
	a := 10.2
	b := 3.7
	fmt.Println("a + b =", a+b) // a + b = 13
	fmt.Println("a - b =", a-b) // a - b = 7
	fmt.Println("a * b =", a*b) // a * b = 30
	fmt.Println("a / b =", a/b) // a / b = 3 // NB! Integer division!
	
	n := 3.14 // 3.14 is implicitly initialised to float64
	fmt.Printf("%v, %T", n, n)
	n = 13.7e72 // 1.37e+73
	fmt.Printf("%v, %T", n, n)
	n = 2.1E14 // 2.1e+14
	fmt.Printf("%v, %T", n, n)
	
	/*
	a + b = 13.899999999999999
  a - b = 6.499999999999999
  a * b = 37.74
  a / b = 2.7567567567567566
  3.14, float641.37e+73, float642.1e+14, float64
	*/
}

```

### Complex numbers

| Type | Description |
|:--- |:---- |
| `complex64` | set of all complex numbers with `float32` real and imaginary parts |
| `complex128` | set of all complex numbers with `float64` real and imaginary parts |

Complex numbers has all the same operations as floating point numbers.

To decompose complex numbers to real and imaginary parts use `real(_)` and `imag(_)` functions.

Use `complex(_, _)` function to compose complex number as on example:

```
package main

import (
	"fmt"
)

func main() {
	// Decompose
	var n complex64 = 1 + 2i 
	fmt.Printf("%v, %T\n", real(n), real(n))
	fmt.Printf("%v, %T\n", imag(n), imag(n))
	
	// Compose
	var m complex128 = complex(5, 12) 
	fmt.Printf("%v, %T\n", m, m)	
}
/*
1, float32
2, float32
(5+12i), complex128
*/
```

## Text types

`string` stands for ANY `utf8` character. String is **immutable** sequence of bytes. 

String allows only concatenation and **readonly** access by zero based index. See the example:

```
package main

import "fmt"

func main(){
    var a = "Hello"
    var b = " world"
    var c = a + b // Concatenation

    var some = c[3] // 108, because 108 is a unicode for `l`
    fmt.Println(c, some)
}

// Hello world 108 
```

String can be converted to a byte slice (when we need bit representation of the string)

```
package main

import "fmt"

func main() {
	bs := []byte("Hello world")
	fmt.Println(bs) // [72 101 108 108 111 32 119 111 114 108 100]
}

```

`byte` is the alias for `uint8`  represents any `utf8` char
`rune` is the alias for `int32`  represents any `utf32` 

```
r := 'a' // Rune initialisation
```

Be careful when working with `rune` type and read docs.