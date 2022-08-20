# How to maintain login status

1. **Cookie + Session.**
2. **Security token.**
   1. Initially login, client posts username and password to server, then server generates a token, which includes user information used for authentication. Server sends back the token to client, client saves the token locally and every time includes the token in HTTP request (HTTP header) to server. We also can set an expire period for the token.
   2. Token is stored in string, saving memory space for server. And it is relatively more secure. Even if it is hijacked during transmission, others cannot crack the content, and it reduces server pressure and frequent database queries.
