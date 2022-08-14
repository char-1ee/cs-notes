---
description: Chain of responsibility pattern in Java.
---

# Chain-of-responsibility

This article shows how a request containing user data passes a sequential chain of handlers that preform various things such as authentication, authorization and validation.

### Indexes

```git
middleware/
  Middleware.java             // Basic validation interface.
  ThrottlingMiddleware.java   // Check the limit of number of requests is reached.
  UserExistsMiddleware.java   // Check user's credentials.
  RoleCheckMiddleware.java    // Check user's role.
  
server/
  Server.java                 // Authorization target.
  
Client.java                   // Demo client.
```

### Middleware.java

```java
package middleware;

/** Base middleware class. */
public abstract class Middleware {
    private Middleware next;

    /** Build chains of middlewares objects. */
    public static Middleware link(Middleware first, Middleware... chain) {
        Middleware head = first;
        for (Middleware nextInChain : chain) {
            head.next = nextInChain;
            head = nextInChain;
        }
        return first;
    }

    /**
     * Subclasses will implement this method with concretes checks.
     */
    public abstract boolean check(String email, String password);

    /**
     * Runs check on next object in chain or ends traversing if we are in
     * last object in chain.
     */
    protected boolean checkNext(String email, String password) {
        if (next == null) {
            return true;
        }
        return next.check(email, password);
    }
}
```

### ThrottlingMiddleware.java

```java
package middleware;

/**
 * ConcreteHandler. Checks whether there are too many failed
 * login requests from same IP.
 */
public class ThrottlingMiddleware extends Middleware {
    private int requestPerMinute;
    private int request;
    private long currentTime;

    public ThrottlingMiddleware(int requestPerMinute) {
        this.requestPerMinute = requestPerMinute;
        this.currentTime = System.currentTimeMillis();
    }

    @Override
    public boolean check(String email, String password) {
        if (System.currentTimeMillis() > currentTime + 60_000) {
            request = 0;
            currentTime = System.currentTimeMillis();
        }

        request++;

        if (request > requestPerMinute) {
            System.out.println("Request limit exceeded!");
            Thread.currentThread().interrupt();
        }

        return checkNext(email, password);
    }
}
```

### UserExistMiddleware.java

```java
package middleware;

import server.Server;

/**
 * ConcreteHandler. Checks whether a user with the given credentials exists.
 */
public class UserExistsHandler extends Middleware {
    private Server server;

    public UserExistsHandler(Server server) {
        this.server = server;
    }

    @Override
    public boolean check(String email, String password) {
        if (!server.hasEmail(email)) {
            System.out.println("This email is not registered.");
            return false;
        }

        if (!server.isValidPassword(email, password)) {
            System.out.println("Wrong password.");
            return false;
        }

        return checkNext(email, password);
    }
}
```

### RoleCheckMiddleware.java

```java
package middleware;

/**
 * ConcreteHandler. Checks a user' permissions.
 */
public class RoleCheckMiddleware extends Middleware {
    @Override
    public boolean check(String email, String password) {
        if (email.equals("nevergonnsgiveuup@roll.com")) {
            System.out.print("Got u");
            return true;
        }
        System.out.println("Hello, user");
        return checkNext(email, password);
    }
}
```

### Server.java

```java
package server;

import java.util.Map;

/** Server class */
public class Server {
    private Map<String, String> users = new HashMap<>();
    private Middleware middleware;

    /**
     * Client pass a chain of object to server.
     */
    public void setMiddleware(Middleware middleware) {
        this.middleware = middleware;
    }

    /**
     * Server gets email and password from client and sends the
     * authentication request to the chain
     */
    public boolean login(String email, String password) {
        if (middleware.check(email, password)) {
            System.out.println("Authorization done.");
            // some code here
            return true;
        }
        return false;
    }

    public void register(String email, String password) {
        users.put(email, password);
    }

    public boolean hasEmail(String email) {
        return users.containsKey(email);
    }

    public boolean isValidPassword(String password) {
        return users.get(email).equals(password);
    }
}
```

### Client.java

```java
import middleware.*;
import server;

import java.io.BufferedReader;
import java.io.InputStreamReader;

public class Demo {
    private static BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
    private static Server server;

    private static void init() {
        server = new Server();
        server.register("JonnyDepp@court.xyz", "admin_pass");
        server.register("Captain@court.xyz", "user_pass");

        // Link all checks.
        Middleware middleware = Middleware.link(
            new ThrottlingMiddleware(2),
            new UserExistsMiddleware(server),
            new RoleCheckMiddleware();
        );

        // Server gets a chain from client code.
        server.setMiddleware(middleware);
    }

    public static void main(String[] args) throws IOException {
        init();
        boolean flag;
        do {
            System.out.print("Enter email: ");
            String email = reader.readLine();
            System.out.print("Enter password: ");
            String password = reader.readLine();
            flag = server.login(email, password);
        } while (!flag);
    }
}
```
