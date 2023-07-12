# Go: panic & recover

Panic will happen when encountering runtime error. Panic will execute sequentially:

1. 逆序执行当前goroutine的defer链 (recover从这里介入)
2. 打印错误信息和调用栈堆
3. 调用 exit(2) 结束整个进程

服务端可能会因为runtime error 导致panic而shutdown整个进程，所以可以通过recover()来恢复进程和打印报错信息. Runtime error, for example, 处理客户端传入empty string 而导致divide by 0.

{% code title="gRPC based RPC service example" %}
```go
func (*mServer) SayHello(ctx context.Context, req *idl.HelloRequest) (*idl.HelloResponse, error) {
    defer func(){
        err := recover()
        if err != nil {
            fmt.Println(err)
        }
    }()
    // business logic
}
```
{% endcode %}

像Gin的框架中会在每个Handler当中默认集成recover.
