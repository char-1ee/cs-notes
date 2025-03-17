# Execute-once then run on interval

```go
func UpdateClusterDeployments(vdcs []string, wg *sync.WaitGroup) {
    ticker := time.NewTicker(time.Second * 60)
    once := make(chan struct{}, 1)
    once <- struct{}{}
    
    update := func() {
        // some code
    }
    
    for {
        select {
        case <-once:                    // First case: triggered immediately once
            update()
            wg.Done()                   // Signal completion to the WaitGroup
        case <-ticker.C:                // Second case: triggered every 60 seconds
            update()
        case <-time.After(time.Hour):   // Third case: prevents blocking
            // Oops need to check
        }
    }
}
```

1. **Immediate first execution**: the functions runs once immediately when called
2. **Periodic subsequent execution:** after the first run, it continues to execute ar regular intervals
