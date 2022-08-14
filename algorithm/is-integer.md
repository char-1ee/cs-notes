# Is integer

To check whether given x is an integer,

*   In Java, take the decimal part using `% 1` and then check if is `0`:

    ```java
    return (x + EPSILON) % 1 <= 2 * EPSILON; // caution on double type
    ```
* In C++, check by using `x - int(x) == 0`.
* In JavaScript, check by `Number.isInteger()`.
