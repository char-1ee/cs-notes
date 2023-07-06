---
description: Package Context in Golang
---

# Go: context

### Implementation

{% code overflow="wrap" %}
```go
type Context interface {
	Deadline() (deadline time.Time, ok bool)
	Done() <-chan struct{}
	Err() error
	Value(key any) any
}
```
{% endcode %}

1. Done()函数返回一个只读管道，且管道里不存放任何元素(struct{})，所以用这个管道就是为了实现阻塞
2. Deadline()用来记录到期时间，以及是否到期。
3. Err()用来记录Done()管道关闭的原因，比如可能是因为超时，也可能是因为被强行Cancel了。
4. Value()用来返回key对应的value，你可以想像成Context内部维护了一个map
5. 当调用Deadline()时，Deadline()关闭Done()的管道，然后Err()记录error

Golang提供了Background和TODO以获得Root Context, emptyCtx是接口中使用的变量。

{% code overflow="wrap" %}
```go
var (
        background = new(emptyCtx)
        todo       = new(emptyCtx)
)
func Background() Context {
        return background
}
func TODO() Context {
        return todo
}
```
{% endcode %}

### WithValue

当业务逻辑比较复杂，函数调用链很长时，参数传递会很复杂，如下图：

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

f1产生的参数b要传给f2，虽然f2并不需要参数b，但f3需要，所以b还是得往后传。

如果把每一步产生的新变量都放到Context这个大容器里，函数之间只传递Context，需要什么变量时直接从Context里取，如下图：

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

{% code overflow="wrap" %}
```go
func step1(ctx context.Context) context.Context {
	// 根据父context创建子context，创建context时允许设置一个<key,value>对，
	// key和value可以是任意数据类型
	child := context.WithValue(ctx, "name", "大脸猫")
	return child
}

func step2(ctx context.Context) context.Context {
	fmt.Printf("name %s\n", ctx.Value("name"))
	// 子context继承了父context里的所有key value
	child := context.WithValue(ctx, "age", 18)
	return child
}

func step3(ctx context.Context) {
	fmt.Printf("name %s\n", ctx.Value("name")) //取出key对应的value
	fmt.Printf("age %d\n", ctx.Value("age"))
}

func main() {
	grandpa := context.Background() //空context
	father := step1(grandpa)        //father里有一对<key,value>
	grandson := step2(father)       //grandson里有两对<key,value>
	step3(grandson)
}
```
{% endcode %}

### Timeout

通过context.WithCancel创建一个context，显式地调用cancel()时会关闭context.Done()管道

```go
func f1() {
	ctx, cancel := context.WithCancel(context.Background())
	go func() {
		time.Sleep(100 * time.Millisecond)
		cancel() //调用cancel，触发Done
	}()
	select {
	case <-time.After(300 * time.Millisecond):
		fmt.Println("未超时")
	case <-ctx.Done(): //ctx.Done()是一个管道，调用了cancel()都会关闭这个管道，然后读操作就会立即返回
		err := ctx.Err()        //如果发生Done（管道被关闭），Err返回Done的原因，可能是被Cancel了，也可能是超时了
		fmt.Println("超时:", err) //context canceled
	}
}
```

通过context.WithTimeout创建一个context，当超过指定的时间或者调用cancel()时会关闭context.Done()管道

```go
func f2() {
	ctx, cancel := context.WithTimeout(context.Background(), time.Millisecond*100) //超时后会自动调用context的Deadline，Deadline会，触发Done
	defer cancel()
	
	select {
	case <-time.After(300 * time.Millisecond):
		fmt.Println("未超时")
	case <-ctx.Done(): //ctx.Done()是一个管道，context超时或者调用了cancel()都会关闭这个管道，然后读操作就会立即返回
		err := ctx.Err()        //如果发生Done（管道被关闭），Err返回Done的原因，可能是被Cancel了，也可能是超时了
		fmt.Println("超时:", err) //context deadline exceeded
	}
}
```

注意当一个empty channel被关闭时，会立即返回并且返回对应的zero value

### Example

Client设置一个2s的timeout

```go
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
	"time"
)

func main() {
	client := http.Client{
		Timeout: 2 * time.Second, //小于10秒，导致请求超时，会触发Server端的http.Request.Context的Done
	}
	if resp, err := client.Get("http://127.0.0.1:5678/"); err == nil {
		defer resp.Body.Close()
		fmt.Println(resp.StatusCode)
		if bs, err := ioutil.ReadAll(resp.Body); err == nil {
			fmt.Println(string(bs))
		}
	} else {
		fmt.Println(err) //Get "http://127.0.0.1:5678/": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
	}
}
```

服务端从Request里取提context，故意休息10秒钟，同时监听context.Done()管道有没有关闭。由于Request的context是2秒超时，所以服务端还没休息够context.Done()管道就关闭了

```go
package main

import (
	"fmt"
	"net/http"
	"time"
)

func welcome(w http.ResponseWriter, req *http.Request) {
	ctx := req.Context() //取得request的context
	select {
	case <-time.After(10 * time.Second): //故意慢一点，10秒后才返回结果
		fmt.Fprintf(w, "welcome")
	case <-ctx.Done(): //超时后client会撤销请求，触发ctx.cancel()，从而关闭Done()管道
		err := ctx.Err()            //如果发生Done（管道被关闭），Err返回Done的原因，可能是被Cancel了，也可能是超时了
		fmt.Println("server:", err) //context canceled
	}
}

func main() {
	http.HandleFunc("/", welcome)
	http.ListenAndServe(":5678", nil)
}
```
