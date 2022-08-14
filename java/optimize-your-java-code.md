---
description: A collection of optimization methods in Java development.
---

# Optimize your Java code

### Indexes

1. [String concatenation with `String.format()`](optimize-your-java-code.md#string-concatenation-with-string.format)
2. [Build buffered IO streams](optimize-your-java-code.md#build-buffered-io-streams)
3. [Reduce loops by `Map`](optimize-your-java-code.md#reduce-loops-by-map)
4. [Close resources in-time](optimize-your-java-code.md#close-resources-in-time)
5. [Multithreading API calls](optimize-your-java-code.md#multithreading-api-calls)
6. [Lazy loading](optimize-your-java-code.md#lazy-loading)
7. [Initialize capacity for `Collections`](optimize-your-java-code.md#initialize-capacity-for-collections)``
8. [Replace synchronized methods with synchronized blocks](optimize-your-java-code.md#replace-synchronized-methods-with-synchronized-blocks)
9. [Pagination for big data](optimize-your-java-code.md#pagination-for-big-data)
10. [Avoid infinite loops](optimize-your-java-code.md#avoid-infinite-loops)
11. [Avoid logcat overflow](optimize-your-java-code.md#avoid-logcat-overflow)

### String concatenation with `String.format()`

To concatenate multiple parameters in a url (third-party API get), using `+` or `StringBuilder`,

```java
String url = "http://susan.sc.cn?userName="+userName+"&age="+age+"&address="+address+"&sex="+sex+"&roledId="+roleId;
```

After optimization,

```java
String requestUrl = "http://susan.sc.cn?userName=%s&age=%s&address=%s&sex=%s&roledId=%s";
String url = String.format(requestUrl, userName, age, address, sex, roledId);
```

`String.format()` to concatenate url parameters, print logcat. `<br>`



### Build buffered IO streams

Copy 1.txt to 2.txt using `FileInputStream` and `FileOutStream`,

```java
public static void copy() {
    FileInputStream fis = null;
    FileInputStream fos = null;
    try {
        File srcFile = new File("../1.txt");
        File destFile = new File("../2.txt");
      
        fis = new FileInputStream(srcFile);
        fos = new FileOutputStream(destFile);
      
        int len;
        while ((len == fis.read()) != -1) {
            fos.write(len);
        }
        fos.flush();
    } catch (IOException e) {
        e.printStackTree();
    } finally {
        try {
            if (fos != null) fos.close();
        } catch (IOException e) {
            e.printStackTree();
        }
      
        try {
            if (fis != null) fis.close();
        } catch (IOException e) {
            e.printStackTree();
        }
    }
}
```

but it requires frequent reading/writing. Optimize with `BufferedInputStream` and `BufferedOutputStream`,

```java
public static void copy() {
    BufferedInputStream bis = null;
    BufferedOutputStream bos = null;
  
    FileInputStream fis = null;
    FileOutputStream fos = null;
  
    try {
        File srcFile = new File("../1.txt");
        File destFile = new File("../2.txt");
      
        fis = new FileInputStream(srcFile);
        fos = new FileOutputStream(destFile);
      
        bis = new BufferedInputStream(fis);
        bos = new BufferedOutputStream(fos);
      
        byte[] buff = new byte[1024];
        int len;
        while ((len = bis.read(buffer)) != -1) {
            bos.write(buff, 0, len);
        }
        bos.flush;
    } catch (IOException e) {
        e.printStackTree();
    } finally {
        try {
            if (bos != null) bos.close();
            if (fos != null) fos.close();
        } catch (IOException e) {
            e.printStackTree();
        }
      
         try {
            if (bis != null) bis.close();
            if (fis != null) fis.close();
         } catch (IOException e) {
            e.printStackTrace();
         }     
    }
}
```

Define a byte array to buffer data, improving IO efficiency. `<br>`



### Reduce loops by `Map`

```java
for (User user : userList) {
    for (Role role : roleList) {
        if (user.getRoleId().equals(role.getId())) {
            user.setRoleName(role.getName());
        }
    }
}
```

After optimization, utilize Functional programming in Java,

```java
Map<Long, List<Role>> roleMap = roleList.stream().collect(Collectors.groupingBy(Role::getId));
for (User user : userList) {
    List<Role> roles = roleMap.get(user.getRoleId());
    if (CollectionUtils.isNoTEmpty(roles)) {
        user.setRoleName(roles.get(0).getName());
    }
}
```



### Close resources in-time

A counter-example,

```java
// Load driver
Class.forName("com.mysql.jdbc.Driver");
// Establish connection
Connection connection = DriverManager.getConnection("jdbc:mysql//localhost:3306/db?allowMultiQueries=true&useUnicode=true&characterEncoding=UTF-8","root","123456");
// SQL
String sql ="select * from user";
// Create PreparedStatement
PreparedStatement pstmt = conn.prepareStatement(sql);
// Query result
ResultSet rs = pstmt.execteQuery();

while(rs.next()){
   int id = rs.getInt("id");
   String name = rs.getString("name");
}
```

The problem is, `ResultSet`, `PreparedStatement` and `Connection` are not closed when using up. Correction is,

```java
Class.forName("com.mysql.jdbc.Driver");

Connection connection = null;
PreparedStatement pstmt = null;
ResultSet rs = null;
try {
    // Establish connection
    Connection connection = DriverManager.getConnection("jdbc:mysql//localhost:3306/db?allowMultiQueries=true&useUnicode=true&characterEncoding=UTF-8","root","123456");
    // SQL
    String sql ="select * from user";
    // Create PreparedStatement
    PreparedStatement pstmt = conn.prepareStatement(sql);
    // Query result
    ResultSet rs = pstmt.execteQuery();

    while(rs.next()){
       int id = rs.getInt("id");
       String name = rs.getString("name");
}
} catch (IOException e) {
    e.printStackTree();
} finally {
    if (rs != null) rs.close();
    if (pstmt != null) pstmt.close();
    if (connection != null) connection.close();
}
```

Notice that resources should be closed in order. `<br>`



### Multithreading API calls

For a API that calls other services, for example serially call takes `530ms`,

```markdown
                +----------------------------------------------------------------+
UserInfo API -> | UserQuery (200ms) + CreditQuery (150ms) + BalanceQuery (180ms) |
                +----------------------------------------------------------------+
```

After optimization with parallel calls, the `UserInfo` only takes `200ms`,

```java
public UserInfo getUserInfo(Long id) throws InterruptException, ExecutionException {
    final UserInfo userInfo = new UserInfo();
  
    CompleteableFuture userFuture = CompleteableFuture.supplyAsync(() -> {
        getRemoteUserAndFill(id, userInfo);
        return Boolean.TRUE;
    }, executor);
  
    CompleteableFuture creditFuture = CompleteableFuture.supplyAsync(() -> {
        getRemoteCreditAndFill(id, userInfo);
        return Boolean.TRUE;
    }, executor);
  
    CompleteableFuture balanceFuture = CompleteableFuture.supplyAsync(() -> {
        getRemoteBalanceAndFill(id, userInfo);
        return Boolean.TRUE;
    }, executor);
  
    userFuture.get();
    creditFuture.get();
    balanceFuture.get();
    return userInfo;
}
```

Before Java8 `Callable` and after Java8 `CompleteableFuture`, and here uses thread pool `executor`. `<br>`



### Lazy loading

Typical example is Singleton pattern, having two implementations **Eager loading** and **Lazy loading**. Eager loading for Singleton does not need to concern thread safety but wastes memory.

```java
public class Singleton {
    private static final Singleton INSTANCE = new Singleton();
  
    private Singleton() {
    }
  
    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```

A variable declared as `static final` tells the JVM that there is only one copy in memory, which is globally unique and unmodifiable. The lazy loading implementation, only initialize instance when needed.

```java
public class Singleton {
    private static Singleton INSTANCE;
  
    private Singleton() {
    }
  
    public synchronized static Singleton getInstance() {
        if (INSTANCE != null) 
            INSTANCE = new Singleton();
        return INSTANCE;
    }
}
```



### Initialize capacity for `Collections`

Many collections like `ArrayList` and `HashMap` have mechanisms of auto extending their capacities. For example, `ArrayList` has a default capacity of 10, but its capacity grows in 1.5 times than original when increasing. While for a large dataset, the `ArrayList` will spend great time on expansion. Thus, for better performance, initialize a constant capacity for `Collections`.



### Replace synchronized methods with synchronized blocks

Locks on a method have a high lock granularity, which reduces performance.

```java
public synchronized foo(String fileUrl) {
    mkdir();
    uploadFile(fileUrl);
    sendMessage(fileUrl);
}
```

We only need avoid creating multiple directories. After optimization,

```java
public void foo(String fileUrl) {
    synchronized(this) {
        if (!exist(path)) mkdir();   
    }
    uploadFile(fileUrl);
    sendMessage(fileUrl);  
}
```



### Pagination for big data

```java
List<User> userList = userMapper.getAllUser();
for (User user : userList) {
    foo();
}
```

When `getAllUser()` returns a larger number of dataset, we use pagination (in Spring, Mybatis etc.) rather then a "big collection".

```java
private static final int PAGE_SIZE = 500;

int currPage = 1;
RequestPage page = new RequestPage();
page.setPageNo(currPage);
page.setPageSize(PAGE_SIZE);

Page<User> pageUser = userMapper.search(page);

while (pageUser.getPageCount >= currPage) {
    for (User user : pageUser.getData()) {
        foo();
    }
    page.setPageNo(++currPage);
    pageUser = userMapper.search(page);
}
```



### Avoid infinite loops

```java
while (true) {
    if (condition) {
        break;
    }
    doSomething();
}
```

Here uses `while (true)`. It will goto a infinite loop if `condition` is so complex that produces an error. So not recommended to write loop in this way, except CAS locks. For recursions, it is adviced to define a `maxRecursionDepth == 4`. If depth of recursion grows larger than 4, then return.



### Avoid logcat overflow

```java
@PostMapping("/query")
public List<User> query(@RequestBody List<Long> ids) {
    log.info("request params:{}", ids);
    List<User> userList = userService.query(ids);
    log.info("response:{}", userList);
    return userList;
}
```

If there passes massive `ids` and this method is called frequently, there will print too much log messages occupying remaining space on disk. After optimization,

```java
@PostMapping("/query")
public List<User> query(@RequestBody List<Long> ids) {
    if (log.isDebugEnabled()) {
        log.debug("request params:{}", ids);
    }
   
    List<User> userList = userService.query(ids);
  
    if (log.isDebugEnabled()) {
        log.debug("response:{}", userList);
    }
    return userList;
}
```

Thus, we judge with `isDebugEnabled`. Only when the log class is `debug` then print log. The default class is `info`, we can change it to `debug` in case of emergency.

***
