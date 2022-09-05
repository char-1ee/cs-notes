---
description: Common Gateway Interface
---

# CGI

A standard used when web server serving dynamic content to clients. It solves problems includes:

### How Does the Client Pass Program Arguments to the Server?&#x20;

Arguments for GET requests are passed in the URI. As we have seen, a ‘?’ character separates the filename from the arguments, and each argument is separated by an ‘&’ character. Spaces are not allowed in arguments and must be represented with the %20 string. Similar encodings exist for other special characters.

### How Does the Server Pass Arguments to the Child?

After a server receives a request such as&#x20;

```http
GET /cgi-bin/adder?15000&213 HTTP/1.1
```

it calls `fork` to create a child process and calls `execve` to run the /cgi-bin/adder program in the context of the child. Programs like the `adder` program are often referred to as **CGI programs** because they obey the rules of the **CGI standard**. Before the call to `execve`, the child process sets the **CGI environment variable** `QUERY_STRING` to `15000&213`, which the adder program can reference at run time using the Linux getenv function.

### How Does the Server Pass Other Information to the Child?

CGI defines a number of other environment variables that a CGI program can expect to be set when it runs.&#x20;

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### Where Does the Child Send Its Output?

A CGI program sends its dynamic content to the **standard output**. Before the child process loads and runs the CGI program, it uses the Linux `dup2` function to redirect standard output to the connected descriptor that is associated with the client. Thus, anything that the CGI program writes to standard output goes directly to the client.&#x20;

Notice that since the parent does not know the type or size of the content that the child generates, the child is responsible for generating the `Content-type` and `Content-length` response headers, as well as the empty line that terminates the headers.&#x20;

Figure 11.27 shows a simple CGI program that sums its two arguments and returns an HTML file with the result to the client. Figure 11.28 shows an HTTP transaction that serves dynamic content from the adder program.

```c
1 #include "csapp.h"
2
3 int main(void) {
4     char *buf, *p;
5     char arg1[MAXLINE], arg2[MAXLINE], content[MAXLINE];
6     int n1=0, n2=0;
7
8     /* Extract the two arguments */
9     if ((buf = getenv("QUERY_STRING")) != NULL) {
10         p = strchr(buf, ’&’);
11         *p = ’\0’;
12         strcpy(arg1, buf);
13         strcpy(arg2, p+1);
14         n1 = atoi(arg1);
15         n2 = atoi(arg2);
16     }
17
18     /* Make the response body */
19     sprintf(content, "QUERY_STRING=%s", buf);
20     sprintf(content, "Welcome to add.com: ");
21     sprintf(content, "%sTHE Internet addition portal.\r\n<p>", content);
22     sprintf(content, "%sThe answer is: %d + %d = %d\r\n<p>",
23     content, n1, n2, n1 + n2);
24     sprintf(content, "%sThanks for visiting!\r\n", content);
25
26     /* Generate the HTTP response */
27     printf("Connection: close\r\n");
28     printf("Content-length: %d\r\n", (int)strlen(content));
29     printf("Content-type: text/html\r\n\r\n");
30     printf("%s", content);
31     fflush(stdout);
32
33     exit(0);
34 }
```
