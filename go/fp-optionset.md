---
description: Check Nil variables
---

# (FP) OptionSet

Given following generic utility types and functions

```go
import (	
    "k8s.io/apimachinery/pkg/util/sets"
    "k8s.io/klog/v2"
)

type Optional interface {
    IsNil() bool
}

type OptionSet struct {
    name    string
    optionals    []Optional
}

func NewOptionSet(name string, optionals ...Optional) *OptionSet {
    return &OptionSet{
        name:    name,
        optionals: append([]Optional{}, optionals...),
    }
}

// Checks if any optional values are nil
func (os *OptionSet) AnyNil() (bool, []int) {
    nilSet := sets.NewInt()
    for i, v := range os.optionals {
        if v.IsNil() {
            nilSet.Insert(i)
        }
    }
    anyNil := nilSet.Len() > 0
    return anyNil, nilSet.List()
}

// Executes a function if no values are nil
func (os *OptionSet) Then(f func()) *OptionSet {
    anyNil, keys := os.AnyNil()
    if anyNil {
        klog.Infof("optionset %s found these nils: %v", os.name, keys)
        return os
    }
    f()
    return os
}

// Executes a function if any values are nil
func (os *OptionSet) Else(f func()) *OptionSet {
    if anyNil, _ := os.AnyNil(); anyNil {
        f()
    }
}

func (os *OptionSet) ThenFinished(f func()) {
    os.Then(f)
}
```

We can see that the `OptionSet` pattern provides explicit Nil checking with exact indices where nil values occur. Also, the Then/Else functions provides a readable way to handle nil check cases. We can use Then/Else in a chain:

```go
optSet.Then(func() {
    fmt.Println("First Then")
}).Then(func() {
    fmt.Println("Second Then")
}).ThenFinished(func() {
    fmt.Println("Final operation")
})

// Or if you need Else:
optSet.Then(func() {
    fmt.Println("Operation if no nils")
}).Else(func() {
    fmt.Println("Operation if any nils")
})
```

Example usage:

```go
// Assume these members implemented IsNil() already.
optSet := NewOptionSet("startup",
    config,
    database,
    cacheService,
    authProvider,
)

optSet.Then(func() {
    startServer()
}).Else(func() {
    log.Fatal("Failed to initialize all required services")
})
```

Example 2:

```go
type DatabaseConfig struct {
    Host     string
    Port     int
    Username string
    Password string
}

func (dc *DatabaseConfig) IsNil() bool {
    return dc == nil || dc.Host == "" || dc.Username == "" // Custom nil check logic
}

type APIConfig struct {
    Endpoint string
    APIKey   string
}

func (ac *APIConfig) IsNil() bool {
    return ac == nil || ac.Endpoint == "" // Custom nil check logic
}

func initializeSystem(dbConfig *DatabaseConfig, apiConfig *APIConfig) {
    optSet := NewOptionSet("system-init", dbConfig, apiConfig)
    
    optSet.Then(func() {
        fmt.Println("All configurations are valid")
    }).Else(func() {
        anyNil, indices := optSet.AnyNil()
        fmt.Printf("Invalid configurations at indices: %v\n", indices)
    })
}
```

Additionally, we can define a struct `OptionPtr`

```go
type OptionPtr[T any] interface {
    IsNil() bool
    GetValue() *T
    Get() (*T, bool)
}

type optionPtr[T any] struct {
    val *T
}

func SomePtr[T any](v *T) OptionPtr[T] {
    if v == nil {
        return NonePtr[T]()
    }
    return &optionPtr[T]{v}
}

func NonePtr[T any]() OptionPtr[T] {
    return &optionPtr[T]{}
}
```

Then any objects wrapped by `OptionPtr` can be directly passed into `NewOptionSet()`
