# Goroutines

## Creating goroutines
A goroutine is a lightweight thread of execution.
Most of the programming languages are using OS threads creating separate execution stack for each function. That's why in Java or C# we avoid creating threads.

Go is using so called green thread. There is an abstraction (actually a scheduler) allowing us to get some execution time on some of OS threads.
That's why goroutines are cheap, because they can start with very low stack space.

`go` keyword spawns new goroutine

The next example surprisingly prints nothing. The reason is that `sayHello()` routine didn't actually have any execution time.

```
func main() {
	go sayHello()
}

func sayHello() {
	fmt.Println("Hello!")
}
```

## Synchronization
For further understanding let's recall how closures work.

```
func main() {
	var msg = "Hello"
	go func(msg string) { // Best way to pass data to anonymous func is to pass as argument!
		fmt.Println(msg)
	}(msg)
	time.Sleep(100 * time.Millisecond) // Bad practice! Just for the exucational purpose!
}

func sayHello() {
	fmt.Println("Hello!")
}
```

### WaitGroups
To give the routine execution time we use another approach. `WaitGroup` is designed to synchronize multiple goroutines together. The following example has two goroutines: `main()` and anonymous function and synchronizes them together.

```
var wg = sync.WaitGroup{}

func main() {
	var msg = "Hello"
	wg.Add(1)
	go func(msg string) {
		fmt.Println(msg)
		wg.Done()
	}(msg)
	msg = "Goodbye!"
	wg.Wait()
}
```


In case when we have multiple threads using shared resources we need synchronization mechanism. In the following examples goroutines are actually racing against each other. This will produce different order of execution each time!


```
var wg = sync.WaitGroup{}

var counter = 0

func main() {
	// Spawns 20 goroutines
	for i := 0; i < 10; i++ {
		wg.Add(2)
		go sayHello() // one goroutine 
		go increment() // one more goroutine
	}
	wg.Wait()
	/*
	Prints complete mess which is different from run to run:
	Hello 3
	Hello 0
	Hello 2
	Hello 4
	Hello 5
	Hello 10
	Hello 8
	Hello 10
	Hello 5
	Hello 10
	*/
}

func sayHello() {
	fmt.Printf("Hello %v\n", counter)
	wg.Done()
}

func increment() {
	counter++
	wg.Done()
}
```


### Mutexes
`Mutex` is a basically a lock used to lock resources. The following example shows how to use Mutex to syncrhonise data. 

**NB!** This particular example is better because it Kills concurency. Works just for education purposes

```
var wg = sync.WaitGroup{}
var counter = 0
var m = sync.RWMutex{} // RW stands for read/write mutex.

func main() {
	for i := 0; i < 10; i++ {
		wg.Add(2)
		m.RLock()
		go sayHello()
		m.Lock()
		go increment()
	}
	wg.Wait()
}

func sayHello() {
	fmt.Printf("Hello %v\n", counter)
	m.RUnlock()
	wg.Done()
}

func increment() {
	counter++
	m.Unlock()
	wg.Done()
}
```

## Parallellism
By default go uses the number of cores your machine has.

Use `runtime.GOMAXPROCS(-1)` to print available processors cores without changing the value.

You can also set number of available threads `runtime.GOMAXPROCS(100)` will set max amount of threads to `100`

```
func main() {
	fmt.Println(runtime.GOMAXPROCS(-1))
}
```

Find the best value of threads for your application.

## Best practices

- Don't create goroutines in libraries you develop. Let consumer to decide how to control concurency
- When creating goroutine, know how it will end.
	- Avoid subtle memory leaks
- Check for race conditions at compile time (use `-race` option when running go program in terminal) 
	- `go run -race  goroutines.go` - prints compiler warining on race condition!
