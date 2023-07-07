# extern

### [External  and Internal linkage](https://learn.microsoft.com/en-us/cpp/cpp/program-and-linkage-cpp?view=msvc-170)

* _Internal linkage_ refers to everything only **in scope of a translation unit**.
* _External linkage_ refers to things that exist beyond a particular translation unit. In other words, **accessible through the whole program**, which is the combination of all translation units (or object files).

If the linkage is not specified then the default linkage is `extern` (external linkage) for non-`const` symbols and `static` (internal linkage) for `const` symbols.

```cpp
// In namespace scope or global scope.
int i; // extern by default
const int ci; // static by default
extern const int eci; // explicitly extern
static int si; // explicitly static

// The same goes for functions (but there are no const functions).
int f(); // extern by default
static int sf(); // explicitly static
```

### One Definition Rule

ORD, variable can only be defined once.&#x20;

Defining declaration, allocating memory space for variables. Referencing declaration, not allocation memeory space for variables because it reference existing variables.

### extern "C"

[https://stackoverflow.com/questions/1041866/what-is-the-effect-of-extern-c-in-c](https://stackoverflow.com/questions/1041866/what-is-the-effect-of-extern-c-in-c)
