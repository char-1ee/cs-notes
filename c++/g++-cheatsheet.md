---
description: https://bytes.usc.edu/cs104/wiki/gcc/
---

# g++ cheatsheet

#### Most Common <a href="#most-common" id="most-common"></a>

* `-Wall` show all warnings. It turns on all standard C++ warnings about code that might cause unexpected or undefined behavior.
* `-g` provides debugging feature for your program. You will need this when you want to use gdb or valgrind.
* `--std=c++<##>` uses version `<##>` of C++ when compiling. This will allow you to use specific features of that C++ version. Typically, we have you use `--std=c++17`.
* `-o <filename>` compiles and links files into an executable named `<filename>`. The default filename is `a.out`.
* `-c` compiles and assembles files but doesn’t link them. This is useful when building large projects to separate file compilation and minimize what is re-compiled.

#### Bonus

* `--sys-root=<directory>` uses `<directory>` as root directory for headers and libraries.
* `-I /<absolute-path>` adds `<absolute-path>` to the compiler’s search paths. The path must written from the root of the filesystem, `/`.
* `-Werror` make all warnings into errors _do this before submitting your code to avoid getting points deducted!_
* `-pedantic` issues all warnings demanded by strict [ISO C++](https://en.wikipedia.org/wiki/C%2B%2B#Standardization) rules if you want to be _extra_ safe
* `-Wextra` enables some extra warnings not turned on by `-Wall`. These include warnings for bad pointer to integer zero comparisons, base class not initialized in copy constructor of derived class, etc.
* `-Wfatal-errors` is similar to `-Wall` but treats an error as fatal and stops before dumping a long list of errors into the terminal.
* `-fmax-errors=n` tells the compiler to stop after encountering `n` errors in your code. Usually you want to see all errors in your code some error messages may get too long for you to read.
* `-Wshadow` warns whenever a local variable or type declaration shadows another variable, parameter, class member, etc.
* `-Wsign-conversion` or `-Wconversion` warn if making unsafe, implicit conversions between signed and unsigned types (i.e. unsigned int + (constant expression) )
* `-O2` optimize code and check for errors in parallel to find potential bugs. ([read more about this here](https://bytes.usc.edu/cs104/wiki/gcc/#more-error-checks))
* `-funroll-loops` is to unroll the loops in code
* `--help` gets help on specific options for the compiler.

**Updates:**

* `-fsanitize=address` check memeory leak. But space and time consuming. For details, click [here](https://www.bynav.com/en/resource/bywork/healthy-work/393.html).

```bash
gcc -fsanitize=address -fno-omit-frame-pointer -O1 -g main.c -o main
```

#### Cheatsheet

```shell
# Compile a single program
g++ -Wall -g -std=c++17 test.cpp -o test

# Print all possible warning flags available to compile with
g++ --help=warnings

# Compile program using header files in ./libs
# $(pwd) will expand to your current working directory
g++ -I /$(pwd)/libs -Wall -g -std=c++17 test.cpp -o test

# Compile and optimize in parallel for helpful warnings about potential bugs
g++ -O2 -Wall -std=c++17 main.cpp -o main
```

```shell
# use the following warnings to compile your program
-std=c++17 -pedantic -Wall -Wextra -Werror -Wshadow -Wsign-conversion -g

# example compilation
g++ -g -std=c++17 -pedantic -Wall -Wextra -Werror -Wshadow -Wconversion -Wunreachable-code homework_program.cpp -o homework_program
```
