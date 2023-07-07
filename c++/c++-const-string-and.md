---
description: rather than string, string&
---

# const string&

### Why \`const string&\` rather than \`string\`?

In C++, passing function parameters as `const string&` rather than just `string` can provide several benefits in terms of performance and memory usage.

When a string is passed by value, a copy of the entire string is made and passed to the function. This can be expensive if the string is large, since it involves allocating memory for the copy and copying the entire contents of the string.

On the other hand, when a string is passed by reference (`const string&`), only a reference to the original string is passed. This avoids the overhead of making a copy, and can result in faster function calls and less memory usage.

The `const` qualifier is used to indicate that the function will not modify the contents of the string. This allows the compiler to optimize the code further, since it knows that the string will not change during the function call.

In summary, passing function parameters as `const string&` can provide better performance and memory usage compared to passing by value, especially for large strings. The `const` qualifier ensures that the function cannot modify the original string, allowing for further optimizations:

### What kind of 'further optimization'?

When a variable is declared as `const` in C++, it means that the value of the variable cannot be changed after initialization. This provides the compiler with additional information that it can use to optimize the code.

Here are some examples of optimizations that can be made by the compiler when a variable is declared as `const`:

1. Constant folding: When an expression involving `const` variables is evaluated, the compiler can pre-compute the result at compile time. This can eliminate unnecessary computations and reduce the size of the compiled code.
2. Code inlining: When a `const` variable is used in a function call, the compiler may choose to inline the function, which means that the function code is inserted directly into the calling code. This can eliminate the overhead of function calls and improve performance.
3. Dead code elimination: If a `const` variable is declared but never used in the code, the compiler can eliminate the variable and any associated code during the optimization process. This can reduce the size of the compiled code and improve performance.
4. Optimization of loops: When a `const` variable is used in a loop, the compiler can optimize the loop by unrolling it or transforming it into a more efficient form. This can improve performance and reduce the number of instructions executed.

Overall, using `const` in C++ can provide the compiler with additional information that it can use to optimize the code. This can result in faster and more efficient code.
