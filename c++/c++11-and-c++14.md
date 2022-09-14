---
description: Example code snippet about a part of featured C++11 and C++14
---

# C++11 and C++14

### 1. Strongly Typed Enums

```cpp
enum class CoordinateArea {
    FirstArea, SecondArea, ThirdArea, FourthArea
};

CoordinateArea caOne = CoordinateArea::FirstArea;
CoordinateArea caSome = CoordinateArea::FourthAreal
```

### 2. Lamba Expressions of Lambda Functions

```cpp
[firstPart](secondPart) TypeYouReturn{ BodyOfLambda }(acctualParameters);
```

firstPart is used for variable scope which will be exploited inside the lambda function. You could use more variables if have a need for that.

Keep the following in mind when you are using this syntax for the \[firstPart]:

* \[] it means that you will not provide anything to give to lambda.
* \[&] it is used to say that you have some references to mess with.
* \[=] it is used to make the copy.
* \[this] is used to the enclosing class.

secondPart is necessary for parameter list for nameless function but it could be left empty as well.

TypeYouReturn is used to register what type will be returned from your lambda.

BodyOfLambda is used for actions you wish to do, in here you will type some code that will be used to perform actions you are intended to apply in the body of this function.

actualParameters are used to provide input into lambda function.

```cpp
double dUpperPart = [](double dX, double dY)double{ return dX*dX +dY*dY;} 
```

```cpp
vectror<int> iVector;
for_each(begin(iVector), end(iVector), [](int n){ if(n%2==0) cout << n << end;});
```

### 3. Static Assertions

```
static_assert(evaluatedExpression, stringMessage);
```

```cpp
static_assert(sizeof(long long int)>=16;”This is unexpected”);
```

### 4. Move and &&

For this, we need to understand lvalue and rvalue.

l stands for left side. It is one of the basic properties lvalue has it is an object in memory that could be at the left side of expression; an example would be some variable.

r stands for right side. This will be located on the right side of expression and it should not be found on right side, the specimen for it is a constant.

In older standard we could use only lvalues as reference, now that has changed and you could use rvalue as reference as well. It is useful in situations where you need to copy some objects, the better solution for that is using the move semantics.

To apply the move constructor in class you would call it like this:

```cpp
MovableClass(MovableClass&&);
```

And, if you need move assignment it is called like this:

```cpp
MovableClass&& operator=(MovableClass&&); 
```

In the new standard, this is well supported and containers and algorithms use this move semantics with appropriate optimizations.

### 5. Pointers

First, is the replacement of `NULL` with `nullptr`. This means that your pointer is not holding an address but it has nothing to point to. It is something like you have variable that has zero value, but it is has some differences as well.

The next few things are new types of [smart pointers](https://www.thegeekstuff.com/2015/02/cpp-smart-pointers/): unique, shared and weak pointers. Let us discuss what are they used for.

_**unique\_ptr**_ is new feature of C++ that will enable you to protect ownership of some resource stored in the memory. If something has ownership it could not be shared, but it is movable. This means that you could transfer it to another unique pointer.

```cpp
unique_ptr<someType> suniquePtr(new someType(args));
...
uniquePtr.release();
```

_**shared\_ptr**_ as one could guess from the name is suitable for situations that require ownership of some resource in the memory to be shared.

```cpp
shared_ptr<someType> somePtr(new someType(args));
```

_**weak\_ptr**_ is enabling the access to something that might exist in memory, the access is granted if you have object that occupy the memory, and it is possible to delete that object, the necessary destructor is called upon if it has been used last time.

```cpp
weak_ptr<someType> weakPtr = somePtr;
```

### 6. Uniform Initialization and Initializer Lists

Older

```cpp
CSomeClass SomeObject( argument1, argument2);

vector <int> ourVector;
for(int i=0; i<5; ourVector.push_back(i++));
```

Change to&#x20;

```cpp
CSomeClass SomeObject = {argument1, argument2};

vector< int> ourVector={0,1,2,3,4,};
```

### 7. Inherited Constructor

<pre class="language-cpp"><code class="lang-cpp">class CChild: public CParent {
public:
<strong>    using CParent::CParent  
</strong>}    </code></pre>

### 8. Multi-threading in C++11

* If you wish to call the thread, which is a class in `std` namespace, you need to add some header. The appropriate is to add `#include <thread>`, or it might signed some other way.
* When you start your thread you could use: join, swap, detach, sleep for etc.
* If you try to protect some resource from other threads so that you have expected results now you should have different kinds of mutex that is added with its library: mutex, recursive mutex, timed mutex and recursive timed mutex.

### 9. Some examples

```cpp
// use few examples with array as a new container like: tuple or some other.
auto tuple = make_tuple("triangle", 't', 3, 4, 5);

// declare map that will have one element string and another is vector 
map<string, vector<int>> aMap;
```

```cpp
// vectors
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

int main(void) {
    vector<int> v = { 1, 2, 3, 4, 5 };
    
    for_each(begin(v), end(v), 
    [](int i) {
        cout<<n<<" is";
        if (n==0)     cout<<" zero";
        else if (n>0) cout<<" positive ";
        else          cout<<" negative "
        cout<<"number\n"; 
    });
}
```

### Reference&#x20;

[Introduction to C++11 and C++14 with Example Code Snippet](https://www.thegeekstuff.com/2016/02/c-plus-plus-11/)
