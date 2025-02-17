# (FP) ErrorCounter

```go
package errors

import (
    "fmt"
    "k8s.io/apimachinery/pkg/util/errors"
)

type ErrorCounter interface {
    Error() error
    GetThreshold() int
    Update(error) error 
    Clear()
    Add(error) 
    Suspend()
}

type errorCounter struct {
    counter int    // How many errors have occured
    threshold int  // Maximum allowable errors
    errs []error    // History of errors
}

type ErrorCounterMatch struct {
    Try             func()    // Sucess case
    Catch           func()    // Error case
    OnExceedCounter func()    // Threshold exceeded case
}

func NewErrorCounter(th int) ErrorCounter {
    if th < 0 {
        th = 0
    }
    return &errorCounter{threshold: th}
}

// Return error if error counter exceeds threshold or nil
func (c *errorCounter) Error() error {
    if c.counter > c.threshold {
        return fmt.Errorf("error counter exceed %d/%d, err %v", c.counter, c.threshold, errors.NewAggregate(c.errs))
    }
    return nil
}

func (c *errorCounter) GetThreshold() int {
    return c.threshold
}

func (c *errorCounter) Update(err error) error {
    if err == nil {
        c.Clear()
        return nil
    } 
    c.Add(err)
    return c.Error()
} 

func (c *errorCounter) Clear() {
    c.counter = 0
    c.errs = nil
}

func (c *errorCounter) Add(err error) {
    if c.counter > c.threshold {
        return 
    }
    if err != nil {
        c.counter += 1
        c.errs = append(c.errs, err)
    }
}

func (c *errorCounter) Match(err error, match ErrorCounterMatch) {
    if err != nil {
        if match.Catch != nil {
            match.Catch()
        }
    } else {
        if match.Try != nil {
            match.Try()
        }
    }
    if exceedError := c.Update(err); exceedError != nil {
        if match.OnExceedCounter != nil {
            match.OnExceedCounter()
        }
    }
}

func (c *errorCounter) Suspend() {
    c.counter = c.threshold + 1
    c.errs = append(c.errs, fmt.Errorf("err counter is suspend"))
}
```

Common use cases

1. Circuit breaker pattern

```go
counter := NewErrorCounter(3)
// In a loop or recurring opration:
if err := opration(); err != nil {
    if exceedErr := counter.Update(err); exceedErr != nil {
        // Circuit is now open - stop trying\
        break
    }    
}
```

2. Retry with maximum attempts

```go
counter := NewErrorCounter(3)
match := ErrorCounterMatch {
    Try: func() {}
    Catch: func() {}
    OnExceedCounter: func() {}
}

for {
    err := someOperation()
    counter.Match(err, match)
}
```
