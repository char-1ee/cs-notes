---
description: How to optimize if-else writing.
---

# If-else optimization

With the growth of logics, long nested if-else writing could be a burden for code extensibility and maintainability. There are a series of optimization schemes. In the language syntax level, we have approaches like

* Switch-case syntax, ternary conditional operator
* Optional (JDK 8+) for nullable checks
* Enum methods

In the logic/code structure level, we can do

* Early return in if-else blocks / remove unnecessary else
* Merges of condition expressions

While more practical ways based engineering practices:

* Table driven
* Factory pattern / strategy pattern
* [Rule engine](https://www.oracle.com/technical-resources/articles/javase/javarule.html) (evaluates the Rules and returns the result based on the input)
* [Chain of responsibility pattern](https://developer.aliyun.com/article/781471)&#x20;

## Table of contents

1. [Thoughts on optimizations](https://gist.github.com/char-1ee/6bef696136cfa5b7a0df07c8b2aa8892#thoughts-on-optimizations)
2. [Optimization schemes](https://gist.github.com/char-1ee/6bef696136cfa5b7a0df07c8b2aa8892#optimization-schemes)
   1. [Optional](https://gist.github.com/char-1ee/6bef696136cfa5b7a0df07c8b2aa8892#optional)
   2. [Table driven](https://gist.github.com/char-1ee/6bef696136cfa5b7a0df07c8b2aa8892#table-driven)
   3. [Enumeration](https://gist.github.com/char-1ee/6bef696136cfa5b7a0df07c8b2aa8892#enumeration)
   4. [Factory pattern](https://gist.github.com/char-1ee/6bef696136cfa5b7a0df07c8b2aa8892#factory-pattern)
3. [References](https://gist.github.com/char-1ee/6bef696136cfa5b7a0df07c8b2aa8892#references)

## Thoughts on optimizations

Assume there encounters a business logic like

```
if (a) {
   if (b)   f1(); 
   else     f2(); 
} else {
   if (b)   f3(); 
   else     f4(); 
}
```

If conditions `a` and `b` return integer type `0` and `1` to represent boolean `false` and `true`, we can easily using the return values as 2D array indexes, to replace above if-else code to

```
let array = [[f1, f2], [f3, f4]];
array[a][b]();
```

where stores function pointers in array. In this way, a 4-branch code block is refactored with a 2D table-driven approach.\
Actually what we did is encapsulate _a **complex** logic with a **basic** logic unit_ into _a **simpler** logic with a **more complex** logic unit_.\
That is a trade-off between readability and complexity.

Another example is

```java
if (x % 2 == 0) {
    printf("even");
} else {
    printf("odd");
}
```

We write as

```java
int arr[] = {"even", "odd"};
printf("%s", arr[x % 2]);
```

It is like we compress the logic of conditional checks to make code so-called \_\_elegant\_\_, but the cost is less information is exposed to whoever read your code. Fewer lines of code with higher complexity. So give much more considerations before writing 'table-driven' like code in engineering practices.

But optimizations are needed when nested if-else statements grow bigger, complex and unmanageable in nature. Under that premise, I summarized several useful optimization schemes in my stand.

## Optimization schemes

### Optional

[Optional](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Optional.html) is introduced in JDK 8. It can be used to reduce if-else on not-empty or null judgements.

Before optimization

```java
String str = "Hi I am Xingjian"; 
if (str != null) {    
    System.out.println(str); 
} else {     a
    System.out.println("Null"); 
} 
```

After optimization

```java
Optional<String> strOptional = Optional.of("Hi I am Xingjian");
strOptional.ifPresentOrElse(System.out::println, () -> System.out.println("Null"));
```

### Table driven

Before optimization

```java
if (param.equals(value1)) {     
    doAction1(someParams); 
} else if (param.equals(value2)) {     
    doAction2(someParams); 
} else if (param.equals(value3)) {     
    doAction3(someParams); 
}
```

After optimization

```java
// Function<T> used as the assignment target for a lambda expression or method reference.
Map<T, Function<T> action> conditionTable = new HashMap<>();

// Initialization
conditionTable.put(condition1, (someParams) -> { doAction1(someParams) });
conditionTable.put(condition2, (someParams) -> { doAction2(someParams) });
conditionTable.put(condition3, (someParams) -> { doAction3(someParams) });

// Apply: pass the condition as Key in table and pass parameters via apply().
conditionTable.get(condition).apply(someParams);
```

Table-driven method uses Array/HashMap/TreeMap/Set to establish reflections from a conditional check result to a resulting assignment/function/entry to a branching code block. In nature, table-driven is a trade-off between data structure complexity and logic complexity.

### Enumerations

Before optimization

```java
String orderStatusDes;
if (orderStatusDes == 0) {
    orderStatusDes = "Order not paid";
} else if (orderStatusDes == 1) {
    orderStatusDes = "Order paid";
} else if (orderStatusDes == 2) {
    orderStatusDes = "Shipped";
} /*...*/
```

After optimization

```java
public enum OrderStatusEnum {
    UN_PAID(0, "Order not paid"),
    PAIDED(1, "Order paid"),
    SENDED(2, "Shipped");
    /*...*/
    
    private int index;
    private String desc;
    
    public int getIndex() {
        return index;
    }
    
    public String getDesc() {
        return desc;
    }
    
    OrderStatusEnum(int index, String desc) {
        this.index = index;
        this.desc = desc;
    }
    
    OrderStatusEnum of(int orderStatus) {
        for (OrderStatusEnum temp : OrderStatusEnum.values()) {
            if (temp.getIndex() == orderStatus) {
                return temp;
            }
        }
        return null;
    }
}
```

Apply the enum in if-else block

```java
String orderStatusDes = OrderStatusEnum.of(orderStatus).getDesc();
```

Compared to last Table-Driven method, above Enumeration example is more likely a **value-to-value reflection**, which assigns a value to the return result according to the condition checks. Mostly it is because nature Enums in programming languages are value-based. So how Enumberation method is used in **value-to-function pattern**, aka what if after condition checks the program requires to execute a function in each branch?

Scenario

```java
public int calculate(int a, int b, String operator) {
    int res = Integer.MIN_VALUE;
    if ("add".equals(operator)) {
    	res = a + b;
    } else if ("multiply".equals(operator)) {
        res = a * b;
    } else if ("divide".equals(operator)) {
        res = a / b;
    } else if ("subtract".equals(operator)) {
        res = a - b;
    }
    return res;
}
```

After optimization

```java
public enum Operator {
    ADD {
        @Override
        public int apply(int a, int b) {
            return a + b;
        }
    },
    MULTIPLY {
        @Override
        public int apply(int a, int b) {
            return a * b;
        }
    },
    SUBTRACT {
        @Override
        public int apply(int a, int b) {
            return a - b;
        }
    }, 
    DIVIDE {
        @Override
        public int apply(int a, int b) {
            return a / b;
        }
    };
    public abstract int apply(int a, int b);
}
```

Then in the Calculator class, we define the method

```java
public int calculate(int a, int b, Operator operator) {
    return operator.apply(a, b);
}
```

This method of Enumerations benefits from the syntax sugar of Java Enums.

### Factory pattern

We can using the same scenario in Enumerations.

1.  Fecth out the repeated operation pattern as an interface

    ```java
    public interface Operation {
       int apply(int a, int b);
    }
    ```
2.  Implement the interfaces (strategy implementation)

    ```java
    public int AddOperation implements Operation {
       @Override
       public int apply(int a, int b) {
          return a + b;
       }
    }
    /*...*/
    ```
3.  Construct the Factory class

    ```java
    public class OperatorFactory {
       private static Map<String, Operation> operationMap = new HashMap<>();
       static {
          operationMap.put("add", new AddOperation());
          operationMap.put("subtract", new SubtractOperation());
          operationMap.put("multiply", new MultiplyOperation());
          operationMap.put("divide", new DivideOperation());
       } // store the strategies 
       
       // Returns instance of Operations according to given operator
       public static Operation getOperation(String operator) {
          return operationMap.get(operator);
       }
    }
    ```
4.  Query the factory

    ```java
    public int calculate(int a, int b, String operator) {
       Operation op = OperatorFactory.getOperation(operator);
       return op.apply(a, b);
    }
    ```

Factory pattern is widely used together with strategy pattern. It adds an abstraction layer on original if-else logics, which we can regard it as a full utilization of OOP concepts and engineering practices.

## Further reading

* [If-else and Branch Predictor](https://www.zhihu.com/question/441518636/answer/1701252133)
* [Optimize if-else with Tree-of-responsibility pattern](https://developer.aliyun.com/article/781471)

## References

* [Java (optimization 28) optimizing if else writing](https://programmer.help/blogs/java-optimization-28-optimizing-if-else-writing.html)
* [Java Enum Methods -return opposite direction enum](https://stackoverflow.com/questions/18883646/java-enum-methods-return-opposite-direction-enum)
* [Write elegant if-else in Spring and Java](https://www.zhihu.com/question/344856665/answer/1751270663)
* [How to Replace Many if Statements in Java](https://www.baeldung.com/java-replace-if-statements)
* [How to optimize a large number of if-else if-else expressions](https://stackoverflow.com/questions/44277807/how-to-optimize-a-large-number-of-if-else-if-else-expressions)
