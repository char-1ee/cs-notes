# Go: sync.RWMutex

### Main APIs

Write lock: Lock(), Unlock()

Read lock: RLock(), RUnlock()

* 在读占用的情况下，写操作的到来将读操作划分成了 已经占用锁的读操作和未占用读锁的读操作两部分；待已占用的读释放完全，再唤醒写；
* 在写占用的情况下，写操作释放锁时，唤醒写锁期间到来的读操作，然后再释放写锁；
* 将上面的状态转化连起来，推论：在读写高并发时，golang的实现就变为：写（1），读（n），写（1），读（j）……

### A dead lock case

```go
var mu2 sync.RWMutex
var count int

func main() {
	go A2()
	time.Sleep(2 * time.Second)
	mu2.Lock()
	defer mu2.Unlock()
	count++
	fmt.Println(count)
}
func A2() {
	mu2.RLock()
	defer mu2.RUnlock()
	B2()
}
func B2() {
	time.Sleep(5 * time.Second)
	C2()
}
func C2() {
	mu2.RLock()
	defer mu2.RUnlock()
}
```

### Reference

[https://zhuanlan.zhihu.com/p/454136281](https://zhuanlan.zhihu.com/p/454136281)
