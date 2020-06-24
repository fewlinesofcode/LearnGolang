# Interfaces

## Basics

```
func main() {
	var w Writer = ConsoleWriter{}
	w.Write([]byte("Hello, Interfaces!"))
}
```

Interfaces don't describe data. Interfaces describe behaviors
Instead of storing data, interfaces stores method definitions (like in most languages)

```
type Writer interface {
	Write([]byte) (int, error)
}
```

In Go lanng interfaces are implicitly implemented.
type ConsoleWriter struct{}

```
// This method satisfies the requirements of `ConsoleWriter` interface
func (cw ConsoleWriter) Write(data []byte) (int, error) {
	n, err := fmt.Println(string(data))
	return n, err
}
```

The purpose of `interfaces` is obviously a polymorphism.

### Naming convention
Interface's name should describe what this interface does.
If you have single method interface should have `er` like follows

```
type Execer interface { // ExecER, or ReadER!
	Exec(query string, args []Value) (Result, error)
}
```

We can also define interface on any kind of custom type.
You cannot modify an actual type, but you can decorate it with properties using `interfaces`

```
func main() {
	myInt := IntCounter(0)
	var inc Incrementer = &myInt
	for i := 0; i < 10; i++ {
		fmt.Println(inc.Increment())
	}
}

type Incrementer interface {
	Increment() int
}

type IntCounter int

func (ic *IntCounter) Increment() int {
	*ic++
	return int(*ic)
}
```

## Composing interfaces
Interfaces has a very little opinion. Single method interfaces are very common in GO. The reason is the power of interfaces comes also from the composability
See the following example:
```
func main() {
	var wc WriterCloser = NewBufferedWriterCloser()
	wc.Write([]byte("So, how is GoLang learning goes?"))
	wc.Close()

	// Prints
	// So, how
	// is GoLan
	// g learni
	// ng goes?

	// When you need to cast an interface and avoid `panic`, you can use (result, error) tuple technique

	r, ok := wc.(io.Reader)
	if ok {
		fmt.Println(r)
	} else {
		fmt.Println("Conversion failed")
	}
	// Prints
	// Conversion failed
}

type Writer interface {
	Write([]byte) (int, error)
}

type Closer interface {
	Close() error
}

//Composed from two interfaces
//Basically the whole point :)
type WriterCloser interface {
	Writer
	Closer
}

type BufferedWriterCloser struct {
	buffer *bytes.Buffer
}

func (bwc *BufferedWriterCloser) Write(data []byte) (int, error) {
	n, err := bwc.buffer.Write(data)
	if err != nil {
		return 0, err
	}
	v := make([]byte, 8)
	for bwc.buffer.Len() > 8 {
		_, err := bwc.buffer.Read(v)
		if err != nil {
			return 0, err
		}
		_, err = fmt.Println(string(v))
		if err != nil {
			return 0, err
		}
	}
	return n, nil
}

func (bwc *BufferedWriterCloser) Close() error {
	for bwc.buffer.Len() > 0 {
		data := bwc.buffer.Next(8)
		_, err := fmt.Println(string(data))
		if err != nil {
			return err
		}
	}
	return nil
}

func NewBufferedWriterCloser() *BufferedWriterCloser {
	return &BufferedWriterCloser{
		buffer: bytes.NewBuffer([]byte{}),
	}
}
```

#### Empty interface
It's the interface defined on the fly with no methods on it.
Can also be created usual way.
Acts line `Any` in Swift. For me it is not a good thing to use

```
var myObj interface{}
```

### 	Type switches
We can use type switching on the `interface` object. It will properly work with type inference.
It can be used as a part of a hack when we have to work with multiple heterogeneous objects.

```
func main() {
	var i interface{} = 0
	switch i.(type) {
	case int:
		fmt.Println("This is integer")
	case string:
		fmt.Println("This is string")
	default:
		fmt.Println("This is something else")
	}
}
```
## Implementing interfaces with values vs. pointers
An interface definition does not prescribe whether an implementor should implement the interface using a pointer receiver or a value receiver. When you are given an interface value, there’s no guarantee whether the underlying type is or isn’t a pointer
When implementing an interface, if value type is used, then the methods that implements this interface have all to have value receivers. When implementing 
using pointers then methods set for pointers will also access values. See the [Article about interfaces in GO](https://jordanorelli.com/post/32665860244/how-to-use-interfaces-in-go)

## Best practices

- Use many small interfaces. Large interfaces should be composed from the small ones. It is better then large monolithic interfaces
	- Single method interfaces are the most powerful interfaces
		e.g. `io.Reader, io.Writer, interface`

- Don't export interfaces for types that will be consumed. If you don't have specific reason to export the interface - don't do it. (It is somehow contradicts with other programming languages)
- Do export interfaces for types that will be used by package
- Design your functions and methods to receive interfaces whenever possible.
