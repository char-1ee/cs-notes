# Where is const stored?

Compiler allocates the space and later it will determine whether it have to convert it to a literal or not. If you have not ever take the address of it then compiler will convert it into the literal and write it in assembly instructions as literal do not stored anywhere, they are actually written in assembly instructions. You will get different instruction which depends on the platform. Generally, instructions are of fixed-size and read-only therefore their storage refers to the text-segment (also known as code segment) of the memory layout of a program.

But if you ever take the address of it then compiler will store it in the stack. This section refers to the stack segment of the memory layout of a program.

{% embed url="https://cplusplus.com/forum/beginner/12417/" %}
