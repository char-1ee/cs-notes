---
description: Collection of practices writing API in go
---

# API Practices in Go

## Set API timeout

Using _channel_ and _select_ statement to achieve timeout. Remember to create worker goroutine to process business logic.

```go
func readDB() string {
	time.Sleep(300 * time.Millisecond)
	return "ok"
}

func homeHandler(w http.ResponseWriter, req *http.Request) {
	var resp string
	done := make(chan struct{}, 1) // set buffer size to 1 in case memory leak
	go func() {
		resp = readDB()
		done <- struct{}{}
	}()

	select {
	case <-done:
	case <-time.After(100 * time.Millisecond):
		resp = "timeout"
	}
	w.Write([]byte(resp))
}

func main() {
	http.HandleFunc("/", homeHandler)
	http.ListenAndServe("127.0.0.1:8070", nil)
}
```

## Calculate API response time

Noticed that the defer statement here must be wrapped in a function, otherwise time.Since(t0) will be executed instantly.

{% code lineNumbers="true" %}
```go
// API handler
func handler(a, b int) string {
    t0 := time.Now()
    defer func() {
        fmt.Printf("API use time %d ms\n", time.Since(t0).Millisecond()) 
    }
    if a > b {
        time.Sleep(100 * time.Millisecond())
        return "ok"
    } else {
        time.Sleep(200 * time.Millisecond())
        return "ok"
    }
}

func main() {
    handler(3, 4)
}
```
{% endcode %}

## Limit API call rates

Balance high IO throughput of DB side by limiting API rate from server side: using an atomic counter and channel.

* When starting a new IO, counter++, add a new empty element into channel
* When an IO end, counter--., remove an element from channel
* Set a limitation of concurrency of 10 using channel, the goroutine is blocked until the channel is not full.

```go
var counter int32
var limit = make(chan struct{}, 10)

// Simulate DB IO
func readDB() string {
    atomic.AddInt32(&counter, 1)
    fmt.Printf("Read DB concurrency: %d\n", atomic.LoadInt32(&counter))
    time.Sleep(200 * time.Millisecond)
    atomic.AddInt32(&counter, -1)
    return "ok"
}

func handler() {
    limit<-struct{}{}
    readDB()
    <-limit
    return
}

func main() {
    // Simulate high concurrent IO throughput
    for i := 0; i < 100; i++ {
        go handler()
    }
    time.Sleep(3 * time.Second)
}
```
