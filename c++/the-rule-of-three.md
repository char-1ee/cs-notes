---
description: What is Rule of Three in C++?
---

# The Rule of Three

### What is Rule of Three in C++?

> If you need to explicitly declare either
>
> * the destructor,
> * copy constructor,
> * copy assignment operator
>
> yourself, you probably need to explicitly declare all three of them.

### What is Rule of Five in C++11?

From C++11 on, an object has 2 extra special member functions: the move constructor and move assignment.

```cpp
class person {
private:
    std::string name;
    int age;

public:
    person(const std::string& name, int age);        // Ctor
    person(const person &) = default;                // 1/5: Copy Ctor
    person(person &&) noexcept = default;            // 4/5: Move Ctor
    person& operator=(const person &) = default;     // 2/5: Copy Assignment
    person& operator=(person &&) noexcept = default; // 5/5: Move Assignment
    ~person() noexcept = default;                    // 3/5: Dtor
}; 
```

### What is Rule of Zero?

The rule of 3/5 is also referred to as the rule of 0/3/5. The zero part of the rule states that you are allowed to not write any of the special member functions when creating your class.

### Conclusion

To avoid the concerns about the rule of 0/3/5, using tools provided by the standard library like: `std::vector`, `std::string`, `std::unique_ptr` and `std::shared_ptr`, effectively removing the need for custom destructors, move/copy constructors, move/copy assignment and default constructors.

Here is an example:

```cpp
class A {
private:
    std::string brandname;
    std::unique_ptr<B> b;
    std::vector<B> vec;
public:
    virtual void something(){} = 0;
};
```

rather than

```cpp
class A {
private:
    char *brandname;
    B* b; 
    vector<B*> vec; 
public:
    ~Account()
    {
        delete [] brandname;
        delete b;
    };
    something(){} = 0; //virtual function (reason #1: base class)
}
```

### References

* [What is The Rule of Three?](https://stackoverflow.com/questions/4172722/what-is-the-rule-of-three)
* [When do we need to define destructors?](https://stackoverflow.com/questions/22491174/when-do-we-need-to-define-destructors)
* [Wikipedia: Rule of three](https://en.wikipedia.org/wiki/Rule\_of\_three\_\(C%2B%2B\_programming\))
