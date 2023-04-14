# API optimization

接口优化往往有固定的套路，首先就是定位代码中可以优化的点，比如说for loop当中存在 DB query 等

常见的方法有:

#### DB level

1. SQL bluk query 批量查询, 比如传入 id 数组，使用 where id in (...)
2. 优化索引，比如说复杂查询使用联合索引 (注意粒度)

```sql
alter table mt add index `un_org_cat_biz` (`org`,`category`,`bid`) USING BTREE;
```

#### Code level

1. 查询DB由单线程改为多线程，使用 Future 对象 async query, 可参考另一篇[笔记](<../README (1).md>)

```java
CompletableFuture[] futureArray = dataList.stream()
     .map(data -> CompletableFuture
          .supplyAsync(() -> query(data), asyncExecutor)
          .whenComplete((result, th) -> {
       })).toArray(CompletableFuture[]::new);
CompletableFuture.allOf(futureArray).join();
```

2. 使用线程池管理多线程, 比如 TheadPoolExecutor

```java
ExecutorService threadPool = new ThreadPoolExecutor(
    8, //corePoolSize线程池中核心线程数
    10, //maximumPoolSize 线程池中最大线程数
    60, //线程池中线程的最大空闲时间，超过这个时间空闲线程将被回收
    TimeUnit.SECONDS,//时间单位
    new ArrayBlockingQueue(500), //队列
    new ThreadPoolExecutor.CallerRunsPolicy()); //拒绝策略
```

#### Design level

1. 限制一次性查询记录的条数
2. Frontend -> pagination
3.  Backend -> 分批调用，比如说查询500条记录，业务系统只调用一次查询接口.

    现在改成业务系统每次只查100条记录，分5批调用，总共也是查询500条记录, 但是注意trade-off, 频繁 DB i/o 反而影响性能
