# API

## Calculate API response time

Noticed that the defer statement here must be wrappered in a function, otherwise time.Since(t0) will be executed instantly.

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

## API rate limiting

Balance high IO throughput of DB side by limiting API rate from server side: using atomic counter and channel.

* When start a new IO, counter++, add a new empty element into channel
* When a IO end, counter--., remove an element from channel
* Set a limitation of concurrency of 10 using channel, the gorountine is blocked until the channel is not full.

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
