---
description: A locked cache implemented with read write lock
---

# Locked Cache

```go
package localcache

import (
    "code.byted.org/gopkg/facility/cache"
    "sync"
)

type LockedCache {
    cache.Cache
    lockMap map[string]*sync.RWMutex
    sync.RWMutex
}

func New(/* params */) *LockedCache {
    c.Cache = cache.NewLRU(/* params */)
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

Client code

```go
func Foo(s string) ([]string, error) {
    mCache := localcache.New(/* cache config */)
    // ... -> cacheKey
    if v, ok := mCache.Get(cacheKey); ok {
        return v.([]string), nil 
    }
    mCache.LockByKey(cacheKey)
    defer mCache.UnLockByKey(cacheKey)
    if v, ok := mCache.Get(cacheKey); ok {
        return v.([]string), nil
    }
    
    // main logic ... -> vals
    
    mCache.Set(cacheKey, vals)
    return vals, nil
}
```

Noticed that we can also maintain a global object as our cache

```go
package localcache

import (
	"code.byted.org/gopkg/facility/cache"
	"sync"
)

var FilterCache LockedCache

func Init() {
	FilterCache.Cache = cache.NewLRU(10000, "filter_cache", true, func(name string, value interface{}, prefix string, tagkv map[string]string) {
		_ = metrics.EmitCounter(name, value, prefix, tagkv)
	})
	FilterCache.lockMap = map[string]*sync.RWMutex{}
}

func FilterCacheKey(ctx context.Context, bid, env, keyType string) string {
	return fmt.Sprintf(consts.FilterCacheKeyFmt, bid, env, keyType)
}

type LockedCache struct {
	cache.Cache
	lockMap map[string]*sync.RWMutex
	sync.RWMutex
}

func (c *LockedCache) LockByKey(cacheKey string) {
	l := c.getLockByKey(cacheKey)
	l.Lock()
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

func (c *LockedCache) UnLockByKey(cacheKey string) {
	l := c.getLockByKey(cacheKey)
	l.Unlock()
}
```

Then the client code needs some changes:

```go
func Bar(s string) ([]string, error) {
    cacheKey := localcache.FilterCacheKey(ctx, adapter.Bid, adapter.Env, mapKeyType)
        if v, ok := localcache.FilterCache.Get(cacheKey); ok {
            return v.([]string), nil
        }
	localcache.FilterCache.LockByKey(cacheKey)
	defer localcache.FilterCache.UnLockByKey(cacheKey)
	if v, ok := localcache.FilterCache.Get(cacheKey); ok {
	    return v.([]string), nil
	}
    
    // main logic ... -> vals
    
    localcache.FilterCache.Set(cacheKey, vals, 30*time.Minute)
    return vals, nil
}
```
