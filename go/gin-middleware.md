# Middleware with Gin

Middleware in microservices refers to the reusable tool code. Below is an example of middleware in  Gin framework.

```go
import (
	"fmt"
	"net/http"
	"time"
	"github.com/gin-gonic/gin"
)

var limitChan = make(chan struct{}, 10) //瞬间并发度限制为10

// 计时中间件
func timerMiddleware() gin.HandlerFunc {
	return func(ctx *gin.Context) {
		begin := time.Now()
		ctx.Next() //执行本handler后面所有的handlers
		fmt.Printf("use time %d ms\n", time.Since(begin).Milliseconds())
	}
}

// 限流中间件
func limitMiddleware() gin.HandlerFunc {
	return func(ctx *gin.Context) {
		limitChan <- struct{}{}
		fmt.Printf("concurrency %d\n", len(limitChan)) //打印瞬间并发度
		ctx.Next()
		<-limitChan
	}
}

// 业务Handler
func bizHandler(ctx *gin.Context) { //GIN框架默认为handler加了recover
	time.Sleep(100 * time.Millisecond)
	ctx.String(http.StatusOK, "高性能golang\n")
}

func main() {
	engine := gin.Default()
	// engine.Use(limitMiddleware())                                                  //全局中间件，应用到所有路径上
	// engine.Use(timerMiddleware())                                                  //Use()函数把中间件追加到handler chain的首部
	engine.GET("/1", timerMiddleware(), bizHandler)                                //局部中间件，针对某一个路径单独设置
	engine.GET("/2", timerMiddleware(), limitMiddleware(), bizHandler, bizHandler) //后面是不定长参数，handler chain
	engine.Run("127.0.0.1:5678")
}
```
