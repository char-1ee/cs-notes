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

