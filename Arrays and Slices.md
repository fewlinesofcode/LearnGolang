# Arrays and Slices

## Arrays
Arrays in Go are **fixed size contiguous** blocks of memory and can only store one type of data. 

In GO arrays are **value types**! Be careful and make sure to use pointers when you need variables to point the same array!

```
a := [...]int{1, 2, 3}
b := a
b[1] = 5
// b and a are different now because `b` was copied
```

### Creation

Array can be created as follows:
```
package main

import (
	"fmt"
)

func main() {
	var a [5]int // Reserve capacity, fill with zeroes appropriate to the array type
	fmt.Println(a)
	
	var b = [3]int{1, 2, 3}  // Create with predefined values
	fmt.Println(b)
	
	c := [3]string{"a", "b", "c"}  // Short assignment
	fmt.Println(c)
	
	c := [...]string{"a", "b", "c"}  // Creates array large enough to hold data passed in initializer (3 elements in this case)
	fmt.Println(c)
	
	var twoD [2][3]int
    for i := 0; i < 2; i++ {
        for j := 0; j < 3; j++ {
            twoD[i][j] = i + j
        }
    }
    fmt.Println("2d: ", twoD)
}
```

### Working with arrays

Getting and setting values in Array is straightforward and no different from most other Programming languages:

```
var a [5]int
a[4] = 100

fmt.Println("Set:", a)
fmt.Println("Get:", a[4])

/*
set: [0 0 0 0 100]
get: 100
*/
```

To get length of an array use `len` function.
```
var a [5]int

fmt.Println("Length:", len(a))

// Length: 5
```

## Slices
Slices are a key data type in Go, giving a more powerful interface to sequences than arrays.

Slice is a projection of an Array and we can work with slices exactly as with arrays like `get`, `set` and `len(_)` (See “Working with arrays” section above)

Slices are **reference types**!

### Creation

Slice can be created alike array, but we don’t need to know the length of the slice in compile time.
```
	a := []int {1, 2, 3}
	fmt.Println(a)
	//> [1 2 3]
```

`make(_,_,_)` can also be used to create slices. Here we make a slice of strings of length 3 (initially zero-valued).

```
s := make([]string, 3, capacity)
```

Slice can also be created with initial values:

```
t := []string{"g", "h", "i"}
fmt.Println("dcl:", t)
```

Slices can be composed into multi-dimensional data structures. The length of the inner slices can vary, unlike with multi-dimensional arrays.
```
twoD := make([][]int, 3)
for i := 0; i < 3; i++ {
    innerLen := i + 1
    twoD[i] = make([]int, innerLen)
    for j := 0; j < innerLen; j++ {
        twoD[i][j] = i + j
    }
}
fmt.Println("2d: ", twoD)
```

### Working with arrays
Slice is a projection of underlying array. Slice has capacity. When capacity is reached, slice will allocate approximately current capacity * 2 memory, which may be slow!

```
b := []byte{'g', 'o', 'l', 'a', 'n', 'g'}
// b[1:4] == []byte{'o', 'l', 'a'}, sharing the same storage as b
```


When capacity is known always use it for the sake of performance.

Slices sub ranges can be accessed using **Half-open** ranges. 
The start and end indices of a slice expression are optional; they default to zero and the slice's length respectively:
```
// b[:2] == []byte{'g', 'o'}
// b[2:] == []byte{'l', 'a', 'n', 'g'}
// b[:] == b
This is also the syntax to create a slice given an array:

x := [3]string{"Лайка", "Белка", "Стрелка"}
s := x[:] // a slice referencing the storage of x
```

Slices can be modified using `append(_, _)` function.

**NB!** There are possible “gotchas” in deleting elements from the slice. Read [Slice intro](https://blog.golang.org/slices-intro) for more information.