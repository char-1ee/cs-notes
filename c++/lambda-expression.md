# Lambda expression

```cpp
[capture list] (parameter list) -> return type { function body }
```

Here is a brief description of each part of the syntax:

* **Capture list**: Specifies which variables from the enclosing scope the lambda function can access. This can be done by value, reference, or a combination of both. The capture list is optional, but if you don't need to capture any variables, you still need to include the empty pair of square brackets: `[]`.
* **Parameter list**: Specifies the parameters of the lambda function. This is the same as for a regular function.
* **Return type**: Specifies the return type of the lambda function. This is also optional, but if you don't specify it, the return type will be deduced by the compiler.
* **Function body**: Specifies the code that will be executed when the lambda function is called.

Here is a simple example of a lambda expression that adds two numbers and returns the result:

```cpp
int x = 10;
auto sum = [x](int a, int b) -> int { return a + b + x; };
int res = sum(2, 3);
```

### Capture list

1. **Value Capture**: This captures the value of a variable at the time the lambda is created. The syntax for value capture is `[var]`, where `var` is the name of the variable to be captured. For example, `[x]` captures the value of `x` at the time the lambda is created.
2. **Reference Capture**: This captures a reference to a variable at the time the lambda is called. The syntax for reference capture is `[&var]`, where `var` is the name of the variable to be captured. For example, `[&x]` captures a reference to `x`.
3. **Implicit Capture**: This captures all variables used in the lambda by value or reference, depending on their usage. The syntax for implicit capture is `[=]` for capturing by value, or `[&]` for capturing by reference. For example, `[=]` captures all variables used in the lambda by value, and `[&]` captures all variables by reference.

### Usages

```cpp
// sorting in descending order
sort(v.begin(), v.end(), [](int a, int b) { return a > b; });
```

```cpp
// Max heap 
priority_queue<int, vector<int>, greater<int>> maxHeap1;

priority_queue<int, vector<int>, function<bool(int, int)>>  maxHeap(
    [](int a, int b) { return a < b; }
);

bool comp(const int& a, const int& b) { return a < b; }
priority_queue<int, vector<int>, decltype(&comp)> maxHeap3;
```

### Performance against `inline`

In general, inline functions are faster than lambda expressions because they are compiled directly into the calling code, whereas lambda expressions require additional overhead for their creation and invocation, such as the creation and initialization of the closure object that captures the variables used in the lambda.

### Reference

{% embed url="https://learn.microsoft.com/en-us/cpp/cpp/lambda-expressions-in-cpp?view=msvc-170" %}
