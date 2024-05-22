# Proxy Pattern

* Provides an object that acts as a substitue for a real service object used by a client.&#x20;
* A proxy receives client requests, does some work (access control, caching, etc.) and then passes the request to a service object.
* So the proxy object has the same inferfaces as the real services (pretending it is the real services to clients).

### TL;DR

```python
from abs import ABC, abstractmethod

class Subject(ABC):
    @abstractmethod
    def request(self) -> None:
        pass
        
class RealSubject(Subject):
    def request(self) -> None:
        print("RealSubject: handling request")
        
class Proxy(Subject):
    def __init__(self, real_subject: RealSubject) -> None:
        self._real_subject = real_subject
        
    def request(self) -> None:
        if self.check_access():
            self._real_subject.request()
            self.log_access()
            
    def check_access(self) -> bool:
        print("Proxy: Checking access prior to firing a real request.")
        return True 
        
        
# Client 
def client_code(subject: Subject) -> None:
    # ...
    subject.request()
    # ...


if __name__ == "__main__":
    print("Client: Executing the client code with a real subject:")
    real_subject = RealSubject()
    client_code(real_subject)

    print("Client: Executing the same client code with a proxy:")
    proxy = Proxy(real_subject)
    client_code(proxy)
```

## Background

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

You have a massive object that consumes a vast amount of system resources. You need it from time to time, but not always.

You could implement lazy initialization: create this object only when it’s actually needed. All of the object’s clients would need to execute some deferred initialization code. Unfortunately, this would probably cause a lot of code duplication.

<figure><img src="../.gitbook/assets/image (22).png" alt="" width="383"><figcaption><p>The proxy disguises itself as a database object. It can handle lazy initialization and result caching without the client or the real database object even knowing.</p></figcaption></figure>

The Proxy pattern suggests that you create a new proxy class with the same interface as an original service object. Then you update your app so that it passes the proxy object to all of the original object’s clients. Upon receiving a request from a client, the proxy creates a real service object and delegates all the work to it.

#### Benefits

If you need to execute something either before or after the primary logic of the class, the proxy lets you do this without changing that class. Since the proxy implements the same interface as the original class, it can be passed to any client that expects a real service object.

## Structure

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

Proxy class contains a reference to the service object. After proxy finishes its processing (e.g. lazy init, logging, access control, caching etc.), it passes the request to the servic object.&#x20;

Usually, proxy class manages the full lifecycle of their service objects.

## Applicabilty

1. **Virtual proxy -> lazy initialization.** When you have a heavyweight service object that wastes system resources by being always up, even though you only need it from time to time. Instead of creating the object when the app launches, you can delay the object’s initialization to a time when it’s really needed.
2. Protection proxy -> access control
3. Remote proxy -> local execution of a remote service.&#x20;
4. Logging/Caching proxy -> logging history, caching.
5. **Smart reference**. When you need to be able to dismiss a heavyweight object once there are no clients that use it. The proxy can keep track of clients that obtained a reference to the service object or its results. From time to time, the proxy may go over the clients and check whether they are still active. If the client list gets empty, the proxy might dismiss the service object and free the underlying system resources. The proxy can also track whether the client had modified the service object. Then the unchanged objects may be reused by other clients.
