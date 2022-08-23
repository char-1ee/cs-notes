# How to maintain login status

1. **Cookie + Session.**
2. **Security token.**
   1. Initially login, client posts username and password to server, then server generates a token, which includes user information used for authentication. Server sends back the token to client, client saves the token locally and every time includes the token in HTTP request (HTTP header) to server. We also can set an expire period for the token.
   2. Token is stored in string, saving memory space for server. And it is relatively more secure. Even if it is hijacked during transmission, others cannot crack the content, and it reduces server pressure and frequent database queries.



一：服务端默认的session

这种方式最大的优点是服务端不用增加任何代码，但APP与网站不同，通常情况下，我们会希望APP的登陆状态能维持数天，甚至数月之久，大部分的服务端程序，都会在进程重启时或客户端多久不活动时，将session全部清空，致使状态丢失



二、在客户端记录登陆用户的用户名和密码

客户端在登陆时，判断如果服务端返回验证成功，则将用户名和密码（为了客户端安全起见，可以将password多次md5）保存到本地的localStorage中，然后每次HTTP请求时，将用户名和密码带到参数中，服务端每次都需要读取数据库判断合法性，会浪费一定的服务器资源，同时，因为密码保存到本地，带来了一定的安全风险。



三：借助于数据库或文件系统等维持持久状态

这种方式，是为了解决方法一中session不持久的问题（如：将session保存的位置修改到文件或数据库），以及方案二中的安全性问题，不过这会降低服务端性能，或浪费服务端资源，代码逻辑流程如下：

数据库中，除了常见的user,password之外，额外增加一个字段，如：sessionID

在登陆接口中，如果判断用户输入的用户名密码正确时，生成一个随机字符串，保存到sessionID中，同时，客户端也将该值保存，在每次请求时，传递该值，服务端通过该值查询数据库，取得用户的身份信息。



四：密钥方式

以上三种方法，要么不能持久保存状态，要么不安全，要么每次请求都需要读取数据库验证，有没有一种方法，能解决以上的缺陷呢，于是，就有了这个方法，流程如下：

1. 客户端输入用户名和密码，提交到服务端验证
2. 服务端验证成功后，给客户端返回以下值：
   1. uid : 用户的唯一标示
   2. time : 当前unix时间戳
   3. key : MD5 (uid+time+"一个只有你自己知道的字符串密钥")

3：客户端保存以上3个值在本地，每次HTTP请求时，将以上3个值发送到服务端

4：服务端验证key，判断如果与客户端发送的key一致，则说明用户身份无误

5：服务端每次收到请求时，通过当前时间-客户端time字段得到的差值，可以控制这个key的有效期
