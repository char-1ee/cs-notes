---
description: A locked cache implemented with read write lock
---

# Locked Cache

```go
import (
    "code.byted.org/gopkg/facility/cache"
    "sync"
)

type LockedCache {
    cache.Cache
    lockMap map[string]*sync.RWMutex
    sync.RWMutex
}

func (c *LockedCache) New() {
    c.Cache = cache.NewLRU(/* some params */)
    c.lockMap = map[string]*sync.RWMutex{}    
}

func (c *LockedCache) getLockByKey(cacheKey string) *sync.RWMutex {
    c.RLock()
    l, ok := c.lockMap[cacheKey]
    c.RUnlock()
    if !ok {
        c.Lock()
        defer c.Unlock()
        if l, ok := c.lockMap[cacheKey]; ok {
            return l
        }
        l = &sync.RWMutex{}
        c.lockMap[cacheKey] = l
    }
    return l
}

func (c *LockedCache) LockByKey(cacheKey string) {
    l := c.getLockByKey(cacheKey)
    l.Lock()
}

func (c *LockedCache) UnLockByKey(cacheKey string) {
    l := c.getLockByKey(cacheKey)
    l.Unlock()
}
```
