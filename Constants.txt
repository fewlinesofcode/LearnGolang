# Constants

Constants must be assigned to be defined on compile time. You **cannot** assign runtime computed value to the constant.

Constants **can** be shadowed by other constants. 

## Naming conventions
Constants are named the same way as variables.
Start from lowercase if you don’t want it to be exported (visible outside a package)

```
const notExportedConst = 42 // Not exported
const ExportedConst = 3.14 // Exported
```

## Typed constants
Typed constants are created the same way as typed variables
```
const myConst float32 = 42
```

## Untyped constants
Untyped constants can be used in expression with the elements of alike type without type conversion and errors.

This example of untyped constant will compile and work:
```
const a = 42
var b float32 = 27
fmt.Printf("%v, %T\n", a + b, a + b) // 69, float32
```

If we try to do the same with variables, compiler will produce `type mismatch` error.
```
var a = 42
var b float32 = 27
fmt.Printf("%v, %T\n", a + b, a + b)
```

## Enumerated constants

#### Iota basic example
The `iota` keyword represents successive integer constants `0, 1, 2,…`
It resets to `0` whenever the word const appears in the source code,
and increments after each const specification.

```
const (
    c0 = iota
    c1 = iota
    c2 = iota
)
fmt.Println(c0, c1, c2) // "0 1 2"
```

This can be simplified to

```
const (
	c0 = iota
	c1
	c2
)
```

Here we rely on the fact that expressions are implicitly repeated in a paren­thesized const declaration – this indicates a repetition of the preceding expression and its type.

#### Start from one

To start a list of constants at 1 instead of 0, you can use iota in an arithmetic expression.

```
const (
    c1 = iota + 1
    c2
    c3
)
fmt.Println(c1, c2, c3) // "1 2 3"
```

#### Skip value

You can use the blank identifier to skip a value in a list of constants.

```
const (
    c1 = iota + 1
    _
    c3
    c4
)
fmt.Println(c1, c3, c4) // "1 3 4"
```

#### Complete enum type with strings [best practice]

Here’s an idiomatic way to implement an enumerated type:

- Create a new integer type,
- List its values using iota,
- Give the type a String function.
- Type Direction int

```
const (
    North Direction = iota
    East
    South
    West
)

func (d Direction) String() string {
    return [...]string{"North", "East", "South", "West"}[d]
}
```

In use:

```
var d Direction = North
fmt.Print(d)
switch d {
case North:
    fmt.Println(" goes up.")
case South:
    fmt.Println(" goes down.")
default:
    fmt.Println(" stays put.")
}
// Output: North goes up.
```

## Enumeration expressions
In Go, enumerated constants are created using the iota enumerator. Since iota can be part of an expression and expressions can be implicitly repeated, it is easy to build intricate sets of values.
```
type ByteSize float64

const (
    _ = iota // ignore first value by assigning to blank identifier
    KB ByteSize = 1 << (10 * iota)
    MB
    GB
    TB
    PB
    EB
    ZB
    YB
)
```

**NB!** Bitshift can be used to efficiently store multiple Boolean variables in one int.

```
const (
	isAdmin = 1 << iota
	isHQ
	canSeeFinancials
)

func main() {
	var roles byte = isAdmin | canSeeFinancials
	fmt.Printf(“%b\n”, roles)
	fmt.Printf(“Is Admin? %b\n”, isAdmin & roles)
}
// 101
// Is Admin? 1
```