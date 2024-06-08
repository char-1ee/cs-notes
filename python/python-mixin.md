# Mixin

A Mixin is a class provides a certain functionality to be used by other classed.&#x20;

```python
class LoggerMixin:
    def log(self, message):
        print(f"Log: {message}")
        
class Calculator:
    def add(self, x, y):
        return x + y
        
class LoggingCalculator(LoggerMixin, Calculator):
    def add(self, x, y):
        result = super().add(x, y)
        self.log("Adding")
        return result
        
calc = LoggingCalculator()
print(calc.add(4, 5))
```

Translation to Java:

```java
// Define an interface with a default method as a mixin
interface LoggerMixin {
    default void log(String message) {
        System.out.println("Log message: " + message);
    }
}

// Define a basic Calculator class
class Calculator {
    public int add(int x, int y) {
        return x + y;
    }
}

// Create a LoggingCalculator class that uses the mixin via interface
class LoggingCalculator extends Calculator implements LoggerMixin {
    @Override
    public int add(int x, int y) {
        int result = super.add(x, y);
        log("Adding " + x + " and " + y + " to get " + result);
        return result;
    }
}

// Main class to run the example
public class MixinExample {
    public static void main(String[] args) {
        LoggingCalculator calc = new LoggingCalculator();
        System.out.println(calc.add(4, 5));
    }
}
```

In Jave, interface default methods can provide a concrete implementation for an interface method. Such that it could be used as a Mixin method.

### Summary

Mixin in Python is born from the multiple inheritence OOP model, which is an independent class provides reusable functionality (e.g. log, timing, etc.), meanwhile no need to be instantiated. It is same as an interface with default methods (not static methods) in Java.
