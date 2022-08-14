# Initialize vector with value

In C++ 11:

```cpp
#include <vector>
using std::vector;
vector<int> v1 {10, 20, 30};
vector<int> v2 = {10, 20, 30};
```

Using Boost `list_of`:

```cpp
#include <vector>
#include <boost/assign/list_of.hpp>
using std::vector;

vector<int> v = boost::assign::list_of(10)(20)(30);
```

Using Boost assign:

```cpp
#include <vector>
#include <boost/assign/std/vector.hpp>
using std::vector;

vector<int> v;
v += 10, 20, 30;
```

Conventional STL:

```cpp
#include <vector>
using std::vector;

static const int arr[] = {10, 20, 30};
vector<int> v (arr, arr + sizeof(arr) / sizeof(arr[0]));
```

Conventional STL with generic macro:

```cpp
#include <vector>
#define ARRAY_SIZE(ar) (sizeof(ar) / sizeof(ar[0]))
#define ARRAY_END(ar) (ar + ARRAY_SIZE(ar))
using std::vector;

static const int arr[] = {10, 20, 30};
vector<int> v (arr, ARRAY_END(arr));
```

Conventional STL with a vector initializer marco:

```cpp
#include <vector>
#define INIT_FROM_ARRAY(ar) (ar, ar + sizeof(ar) / sizeof(ar[0]))

static const int arr[] = {10, 20, 30};
vector<int> v INIT_FROM_ARRAY(arr);
```

### Initialize array with 0

Unless that value is 0 (in which case you can omit some part of the initializer and the corresponding elements will be initialized to 0), there's no easy way.

Elements with missing values will be initialized to 0:

```cpp
int myArray[10] = { 1, 2 }; // initialize to 1,2,0,0,0...

int myArray[10] = { 0 }; // all elements 0
```

In C++, an empty initialization list will also initialize every element to 0. This is not allowed with C until C23:

```cpp
int myArray[10] = {}; // all elements 0 in C++ and C23
```

Remember that objects with static storage duration will initialize to 0 if no initializer is specified:

```cpp
static int myArray[10]; // all elements 0
```

And that "0" doesn't necessarily mean "all-bits-zero", so using the above is better and more portable than `memset()`. (Floating point values will be initialized to +0, pointers to null value, etc.)
