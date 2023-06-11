---
description: Calculate API response time
---

# API timing

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
