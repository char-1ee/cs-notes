---
description: FP paradigms in Go
---

# Functional Options

To overloadingly initialize struct/class, we can use `Config`, `Builder`, `Functional Options`

Given original struct

```go
type Server struct {
    Addr     string        // required
    Port     int           // required
    Protocol string        // not null, default
    Timeout  time.Duration // not null, default
    MaxConns int           // not null, default
    TLS      *tls.Config   // optional
}
```

Since Go doesnt support overload, a troublesome way to initialize a `Server`:&#x20;

```go
func NewDefaultServer(addr string, port int) (*Server, error) {
  return &Server{addr, port, "tcp", 30 * time.Second, 100, nil}, nil
}

func NewTLSServer(addr string, port int, tls *tls.Config) (*Server, error) {
  return &Server{addr, port, "tcp", 30 * time.Second, 100, tls}, nil
}

func NewServerWithTimeout(addr string, port int, timeout time.Duration) (*Server, error) {
  return &Server{addr, port, "tcp", timeout, 100, nil}, nil
}

func NewTLSServerWithMaxConnAndTimeout(addr string, port int, maxconns int, timeout time.Duration, tls *tls.Config) (*Server, error) {
  return &Server{addr, port, "tcp", 30 * time.Second, maxconns, tls}, nil
}
```

### Config

Encapsulate optional fields in struct into Config struct

```go
type Config struct {
    Protocol string
    Timeout  time.Duration
    Maxconns int
    TLS      *tls.Config
}

type Server struct {
    Addr string
    Port int
    Conf *Config
}

// Then constuct struct with only one method
func NewServer(addr string, port int, conf *Config) (*Server, error) {
    // code
}

// using default configuration
srv1, _ := NewServer("localhost", 9000, nil)
conf := Config{ Protocol: "tcp", Timeout: 60*time.Duration }
srv2, _ := NewServer("localhost", 9000, &conf)
```

### Builder

Wrap `Server` struct with `Builder` type

```go
type ServerBuilder struct {
    Server
}

func (sb *ServerBuilder) Create(addr string, port int) *ServerBuilder {
    sb.Server.Addr = addr
    sb.Server.Port = port
    return sb
}

func (sb *ServerBuilder) WithProtocol(protocol string) *ServerBuilder {
    sb.Server.Protocol = protocol
    return sb
}

func (sb *ServerBuilder) WithMaxConn(maxconn int) *ServerBuilder {
    sb.Server.MaxConns = maxconn
    return sb
}

func (sb *ServerBuilder) WithTimeOut(timeout time.Duration) *ServerBuilder {
    sb.Server.Timeout = timeout
    return sb
}

func (sb *ServerBuilder) WithTLS(tls *tls.Config) *ServerBuilder {
    sb.Server.TLS = tls
    return sb
}

func (sb *ServerBuilder) Build() *Server {
    return sb.Server
}

// Usage
sb := ServerBuilder{}
server, err := sb.Create("localhost", 8080)
    .WithProtocol("udp")
    .WithMaxConn(1024)
    .WithTimeOut(30 * time.Second)
    .Build()
```

### Functional Options

First define a function type

```go
type Option func(*Server)
```

Then define constructors with closure:

```go
func Protocol(p string) Option {
    return func(s *Server) {
        s.Protocol = p
    }
}
func Timeout(timeout time.Duration) Option {
    return func(s *Server) {
        s.Timeout = timeout
    }
}
func MaxConns(maxconns int) Option {
    return func(s *Server) {
        s.MaxConns = maxconns
    }
}
func TLS(tls *tls.Config) Option {
    return func(s *Server) {
        s.TLS = tls
    }
}
```

We call `MaxConns(30)` it will return a function to set up `maxconns` automatically.

Then we define `NewServer()` with a variable parameters `options`

```go
func NewServer(addr string, port int, options ...func(*Server)) (*Server, error) {
    
    // default configs
    srv := Server {
        Addr:     addr,
        Port:     port,
        Protocol: "tcp",
        Timeout:  30 * time.Second,
        MaxConns: 1000,
        TLS:      nil,
    }
    // set up configs
    for _, option := range options {
        option(&srv)
    }
    return &srv, nil
}
```

So, we can create Server object by passing functions

```go
s1, _ := NewServer("localhost", 1024)
s2, _ := NewServer("localhost", 2048, Protocol("udp"))
s3, _ := NewServer("0.0.0.0", 8080, Timeout(300*time.Second), MaxConns(1000))
```
