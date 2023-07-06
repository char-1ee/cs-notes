# Pipeline

### Inspiration

{% code overflow="wrap" %}
```go
http.HandleFunc("/v1/hello", WithServerHeader(WithAuthCookie(hello)))
http.HandleFunc("/v2/hello", WithServerHeader(WithBasicAuth(hello)))
http.HandleFunc("/v3/hello", WithServerHeader(WithBasicAuth(WithDebugLog(hello))))
```
{% endcode %}

We can use a proxy function, decorator to combine them

{% code overflow="wrap" %}
```go
type HttpHandlerDecorator func(http.HandlerFunc) http.HandlerFunc 

func Handler(h http.HandlerFunc, decors ...HttpHandlerDecorator) http.HandlerFunc {
    for i := range decors {
        d := decors[len(decors) - 1 - i]
        h = d(h)
    }
    return h
}
```
{% endcode %}

Then we can use it as below

{% code overflow="wrap" %}
```go
http.HandlerFunc("/v4/hello", Handler(hello, WithServerHeader, WithBasicAuth, WithDebugLog))
```
{% endcode %}

### Build pipeline with Channel

First need a echo() to put array into a Channel and return this Channel:

```go
func echo(nums []int) <-chan int {
    out := make(chan int)
    go func() {
        for _, n := range nums {
            out <- n
        }
        close(out)
    }()
    return out
}
```

Then we can follow this pattern to create

```go
func sq(in <-chan int) <-chan int {
  out := make(chan int)
  go func() {
    for n := range in {
      out <- n * n
    }
    close(out)
  }()
  return out
}

func odd(in <-chan int) <-chan int {
  out := make(chan int)
  go func() {
    for n := range in {
      if n%2 != 0 {
        out <- n
      }
    }
    close(out)
  }()
  return out
}

func sum(in <-chan int) <-chan int {
  out := make(chan int)
  go func() {
    var sum = 0
    for n := range in {
      sum += n
    }
    out <- sum
    close(out)
  }()
  return out
}
```

Finally the client code can be written as:

```go
nums := []int{1, 2, 3, 4, 5, 6, 7}
for n := range sum(sq(odd(nums))) {
    fmt.Println(n)
}
```

which functions as same as

```bash
echo $nums | odd | sq | sum
```

We can also use a propxy function doing this:

```go
type EchoFunc func([]int) (<-chan int)
type PipeFunc func(<-chan int) (<-chan int)

func pipeline(nums []int, echo EchoFunc, pipeFuncs ...PipeFunc) <-chan int {
    ch := echo(nums)
    for i := range pipeFuncs {
        ch = pipeFuncs[i](ch)
    }
    return ch
}
```

Client code:

```go
nums := []int{1, 2, 3, 4, 5, 6, 7}
for n := range pipeline(nums, odd, sq, sums) {
    fmt.Println(n)
}
```

### Further reading

{% embed url="https://blog.golang.org/advanced-go-concurrency-patterns" %}

{% embed url="https://www.youtube.com/watch?v=f6kdp27TYZs" %}
