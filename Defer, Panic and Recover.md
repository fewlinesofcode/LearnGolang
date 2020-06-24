# Defer, Panic and Recover

## Defer
`defer` used to delay execution of a statement until function exits:

```
func main() {
	fmt.Println("start")
	defer fmt.Println("middle")
	fmt.Println("end")
}

/*
start
end
middle
*/
```

`defer` allows to associate resources opening and resource closing without forgetting to do it in the end of some long function's body.

Do not use `defer` in the loop (be careful at least). It may potentially introduce high resources usage. Remember that defer only fired when function is about to return.


Multiple `defer` statements run in LIFO order. There is the logic behind that, because `defer` is often used to free resources. LIFO prevents resources from being closed before their dependencies are closed.

```
func main() {
	defer fmt.Println("start")
	defer fmt.Println("middle")
	defer fmt.Println("end")
}

/*
end
middle
start
*/
```

Defer captures argument that is present at the moment of `defer` reached, NOT the value when `defer` is called. See the example:

```
func main() {
	a := "start"
	defer fmt.Println(a)
	a = "end"
}

//> start
```


## Panic and Recover
`panic` and `recover` are similar to `try-catch` in other languages. 

`panic` occur when the program cannot continue at all. Similar to `fatalError()` in Swift.

Panic is executed as soon as it is reached

```
func main() {
	fmt.Println("start")
	panic("something bad happened")
	fmt.Println("end") // Will not be printed
}
```

Function will stop executing immediately after `panic`. In the following example `panic` will make `defer` to execute, 
`defer` is called when the function stops working.

If nothing handles `panic` - program will exit!

```
func main() {
	fmt.Println("start")
	defer fmt.Println("this was deferred")
	panic("something bad happened")
	fmt.Println("end")
}

/*
start
this was deferred
*/
```

`defer` accepts not a function but a function call. `recover` can be specified to react on specific error.

`recover` *can be used to recover from panics*. Recovers are only useful in deferred functions. Function that `recover`s stops execution, but the program continues. Current function **will NOT attempt to continue**, but higher functions in the stack will.

If the error in the `recover` is such that program should stop execution, use `panic` again (commented line after `log.Println("Error", err)`)

See the following example:

```
func main() {
	fmt.Println("start")
	panicker()
	fmt.Println("end")
}

func panicker() {
	fmt.Println("about to panic")
	defer func() {
		if err := recover(); err != nil {
			log.Println("Error", err)
			// panic(err)
		}
	}()
	panic("something bad happened")
	fmt.Println("done panicking")
}

/*
start
about to panic
2020/05/27 00:52:38 Error something bad happened
end
*/
```
