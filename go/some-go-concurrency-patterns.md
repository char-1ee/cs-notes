# Some Go concurrency patterns

#### 1. Basic channel

```go
// Pattern 1: basic channel
func basicChannelPattern() {
    ch := make(chan int, buffSize)

    // Producer
    go func() {
        ch <- data
        close(ch)
    }

    // Consumer
    for value := range ch {
        // Process data
    }
}
```

#### 2. Select for multiple channels

```go
// Pattern 2: Select for multiple channels
func selectPattern() {
    ch1 := make(chan int)
    ch2 := make(chan int)
    done := make(chan struct{})

    for {
        select {
        case v1 := <-ch1:
            // v1
        case v2 := <-ch2:
            // v2
        case <-done:
            return
        default:
            // When no channel is ready
        }
    }
}
```

#### 3. Worker pool

```go
// Pattern 3: Worker pool
func workPool(nWorkers int, jobs <-chan Job, results chan<- Result) {
    var wg sync.WaitGroup
    for i := 0; i < nWorkers; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            for job := range jobs {
                res := process(job)
                results <- res
            }
        }()
    }
    wg.Wait()
```

#### 4. Barrier synchronization

```go
// Pattern 4: Barrier synchronization 
func barrierSync() {
    var wg sync.WaitGroup
    barrier := make(chan struct{})

    // Launch goroutines
    for i := 0; i < n; i++ {}
    wg.Add(1)
    go func() {
        defer wg.Done()
        // Phase 1
        <- barrier // wait for signal
        // Phase 2
    }(0)

    close(barrier)
    wg.Wait()
}
```

Barrier is used to seperate the workflow into 2 parts, which mean the 2nd part can only start until 1st part complete.

```go
// All components must initialize before system starts
barrier := make(chan struct{})
go initDatabase(barrier)
go initCache(barrier)
go initAPI(barrier)
// Wait for all initializations
<-barrier
// Start system
```

#### 5. Pipeline

```go
// Pattern 5: Pipeline
func pipeline() {
    stage1 := func(in <-chan int) <-chan int {
        out := make(chan int)
        go func() {
            defer close(out)
            for v := range in {
                out <- process1(v)
            }
        }()
        return out
    }

    stage2 := func(in <-chan int) <-chan string {
        out := make(chan string)
        go func() {
            defer close(out)
            for v := range in {
                out <- process2(v)
            }
        }()
        return out
    }

    // Build the pipeline
    input := Input()
    output1 := stage1(Input)
    output2 := stage2(output1)
}
```
