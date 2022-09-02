# Error Handling

The systems-level function calls that we will encounter in this book use three different styles for returning errors: _Unix-style, Posix-style,_ and _GAI-style_.

### Unix-Style Error Handling

Functions such as fork and wait that were developed in the early days of Unix (as well as some older Posix functions) overload the function return value with both error codes and useful results.

For example, `wait()`returns -1 when encounter error and then set the global variable `errno`to an error code or it returns PID of reaped child.

```c
if ((pid = wait(NULL)) < 0) {
    fprintf(stderr, "wait error: %s\n", stderror(errno));
    exit(0);
}
```

The strerror function returns a text description for a particular value of errno.

### Posix-Style Error Handling

Many of the newer Posix functions such as Pthreads use the return value only to indicate success (zero) or failure (nonzero). Any useful results are returned in function arguments that are passed by reference.

For example, the Posix-style `pthread_create` function indicates success or failure with its return value and returns the ID of the newly created thread (the useful result) by reference in its first argument.

```c
if ((retcode = pthread_create(&tid, NULL, thread, NULL)) != 0) {
    fprintf(stderr, "pthread_create error: %s\n", stderror(retcode));
    exit(0);
}
```

The strerror function returns a text description for a particular value of retcode.

### GAI-Style Error Handling

The getaddrinfo (GAI) and getnameinfo functions return zero on success and a nonzero value on failure.

```c
if ((retcode = getaddrinfo(host, service, &hints, &result)) != 0) {
    fprintf(stderr, "getaddrinfo error: %s\n", gai_strerror(retcode));
    exit(0);
}
```

The gai\_strerror function returns a text description for a particular value of retcode.

### Error-Handling Wrappers

The idea is that given some base system-level function foo, we define a wrapper function Foo with identical arguments, but with the first letter capitalized. The wrapper calls the base function and checks for errors. If it detects an error, the wrapper prints an informative message and terminates the process. Otherwise, it returns to the caller. Notice that if there are no errors, the wrapper behaves exactly like the base function. Put another way, if a program runs correctly with wrappers, it will run correctly if we render the first letter of each wrapper in lowercase and recompile.

```c
void unix_error(char *msg) /* Unix-style error */
{
    fprintf(stderr, "%s: %s\n", msg, strerror(errno));
    exit(0);
}

pid_t Wait(int *status) 
{
    pid_t pid;
    if ((pid = wait(status)) < 0)
        unix_error("Wait error");
    return pid;
}
```
