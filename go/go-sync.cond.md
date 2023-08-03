# Go: sync.Cond

Goroutine synchronization. Similar to 'wait', 'notify', 'notifyAll' in Java.

Scenarios:&#x20;

* Upstream: run M routines to register new users, and add users into \`users\` collection
* Downstream: run N routines to listen \`users\`. Once the number of users reaches 100, it rewards the first H registered users, then end the routine.

```go
type User struct {
    RegTime time.Time // register time
    Point    int    // redeem points
}

var (
    users    []*User // collection of users
    mutex    sync.Mutex // lock when read users
    numListeners    int32 // number of listenings to user
    rewarded   bool        // whether gave points reward
)

const (
    H = 3
    M = 8
    N = 5
)

func Init() {
    users = make([]*User, 0, 500)
    mutex = sync.Mutex{}
    numListener = 0
    rewarded = false
}

// Reward first H registered users
func reward() {
    sort.Slice(users, func(i, j int) bool {
        return users[i].RegTime.Before(users[j].RegTime)
    })
    for _, user := range users[:H] {
        user.Point += 1
    }
}
```

Then the business mode code be like

```go
func Signal() {
    Init()
    downStreamOver = false
    
    // Upstream
    for i := 0; i < M; i++ {
        go func() {
            for {
                if downStreamOver {
                    break
                }
                mutex.Lock()
                users = append(users, &User{RegTime: time.Now()})
                mutex.Unlock()
                time.Sleep(time.Millisecond * time.Duration(rand.Intn(100)))
            }
        }()
    }
    
    // Downstream 
    wg := sync.WaitGroup{}
    wg.Add(N)
    for i := 0; i < N; i++ {
        go func() {
            defer wg.Done()
            for {
                mutex.Lock()
                if !rewarded {
                    atomic.AddInt32(&numListener, 1)
                    if len(users) >= 100 {
                        reward()
                        rewarded = true
                    }
                }
                mutex.Unlock()
                if rewarded {
                    break
                }
            }
        }
    }
    wg.Wait()
    downStreamOver = true
}
```

#### Implementation 1: signal with channel

To reduce the number of listerners to `users`, everytime upstream changes a user, there sends a data to the channel.

```go
func SignalWithChannel() {
    Init()
    downStreamOver = false
    ch := make(chan struct{}, 10 * N)
    
    // Upstream
    for i := 0; i < M; i++ {
        go func() {
            for {
                if downStreamOver {
                    break
                }
                mutex.Lock()
                users = append(users, &User{RegTime: time.Now()})
                mutex.Unlock()
                ch <- struct{}{} // send object to channel
                time.Sleep(time.Millisecond * time.Duration(rand.Intn(100)))
            }
        }()
    }
    
    // Downstream 
    wg := sync.WaitGroup{}
    wg.Add(N)
    for i := 0; i < N; i++ {
        go func() {
            defer wg.Done()
            for {
                <-ch // block until `users` changes
                mutex.Lock()
                if !rewarded {
                    atomic.AddInt32(&numListener, 1)
                    if len(users) >= 100 {
                        reward()
                        rewarded = true
                    }
                }
                mutex.Unlock()
                if rewarded {
                    break
                }
            }
        }
    }
    wg.Wait()
    downStreamOver = true
}
```

#### Implementation 2: signal with Cond

```go
func SignalWithCond() {
    Init()
    downStreamOver = false
    cond := sync.NewCond(&mutex) // initialize with a mutex
    
    // Upstream
    for i := 0; i < M; i++ {
        go func() {
            for {
                if downStreamOver {
                    break
                }
                mutex.Lock()
                users = append(users, &User{RegTime: time.Now()})
                mutex.Unlock()
                cond.Signal() // notify other goroutines that are waiting
                time.Sleep(time.Millisecond * time.Duration(rand.Intn(100)))
            }
        }()
    }
    
    // Downstream 
    wg := sync.WaitGroup{}
    wg.Add(N)
    for i := 0; i < N; i++ {
        go func() {
            defer wg.Done()
            for {
                mutex.Lock()
                
                // Wait内部会先执行mu.Unlock()，
                // 等接收到信号后再执行mu.Lock()，
                // 所以在调Wait()之前需要先上锁                
                cond.Wait() // blocking until notified
                
                if !rewarded {
                    atomic.AddInt32(&numListener, 1)
                    if len(users) >= 100 {
                        reward()
                        rewarded = true
                    }
                }
                mutex.Unlock()
                if rewarded {
                    break
                }
            }
        }
    }
    wg.Wait()
    downStreamOver = true
}
```

### Reference&#x20;

A friendly tut: [https://geektutu.com/post/hpg-sync-cond.html](https://geektutu.com/post/hpg-sync-cond.html)
