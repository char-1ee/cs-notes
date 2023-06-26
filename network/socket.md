# Socket

A socket is an end point of a connection. Each socket has a corresponding socket address that consists of an Internet address and a 16-bit integer _port_ and is denoted by the notation `address:port`. For ports:

* Client -> ephemeral port (auto assigned)
* Server -> well-known port <-> well-known service name (http-80, smtp-25, `/etc/services`)

A connection is uniquely identified by the socket addresses of its two end points. This pair of socket addresses is known as a socket pair and is denoted by the tuple:

_(cliaddr : cliport, servaddr : serport)_

### Socket Interface Application

<figure><img src="../.gitbook/assets/image (3) (2) (1).png" alt=""><figcaption></figcaption></figure>

### Socket address structures

```c
/* IP socket address structure */
struct sockaddr_in {
    uint16_t sin_family; /* Protocol family (always AF_INET) */
    uint16_t sin_port; /* Port number in network byte order */
    struct in_addr sin_addr; /* IP address in network byte order */
    unsigned char sin_zero[8]; /* Pad to sizeof(struct sockaddr) */
};

/* Generic socket address structure (for connect, bind, and accept) */
struct sockaddr {
    uint16_t sa_family; /* Protocol family */
    char sa_data[14]; /* Address data */
};

/* IP address structure */
struct in_addr {
    uint32_t s_addr; /* Address in network byte order (big-endian) */
};
```

From the perspective of a Linux program, a socket is an open file with a corresponding descriptor. Internet socket addresses are stored in 16-byte structures having the type `sockaddr_in`, shown in Figure 11.13. For Internet applications,

* the `sin_family` field is `AF_INET`(IPV4),
* the `sin_port` field is a 16-bit port number, and
* the `sin_addr` field contains a 32-bit IP address.&#x20;

Note that to define socket interface functions accepting different protocol-specific socket address structure, we use sockaddr as a generic structure and then cast any pointers to protocol-specific structure to this generix structure.&#x20;

### Socket Interfaces

#### The `socket` Function

Clients and servers use the socket function to create a socket descriptor.&#x20;

```c
#include <sys/types.h>
#include <sys/socket.h>

int socket(int domain, int type, int protocol); 
// Returns non-negative descriptor if OK, -1 on error.
```

Make the socket an end point of socket connection:

```c
clientfd = Socket(AF_INET, SOCK_STREAM, 0);
```

#### The `connect` Function

A client establishes a connection with a server by calling the connect function.

```c
#include <sys/socket.h>

int connect(int clientfd, const struct sockaddr *addr, socklen_t addrlen);
// Returns: 0 if OK, −1 on error
```

#### The `bind` Function

`bind` function asks the kernel to associate the server's socket address in `addr` with the socket descriptor `sockfd.`

```c
#include <sys/socket.h>

int bind(int sockfd, const struct sockaddr * addr, socklen_t addrlen);
// Returns 0 if OK, -1 on error
```

#### The `listen` Function

The `listen` function converts `sockfd` from an active socket to a listening socket that can accept connection requests from clients.

```c
#include <sys/socket.h>

int listen(int sockfd, int backlog);
// Returns 0 if OK, -1 on error
```

#### The `accept` Function

Servers wait for connection requests from clients by calling the accept function.

```c
#include <sys/socket.h>

int accept(int listenfd, struct sockaddr *addr, int *addrlen);
// Returns: nonnegative connected descriptor if OK, −1 on errorsocet 
```

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

### Host and Service Conversion

#### The `getaddrinfo` Function

The `getaddrinfo` function converts string representations of hostnames, host addresses, service names, and port numbers into socket address structures. (host and service, the two components of a socket address)

```c
#include <sys/types.h>
#include <sys/socket.h>
#include <netdb.h>

int getaddrinfo(const char *host, const char *service, const struct addrinfo *hints, struct addrinfo **result);
// Returns: 0 if OK, nonzero error code on error

void freeaddrinfo(struct addrinfo *result);
// Returns: nothing

const char *gai_strerror(int errcode);
// Returns: error message
```

* The `host` argument to getaddrinfo can be either a domain name or a numeric address (e.g., a dotted-decimal IP address).&#x20;
* The `service` argument can be either a service name (e.g., http) or a decimal port number.

<figure><img src="../.gitbook/assets/image (4) (2).png" alt=""><figcaption></figcaption></figure>

Given host and service (the two components of a socket address), getaddrinfo returns a result that points to a linked list of addrinfo structures, each of which points to a socket address structure that corresponds to host and service.

After a client calls getaddrinfo, it walks this list, trying each socket address in turn until the calls to socket and connect succeed and the connection is established. Similarly, a server tries each socket address on the list until the calls to socket and bind succeed and the descriptor is bound to a valid socket address.

The `addrinfo` structure used by `getaddrinfo`

```c
struct addrinfo {
    int ai_flags; /* Hints argument flags */
    int ai_family; /* First arg to socket function */
    int ai_socktype; /* Second arg to socket function */
    int ai_protocol; /* Third arg to socket function */
    char *ai_canonname; /* Canonical hostname */
    size_t ai_addrlen; /* Size of ai_addr struct */
    struct sockaddr *ai_addr; /* Ptr to socket address structure */
    struct addrinfo *ai_next; /* Ptr to next item in linked list */
};
```

NOTATION! One of the elegant aspects of `getaddrinfo` is that the fields in an `addrinfo` structure are opaque, in the sense that they can be passed directly to the functions in the sockets interface without any further manipulation by the application code. For example, `ai_family`, `ai_socktype`, and `ai_protocol` can be passed directly to socket. Similarly, `ai_addr` and `ai_addrlen` can be passed directly to `connect` and `bind`. _**This powerful property allows us to write clients and servers that are independent of any particular version of the IP protocol.**_

#### The `getnameinfo` Function

The `getnameinfo` function is the inverse of getaddrinfo. It converts a socket address structure to the corresponding host and service name strings.

```c
#include <sys/socket.h>
#include <netdb.h>

int getnameinfo(const struct sockaddr *sa, socklen_t salen, char *host, size_t hostlen, char *service, size_t servlen, int flags);
// Returns: 0 if OK, nonzero error code on error
```

<figure><img src="../.gitbook/assets/image (1) (3) (1).png" alt=""><figcaption></figcaption></figure>

