---
description: 空结构体的用处
---

# struct{}{}

```go
type EST struct {
}

func main() {
    var b EST
    var c struct{}
    fmt.Printf("b address %p size %d\n", &b, unsafe.Sizeof(b))
    fmt.Printf("c address %p size %d\n", &c, unsafe.Sizeof(c))
}
```

Empty struct's size is 0 and share same address (b and c)

#### Usage 1: set&#x20;

There is no built-in set in golang, but can use map to implement set, which only store Keys and set Values as empty structs.

```go
students := make(map[string]struct{}, 10)
students["S1"] = EST{}
students["S2"] = struct{}{}
// { "S1":{}, "S2":{} }
```

#### Usage 2: synchronize goroutines

{% code lineNumbers="true" %}
```go
teachers := make(chan struct{}, 0)
go func() {
    time.Sleep(3 * time.Second)
    fmt.Printf("current gorountine ends")
    teachers <- struct{}{}
}()
<- teachers
```
{% endcode %}

Empty struct is commonly used in channel for synchronization. When the main routine comes to line 7, it blocks because the channel teachers is empty. Only after 3s the goroutine finishes an empty struct is added into the channel, the it unblocks the main routine.

In convention, we choose struct{} to make a channel rather than int, string etc. because as we said above empty struct occupied 0 memory space.
