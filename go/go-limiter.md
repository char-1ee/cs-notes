---
description: golang.org/x/time/rate
---

# Go: Limiter

[token-bucket.md](../algorithm/token-bucket.md "mention")

```go
import (
    "sync/atomic"
    "golang.org/x/time/rate"
)

var totalQuery int 32

// Server side handler
func handler() {
    atomic.AddInt32(&totalQuery, 1)
    time.Sleep(50 * time.Millisecond)
}

// Call handler 
func caller() {
    limiter := rate.NewLimiter(rate.Every(100 * time.Millisecond), 1)
    for {
        limiter.WaitN(context.Background(), 1) // block if no token in bucket
        handler()
    }
}

func main() {
    go caller()
    for i := 0; i < 5; i++ {
        time.Sleep(time.Second)
        fmt.Printf("%d calls are made in past 1 second\n", atomic.LoadInt32(&totalQuery))
        atomic.StoreInt32(&totalQuery, 0) // zero it persecond
    }
}
```
