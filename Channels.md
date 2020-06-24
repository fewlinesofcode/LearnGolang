# Channels

Channels are designed to synchronize data between multiple goroutines. Channels are the pipes that connect concurrent goroutines. You can send values into channels from one goroutine and receive those values into another goroutine

## Basics

Use `make()` function to create the channel. Channels are strongly typed!

```
var wg = sync.WaitGroup{}

func main() {
	ch := make(chan int) // only sends and receives `int`
	wg.Add(2)
	go func() { // Receiving goroutine
		i := <-ch // Receive from channel
		fmt.Println(i)
		wg.Done()
	}()
	go func() { // Sending goroutine
		i := 42
		ch <- i // Send to channel
		i = 27  // Program will still print 42. This line does not affect, because channel accepts copy of the **value**
		wg.Done()
	}()
	wg.Wait()
}
```


The following example creates a **deadlock**, because the channel receives a value on line `ch <- 42`, but there is nothing that proceses this value after.
To the Sender routine will be paused forever, as there is also no place in the channel for another value (we are now working with one-place channel (unbuffered)).

```
var wg = sync.WaitGroup{}

func main() {
	ch := make(chan int)

	go func() { // Receiver
		i := <-ch
		fmt.Println(i)
		wg.Done()
	}()

	for j := 0; j < 5; j++ { // Spawns 10 goroutines (5 senders and 5 receivers)
		wg.Add(2)

		go func() { // Sender
			ch <- 42
			wg.Done()
		}()
	}

	wg.Wait()
}
```

## Restricting data flow

The following example show how data is sent both ways
1. Sending routine sends data to the channel (42)
2. Receiver receives 42 and prints it out
3. Receiver sends 27 to the channel
4. Sender prints value received from the channel (27)

So the data is flowing both ways, which might be wanted or unwanted behavior.

```
var wg = sync.WaitGroup{}

func main() {
	ch := make(chan int) // only sends and receives `int`
	wg.Add(2)
	go func() { // Receiving goroutine
		i := <-ch // Receive from channel
		fmt.Println(i)
		ch <- 27
		wg.Done()
	}()
	go func() { // Sending goroutine
		ch <- 42 // Send to channel
		fmt.Println(<-ch)
		wg.Done()
	}()
	wg.Wait()
}

/*
Prints:
42
27
*/
```

If we need to restrict data flow to only sending OR receiving do as in the following example. Like that we restrict either the sending or receiving ability of the channel.
Trying to send data to receive-only channel will result a compile-time error `invalid operation: ch <- 27 (send to receive-only type <-chan int)`


```
var wg = sync.WaitGroup{}

func main() {
	ch := make(chan int) // only sends and receives `int`
	wg.Add(2)
	go func(ch <-chan int) { // Receiving ONLY channel
		i := <-ch // Receive from channel
		fmt.Println(i)
		// ch <- 27 // Produces compile time error
		wg.Done()
	}(ch)
	go func(ch chan<- int) { // Sending ONLY channel
		ch <- 42 // Send to channel
		wg.Done()
	}(ch)
	wg.Wait()
}
```


## Buffered channels

- Buffered channel block sender side till receiver is available
- Buffered channel block receiver side till message is available
- Can decouple sender and receiver with buffered channels
	- `ch := make(chan int, 50) // Creates a Buffered channel with capacity of 50 values`
- Use buffered channels if sending and receiving has different load (frequency)

The following example creates a buffered channel with 50 values. But it won't work correct way because it is desined to receive only one value.


```
var wg = sync.WaitGroup{}

func main() {
	ch := make(chan int, 50) // Creates a Buffered channel with capacity of 50 values
	wg.Add(2)
	go func(ch <-chan int) { // Receiving ONLY channel
		i := <-ch // Receive from channel
		fmt.Println(i)
		wg.Done()
	}(ch)
	go func(ch chan<- int) { // Sending ONLY channel
		ch <- 42 // Send to channel
		ch <- 27 // NB! This value will NOT be printed!
		wg.Done()
	}(ch)
	wg.Wait()
}
```

To make previous example work we have to introduce somekind of looping behavior.

This example creates a for...range loop on the channel. Notice that we must **explicitly close** the channel to prevent deadlock, because of specifics of for...range loop on channels.

> **NB!** You cannot send messages to closed channel. Yet you cannot even check if channel is closed. You gonna have to use `panic-recover` mechanism to overcome it.

```
var wg = sync.WaitGroup{}

func main() {
	ch := make(chan int, 50) // Creates a Buffered channel with capacity of 50 values
	wg.Add(2)
	go func(ch <-chan int) { // Receiving ONLY channel
		// for i := range ch {
		// 	fmt.Println(i)
		// }
		// wg.Done()

		// As an alternative you can use this syntax:
		for { // infinite for loop
			if i, ok := <-ch; ok { // ok = is channel open
				fmt.Println(i)
			} else {
				break
			}
		}
	}(ch)
	go func(ch chan<- int) { // Sending ONLY channel
		ch <- 42
		ch <- 27
		close(ch) // Closes the channel
		wg.Done()
	}(ch)
	wg.Wait()
}

/*
Prints
42
27
*/
```

## Select statement

Go’s select lets you wait on multiple channel operations. Combining goroutines and channels with select is a powerful feature of Go

Each channel will receive a value after some amount of time, to simulate e.g. blocking RPC operations executing in concurrent goroutines.

We’ll use select to await both of these values simultaneously, printing each one as it arrives

```
package main

import (
    "fmt"
    "time"
)

func main() {

    c1 := make(chan string)
    c2 := make(chan string)

    go func() {
        time.Sleep(1 * time.Second)
        c1 <- "one"
    }()
    go func() {
        time.Sleep(2 * time.Second)
        c2 <- "two"
    }()

    for i := 0; i < 2; i++ {
        select {
        case msg1 := <-c1:
            fmt.Println("received", msg1)
        case msg2 := <-c2:
            fmt.Println("received", msg2)
        }
    }
}

/*
received one
received two

real    0m2.245s
*/
```

Note that the total execution time is only ~2 seconds since both the 1 and 2 second Sleeps execute concurrently.

`var doneCh = make(chan struct{})` this is specific channel that does NOT receive a message (`struct{}` is Zero memory allocation), but it can tell the FACT of receiving or sending message.
Use the following syntax to send message to this channel: `doneCh <- struct{}{}`

