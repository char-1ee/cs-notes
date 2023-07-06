---
description: FP semantics
---

# Map Reduce Filter

Map&#x20;

```go
func MapStrToStr(list []string. fn func(s string) string) []string {
    var res = []string{}
    for _, it := range list {
        res = append(res, fn(it))
    }
    return res
}

list := []string{"a", "b", "c"}
x := MapStrToStr(list, func(s string) string {
    return strings.ToUpper(s)
})
fmt.Printf("%v\n", x) // [A, B, C]
```

Reduce

```go
func Reduce(list []string, fn func(s string) string) []string {
    sum := 0
    for _, it := range list {
        sum += fn(it)
    }
    return sum
}

list := []string{"a", "b", "c"}
x := Reduce(list, func(s string) string {
    return len(s)
})
fmt.Printf("%v\n", x) // 3
```

Filter&#x20;

```go
func Filter(list []int, fn func(n int) bool) []string {
    var res := []int{}
    for _, it := range err {
        if fn(it) {
            res = append(res, it)
        }
    }
    return res
}

intset := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
out := Filter(intset, func(n int) bool {
    return n % 2 == 1
})
fmt.Printf("%v\n", out) // [1, 3, 5, 7, 9]
```

For a generic map-reduce semantic, using `reflect` + `interface{}` or generics in golang, refers to [https://coolshell.cn/articles/21164.html](https://coolshell.cn/articles/21164.html)

### Generics

Map

```go
func gMap[T1 any, T2 any] (arr []T1, fn func(T1) T2) []T2 {
    res := make([]T2, len(arr))
    for i, elem := range arr {
        res[i] = fn(elem)
    }
    return res
}

nums := []int {0, 1, 2, 3, 4, 5}
squares := gMap(nums, func(elem int) int {
    return elem * elem
})
```

Reduce

```go
func gReduce[T1 any, T2 any] (arr []T1, init T2, fn func(T2, T1) T2) T2 {
    res := init
    for i, elem := range arr {
        res = fn(res, elem)
    }
    return res
}

nums := []int {0,1,2,3,4,5,6,7,8,9}
sum := gReduce(nums, 0, func (result, elem int) int  {
    return result + elem
})
```

Filter

```go
func gFilter[T any] (arr []T1, in bool, fn func(T) bool) []T {
    res := []T{}
    for i, elem := range arr {
        choose := fn(elem)
        if (in && choose) || (!in && !choose) {
            res = append(res, elem)
        }
    }
    return res
}

func gFilterIn[T any] (arr []T, f func(T) bool) []T {
  return gFilter(arr, true, f)
}

func gFilterOut[T any] (arr []T, f func(T) bool) []T {
  return gFilter(arr, false, f)
}

nums := []int {0,1,2,3,4,5,6,7,8,9}
odds := gFilterIn(nums, func (elem int) bool  {
    return elem % 2 == 1
})
print(odds)
```

