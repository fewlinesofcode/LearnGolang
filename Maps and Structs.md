#  Maps and Structs

## Maps

### What are they?

Maps are Go representation of a `Dictionary` or associative array. It provides us with O(1) access to value reading and writing by Key. As usual, maps are NOT ordered collections. 

Maps are **Reference types**! Keep an eye on shared mutable state! 

### Creating
To create an empty map, use the builtin `make(map[key-type]val-type)` function

Key should be Equatable (be able to test for equality)

```
// Creates empty map with `string` key and `int` data
m := make(map[string]int)
```

Map can also be created with values using initializer syntax:

```
marks := map[string]int{"john": 13, "alice": 24}
fmt.Println("Marks:", marks)
```

### Manipulation
Map data can be accessed as in any other Programming languages - using `name[key] = val` syntax.

```
		someMap["key1"] = “hello”
    someMap["key2"] = “world”
```

Similar to `Array`, map has length. Map’s length can be found using `len(_)` function:

```
fmt.Println("len:", len(yourMap))
```

> If you access element by the key that doesn’t exist in the map, it will return you ==default ZERO value== appropriate to the map content type.

To check if element exists in the map use optional “comma-ok syntax”. It is somehow similar to Swift pattern matching, where first parameter is the `value` and the second - if element is actually present in the map. This syntax is for disambiguation zero value and non-present element.

```
value, isPresent := someMap["someKey"]
fmt.Println(value, isPresent)  
```

It is safe to delete elements from `map`. Use `delete` function for that purpose:

```
delete(yourMap, "keyWhosObjWillBeDeleted")
```


## Structs

### What are they?
Struct in go is a collection type representing collection of fields and a Type associated with it. Very similar to objects in  other programming languages. 

In GO Structs are handy when we have to unite multiple values under the same umbrella. 

Structs are **Value types**! They are copied. Use pointers if you want to modify the same struct:

```
otherRef = &existingStruct
otherRef.someField = “new Val” // Will also modify `existingStruct`
```

### Creating

Struct type can be created like this:

```
type person struct {
    name string
    age  int
}
```

To create particular struct we can use various options listed below. Omitted keys are zero valued.

```
bob := person{"Bob", 20}
```

It’s better to name the fields when initializing the struct:

```
alice := person{name: "Alice", age: 30}
```

Sometimes it’s handy to use anonymous structs (usually only in a very small scope) for short life structures.

```
someStruct := struct{name string}{name: “John”}
```

### Naming conventions

Exported struct type starts from Capital letter: `type SomeStruct struct ...`

Struct instance uses usual naming conventions for variables. Public (visible from outside the package) struct fields should start from capital letter. Otherwise - usual camel case from small letter.

```
// Not visible outside of a package
type person struct {
    name string // Not visible outside of a package
    Age  int // Visible outside of a package
}

// Visible outside of a package
type PublicPerson struct { 
    name string // Not visible outside of a package
    age  int // Not visible outside of a package
}
```

Structures are manipulated using dot syntax:
```
	// Access struct fields with a dot.
s := person{name: "Sean", age: 50}
fmt.Println(s.name)

// You can also use dots with struct pointers - the
// pointers are automatically dereferenced.
sp := &s
fmt.Println(sp.age)

// Structs are mutable.
sp.age = 51
fmt.Println(sp.age)
```

### Embedding
Embedding is a type of composition used in Go. It allows to embed one structure into another using specific syntax.

[Official documentation](https://golang.org/doc/effective_go.html#embedding)

### Tags
You can mark variables and list their requirements using tag syntax. It is possible then to use it using “reflect” framework. 

Here is an example from [this article](https://itnext.io/validating-struct-map-form-in-go-language-1f819b8596c7):

```
package main
import "fmt"
import "time"
import "github.com/gookit/validate"
// UserForm struct
type UserForm struct {
	Name     string    `validate:"required|minLen:7"`
	Email    string    `validate:"email"`
	Age      int       `validate:"required|int|min:1|max:99"`
	CreateAt int       `validate:"min:1"`
	Safe     int       `validate:"-"`
	UpdateAt time.Time `validate:"required"`
	Code     string    `validate:"customValidator"`
}
// CustomValidator custom validator in the source struct.
func (f UserForm) CustomValidator(val string) bool {
	return len(val) == 4
}
// Messages you can custom validator error messages. 
func (f UserForm) Messages() map[string]string {
	return validate.MS{
		"required": "oh! the {field} is required",
		"Name.required": "message for special field",
	}
}
// Translates you can custom field translates. 
func (f UserForm) Translates() map[string]string {
	return validate.MS{
		"Name": "User Name",
		"Email": "User Email",
	}
}
func main() {
	u := &UserForm{
		Name: "inhere",
	}
	
	v := validate.Struct(u)
	// v := validate.New(u)
	if v.Validate() { // validate ok
		// do something ...
	} else {
		fmt.Println(v.Errors) // all error messages
		fmt.Println(v.Errors.One()) // returns a random error message text
		fmt.Println(v.Errors.Field("Name")) // returns error messages of the field 
	}
}
```
