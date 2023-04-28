---
description: Pure function paradigm in C++
---

# Functional functions

### Express function inputs

Pass by a r**eference-to-const** parameters (_const T&_). For some types, input can also come in by **values**, like primitive types of instance.

### Express function input-output params

C++ allows to modify inputs of a function. Such parameters are both input and output. The typical way to represent this is by **reference-to-not-const** (T&).

### Express function outputs

Function produces outputs through return type:

```cpp
Output f(const Input& input);
```

Old POSIX-like style is discouraged:

```cpp
void f(const Input& input, Output& output);
```

For the return-type-as-output style, the language provides 3 mechanisms for perfection.

#### Performance

> In C, returning by value sounded like folly, because it incurred a copy of objects, instead of copying pointers. But in C++ there are several language mechanisms that elide the copy when returning by value. For instance [Return Value Optimisation](https://www.fluentcpp.com/2016/11/28/return-value-optimizations/) (RVO) or move semantics do this. For example, returning any STL container by value would move it instead of copying it. And moving an STL container takes about as much time as copying a pointer. In many cases the compiler will do its best to elide the copy.

> Only when your profiler showed that a copy made during a return by value of a specific function is your bottleneck for performance, you could think of degrading your code by passing the output parameter by reference. And even then, you could still have other options (like facilitating RVO or implementing move semantics for the returned type).

#### Error handling

POSIX-like style is discouraged:

```cpp
bool f(const Input& input, Output& output);
```

Modern C++, use **optional:**

```cpp
boost::optional<Output> f(const Input& input); // boost
std::optional<Output> f(const Input& input); // C++17

auto output = f(input)
if (output) {
    // use *output 
}
```

**Multiple return types**

The cleanest solution to fix this and produce several outputs by return type, as the language stands today (< C++17), is defining a new structure grouping the outputs"

```cpp
struct Outputs {
   Output1 output1;
   Output2 output2;
};

Output f(const Input& input);
```

In C++11, a _quicker_ but less clean solution is to use tuples:

```cpp
std::tuple<Output1, Output2> f(const Input& input);

Output1 output1;
Output2 output2;
std::tie(output1, output2) = f(inputs);
```

As a final note, here is a syntax that will probably be integrated in C++17 to natively return multiple values, which called **Structured Bindings**:

```cpp
auto [output1, output2] = f(const Input& input); // return an std::tuple 
```

### Reference

{% embed url="https://www.fluentcpp.com/2016/11/22/make-your-functions-functional/" %}
source
{% endembed %}

{% embed url="https://en.cppreference.com/w/cpp/utility/optional" %}
std:optional
{% endembed %}
