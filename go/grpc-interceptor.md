---
description: 拦截器
---

# gRPC: interceptor

Interceptor implements some reusable functionality that is added into microservices. Interceptor is implemented following a fixed function signature defined by gRPC framework and then called when new a gRPC client or a server.

Note that the interceptors in RPC client and RPC server wont affect each other. Server interceptor plays the major role while client interceptor is added for morality.

### Server

```go
import (
	"training_project/service/idl"
	grpc_middleware "github.com/grpc-ecosystem/go-grpc-middleware"
	grpc "google.golang.org/grpc"
)

var limitAtServer = make(chan struct{}, 10) 

// 计时+限流拦截器
func timerAndLimitInterceptor(ctx context.Context, req interface{}, info *grpc.UnaryServerInfo, handler grpc.UnaryHandler) (interface{}, error) {
	begin := time.Now()
	limitAtServer <- struct{}{}
	resp, err := handler(ctx, req)
	fmt.Printf("concurrency %d\n", len(limitAtServer))
	<-limitAtServer
	fmt.Printf("use time %d ms\n", time.Since(begin).Milliseconds())
	return resp, err
}

func timerInterceptor(ctx context.Context, req interface{}, info *grpc.UnaryServerInfo, handler grpc.UnaryHandler) (interface{}, error) {
	begin := time.Now()
	resp, err := handler(ctx, req)
	fmt.Printf("use time %d ms\n", time.Since(begin).Milliseconds())
	return resp, err
}

func lLimitInterceptor(ctx context.Context, req interface{}, info *grpc.UnaryServerInfo, handler grpc.UnaryHandler) (interface{}, error) {
	limitAtServer <- struct{}{}
	resp, err := handler(ctx, req)
	fmt.Printf("concurrency %d\n", len(limitAtServer))
	<-limitAtServer
	return resp, err
}

type MyServer struct{}

func (*MyServer) SayHello(ctx context.Context, request *idl.HelloRequest) (*idl.HelloResponse, error) {
	defer func() {
		err := recover()
		if err != nil {
			fmt.Println(err)
		}
	}()
	time.Sleep(100 * time.Millisecond)
	resp := &idl.HelloResponse{Score: int32(10 / len(request.Name))}
	return resp, nil
}

func main() {
	// 监听本地的5678端口
	lis, err := net.Listen("tcp", "127.0.0.1:5678")
	if err != nil {
		panic(err)
	}
	// 初始化server，并设置interceptor
	server := grpc.NewServer(
		// grpc.UnaryInterceptor(timerAndLimitInterceptor), //grpc.UnaryInterceptor只能使用一次，即server端只能会用一个拦截器
		grpc.UnaryInterceptor(grpc_middleware.ChainUnaryServer(
			timerInterceptor,
			lLimitInterceptor,
		)), //让grpc_middleware帮你把多个拦截器封装成一个拦截器
	)
	// 注册服务
	idl.RegisterHelloServiceServer(server, new(MyServer))
	// 启动服务
	err = server.Serve(lis)
	if err != nil {
		panic(err)
	}
} 
```

### Client

```go
var limitAtClient = make(chan struct{}, 10) //瞬间并发度限制为10

// 计时拦截器
func timerInterceptor(ctx context.Context, method string, req, reply interface{}, cc *grpc.ClientConn, invoker grpc.UnaryInvoker, opts ...grpc.CallOption) error {
	begin := time.Now()
	err := invoker(ctx, method, req, reply, cc, opts...)
	fmt.Printf("use time %d ms\n", time.Since(begin).Milliseconds())
	return err
}

// 限流拦截器
func limitInterceptor(ctx context.Context, method string, req, reply interface{}, cc *grpc.ClientConn, invoker grpc.UnaryInvoker, opts ...grpc.CallOption) error {
	limitAtClient <- struct{}{}
	err := invoker(ctx, method, req, reply, cc, opts...)
	fmt.Printf("concurrency %d\n", len(limitAtClient)) //打印瞬间并发度
	<-limitAtClient
	return err
}

func main() {
	// 连接到GRPC服务端
	conn, err := grpc.Dial("127.0.0.1:5678",
		grpc.WithInsecure(),
		grpc.WithChainUnaryInterceptor(timerInterceptor, limitInterceptor), //不定长参数，第一个拦截器在最外层，最后一个拦截器最靠近真实的业务调用
	)
	if err != nil {
		fmt.Printf("连接GRPC服务端失败 %v\n", err)
		return
	}
	defer conn.Close()

	client := idl.NewHelloServiceClient(conn)

	// 执行RPC调用并打印收到的响应数据，指定1秒超时
	ctx, cancel := context.WithTimeout(context.Background(), 1 * time.Second)
	defer cancel()
	resp, err := client.SayHello(ctx, &idl.HelloRequest{Name: "asb"})
	if err != nil {
		fmt.Printf("远程调用失败 %v\n", err)
		return
	}
	fmt.Printf("score is %d\n", resp.Score)
} 
```
