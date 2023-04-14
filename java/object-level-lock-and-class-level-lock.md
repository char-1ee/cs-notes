---
description: 对象锁和类锁, synchronized and static synchronized
---

# Object-level Lock and Class-level Lock

### Object-level Lock

Or instance lock. We have three ways to use a instance lock:

#### Lock on a non-static variable in a instance

Since a non-static variable is only owned by this instance_,_ not shared by other instances, so the non-static variables declared in an instance can put in a synchronized block. The methods synchronize a same non-static field share a lock.

```java
private Object lock = new Object();

public void lockObjectField() {
    synchronized(lock) {
        // do something
    }
}
```

#### Lock on _this_

_this_ refers to the instance itself. So all methods using synchronized(this) share a same lock.

```java
public void lockThis() {
    synchronized(this) {
        // do something
    }
}
```

#### Lock on non-static method&#x20;

```java
public synchronzied void lockMethod() {
    // do something
}
```

### Class-level Lock

Class-level lock is shared by all threads because a class is stored in JVM methods area. At a constant time, only one thread can use a synchronzied method or block, no matter it is same instance or not.

#### Lock on static variable in class

```java
private static Object lock  = new Object();

public void lockStaticField() {
    synchronized(lock) {
        // do something
    }
}
```

#### Lock on static methods

```java
public static synchronized void lockMethod() {
    // do something
}
```

#### Lock xxx.class

Add a lock on certain class.

```java
public void lockClass() {
    synchronized(MyClass.class) {
        // do something
    }
}
```

### _synchronized(this)_ vs. _synchronized(MyClass.class)_

`MyClass.class` and `this` are different things, they are different references to different objects.

* `this` - is a reference to this particular instance of the class, and
* `MyClass.class` - is a reference to the `MyClass` description object.

These synchronization blocks differ in that the first will synchronize all threads that deal concretely with **this** instance of `MyClass`, and the second one will synchronize **all** threads independently of which object on which method was called.

### References

* [Zhihu](https://zhuanlan.zhihu.com/p/98145713)
* [Java Synchronized Block for .class](https://stackoverflow.com/questions/2056243/java-synchronized-block-for-class)
* [synchronized(this) vs synchronized(MyClass.class)](https://stackoverflow.com/questions/14495776/synchronizedthis-vs-synchronizedmyclass-class)
