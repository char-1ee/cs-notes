# Error Handling

1. using _defer_ to close resources after throw up errors
2. using _Closure_ or _Struct_ to deal with long error logics
   1. A FP paradigm -> Fluent Interface
3. a standard method for error wrappers

### Cleanups with `defer`

```go
func Close(c io.Closer) {
    err := c.Close()
    if err != nil {
        log.Fatal(err)
    }
}

func main() {
    r, err := Open("a")
    if err != nil {
        log.Fatalf("error opening 'a'\n")
    }
    defer Close(r) // 使用defer关键字在函数退出时关闭文件。
  
    r, err = Open("b")
    if err != nil {
          log.Fatalf("error opening 'b'\n")
    }
    defer Close(r) // 使用defer关键字在函数退出时关闭文件。
}
```

### Consequent Error Check

```go
func parse(r io.Reader) (*Point, error) {
    var p Point
    if err := binary.Read(r, binary.BigEndian, &p.Longitude); err != nil {
        return nil, err
    }
    if err := binary.Read(r, binary.BigEndian, &p.Latitude); err != nil {
        return nil, err
    }
    if err := binary.Read(r, binary.BigEndian, &p.Distance); err != nil {
        return nil, err
    }
    if err := binary.Read(r, binary.BigEndian, &p.ElevationGain); err != nil {
        return nil, err
    }
    if err := binary.Read(r, binary.BigEndian, &p.ElevationLoss); err != nil {
        return nil, err
    }
}
```

Actually we can use Closure to abtract duplicates:

```go
func parse(r io.Reader) (*Point, error) {
    var p Point
    var err error
    read := function(data interface{}) {
        if err != nil {
            return
        }
        err = binary.Read(r, binary.BigEndian, data)
    }
    
    read(&p.Longitude)
    read(&p.Latitude)
    read(&p.Distance)
    read(&p.ElevationGain)
    read(&p.ElevationLoss)
    
    if err != nil {
        return &p, err
    }
    return &p, nil
}
```

Also, we can define a struct to contain error:

```go
type Reader struct {
    r     io.Reader
    err   error
}

func (r *Reader) read(data interface{}) {
    if r.err == nil {
        r.err = binary.Read(r.r, binary.BigEndian, data)
    }
}

func parse(input io.Reader) (*Point, error) {
    var p Point
    r := Reader{r: input}
    
    r.read(&p.Longitude)
    r.read(&p.Latitude)
    r.read(&p.Distance)
    r.read(&p.ElevationGain)
    r.read(&p.ElevationLoss)
    
    if r.err != nil {
        return nil, r.err
    }
    return &p, nil
}
```

while this method can be used in [Fluent Interface](https://martinfowler.com/bliki/FluentInterface.html)

```go
// 长度不够，少一个Weight
var b = []byte {0x48, 0x61, 0x6f, 0x20, 0x43, 0x68, 0x65, 0x6e, 0x00, 0x00, 0x2c} 
var r = bytes.NewReader(b)

type Person struct {
  Name [10]byte
  Age uint8
  Weight uint8
  err error
}
func (p *Person) read(data interface{}) {
  if p.err == nil {
    p.err = binary.Read(r, binary.BigEndian, data)
  }
}

func (p *Person) ReadName() *Person {
  p.read(&p.Name) 
  return p
}
func (p *Person) ReadAge() *Person {
  p.read(&p.Age) 
  return p
}
func (p *Person) ReadWeight() *Person {
  p.read(&p.Weight) 
  return p
}
func (p *Person) Print() *Person {
  if p.err == nil {
    fmt.Printf("Name=%s, Age=%d, Weight=%d\n",p.Name, p.Age, p.Weight)
  }
  return p
}

func main() {   
  p := Person{}
  p.ReadName().ReadAge().ReadWeight().Print()
  fmt.Println(p.err)  // EOF 错误
}
```

### Error Wrapper

Wrap error in a concrete error:

```go
type authError struct {
    errMsg string
    err error
}

func (e *authError) Error() string {
    return fmt.Sprintf("auth failed : %s, %v", e.errMsg, e.err)
}
```

A more standard method used in[ github.com/pkg/errors](https://github.com/pkg/errors): export errors from a unified interface `causer` by method `Cause()`

```go
type causer interface {
    Cause() error
}

func (e *authError) Cause() error {
    return e.err
}
```

These have been already standard in golang error handling, but we can borrow these ideas in other languages error handlings.
