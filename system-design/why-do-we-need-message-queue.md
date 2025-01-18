# Why do we need message queue

A generalized image:

<figure><img src="https://kbvr6jx29l.larksuite.com/space/api/box/stream/download/asynccode/?code=OTQyZTgwZGVhNzM1NTBjYTQ2ZDdiMmJlYzgxZWZmNWVfMk11MGdpZ1h4amkxZWtUdUxnd3FaUWplVFBJOWxlNGdfVG9rZW46RG5WdGI1alZGb2VINEh4YW92QnVNa0FYc0FmXzE3MzcyMjU3OTE6MTczNzIyOTM5MV9WNA" alt=""><figcaption></figcaption></figure>

### 业务解耦 Business decoupling

> 在美团旅游的系统架构中，产品中心承担数据中转的角色:
>
> * 上游连接主站、移动后台和旅游供应链等数据源
> * 下游对接筛选系统、API系统等展示系统
>
> 现有的同步调用方式存在以下问题:
>
> 1. 上游系统与产品中心过度耦合
>    1. 部分系统(如团购)只需发送变更通知,无需关注处理结果
>    2. 只有旅游自建供应链真正需要同步确认更新结果
> 2. 下游系统的更新需求对产品中心造成压力
>    1. 更新索引、刷新缓存等操作可以异步处理
>    2. 仅需要发送产品ID变更通知,由下游自行处理
> 3. 订单系统的类似场景
>    1. 支付成功后的短信、积分等非核心流程应该异步化
>    2. 避免因外部系统响应慢而影响主流程性能
>
> 引入消息系统可以解决以上问题,实现更合理的系统解耦和异步处理。

```sql
1. Upstream Systems -> Product Center:
   - Main Site: Product updates
   - Mobile Backend: Product changes
   - Tourism Supply Chain: Inventory updates
   
2. Product Center:
   - Core responsibility: Product data management
   - No need to wait for downstream processing
   - Just broadcasts change notifications

3. Product Center -> Message Queue -> Downstream Systems:
   Simple event notification flow:
   - Search System:
     * Receives product ID change event
     * Updates index independently
   - Cache System:
     * Receives change notification
     * Refreshes cache as needed
   - API System:
     * Gets notified of changes
     * Updates as per own schedule

Similarly for Order System:
1. Order System Core Flow:
   - Processes payment
   - Completes core transaction
   - Broadcasts success event

2. Message Queue -> Supporting Services:
   Async processing:
   - SMS System: Sends notifications
   - Points System: Updates points
   (Core flow doesn't wait for these)
```

### 最终一致性 Eventual consistency

If no new updates are made to a given data item, eventually all access to that item will return the last updated value.

> 以一个银行的转账过程来理解最终一致性，转账的需求很简单，如果A系统扣钱成功，则B系统加钱一定成功。反之则一起回滚，像什么都没发生一样。 然而，这个过程中存在很多可能的意外：
>
> 1. A扣钱成功，调用B加钱接口失败。
> 2. A扣钱成功，调用B加钱接口虽然成功，但获取最终结果时网络异常引起超时。
> 3. A扣钱成功，B加钱失败，A想回滚扣的钱，但A机器down机。
>
> 从技术的角度讲通用的解决方案是：
>
> 1. 强一致性，分布式事务，但落地太难且成本太高。
> 2. 最终一致性，主要是用“记录”和“补偿”的方式。在做所有的不确定的事情之前，先把事情记录下来，然后去做不确定的事情，结果可能是：成功、失败或是不确定，“不确定”（例如超时等）可以等价为失败。成功就可以把记录的东西清理掉了，对于失败和不确定，可以依靠定时任务等方式把所有失败的事情重新搞一遍，直到成功为止。 回到刚才的例子，系统在A扣钱成功的情况下，把要给B“通知”这件事记录在库里（为了保证最高的可靠性可以把通知B系统加钱和扣钱成功这两件事维护在一个本地事务里），通知成功则删除这条记录，通知失败或不确定则依靠定时任务补偿性地通知我们，直到我们把状态更新成正确的为止。 整个这个模型依然可以基于RPC来做，但可以抽象成一个统一的模型，基于消息队列来做一个“企业总线”。 具体来说，本地事务维护业务变化和通知消息，一起落地（失败则一起回滚），然后RPC到达[broker](https://zhida.zhihu.com/search?content_id=252330922\&content_type=Article\&match_order=1\&q=broker\&zhida_source=entity)，在broker成功落地后，RPC返回成功，本地消息可以删除。否则本地消息一直靠定时任务轮询不断重发，这样就保证了消息可靠落地broker。 broker往consumer发送消息的过程类似，一直发送消息，直到consumer发送消费成功确认。 我们先不理会重复消息的问题，通过两次消息落地加补偿，下游是一定可以收到消息的。然后依赖状态机版本号等方式做判重，更新自己的业务，就实现了最终一致性。

So eventual consistency requires:

* If A's account deduction succeeds, B's account addition must succeed.
* If either fails, both should roll back as if nothing happened.

Then the Record and Compensation method is:

1. Record the intended action before execution
2. Attempting the uncertain operation
3. Handle the results with MQ:
   1. Success -> clear the record
   2. Failure/Uncertain -> Retry via scheduled tasks until success

```sql
1. System A (in single DB transaction):
   - Deducts money
   - Creates local message record
   - If transaction failed, rollback

2. System A -> Message Queue:
   - Sends message to queue
   - If queue confirms storage → delete local message
   - If queue fails → retry based on local message record

3. Message Queue -> System B:
   - Queue delivers message to B
   - Retries until B confirms successful processing
```

### 广播 Broadcast

> 消息队列的基本功能之一是进行广播。如果没有消息队列，每当一个新的业务方接入，我们都要联调一次新接口。有了消息队列，我们只需要关心消息是否送达了队列，至于谁希望订阅，是下游的事情，无疑极大地减少了开发和联调的工作量.

### 错峰与流控 Peak shifting and congestion control

> 试想上下游对于事情的处理能力是不同的。比如，Web前端每秒承受上千万的请求，并不是什么神奇的事情，只需要加多一点机器，再搭建一些LVS负载均衡设备和Nginx等即可。但数据库的处理能力却十分有限，即使使用SSD加分库分表，单机的处理能力仍然在万级。由于成本的考虑，我们不能奢求数据库的机器数量追上前端。 这种问题同样存在于系统和系统之间，如短信系统可能由于短板效应，速度卡在网关上（每秒几百次请求），跟前端的并发量不是一个数量级。但用户晚上个半分钟左右收到短信，一般是不会有太大问题的。如果没有消息队列，两个系统之间通过协商、滑动窗口等复杂的方案也不是说不能实现。但系统复杂性指数级增长，势必在上游或者下游做存储，并且要处理定时、拥塞等一系列问题。而且每当有处理能力有差距的时候，都需要单独开发一套逻辑来维护这套逻辑。所以，利用中间系统转储两个系统的通信内容，并在下游系统有能力处理这些消息的时候，再处理这些消息，是一套相对较通用的方式。
>
> * 对于需要强事务保证而且延迟敏感的，RPC是优于消息队列的
> * 对于不会核心业务逻辑不关心的事情，可以利用消息队列去做
> * 支持最终一致性的消息队列，能够用来处理延迟不那么敏感的“[分布式事务](https://zhida.zhihu.com/search?content_id=252330922\&content_type=Article\&match_order=2\&q=%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1\&zhida_source=entity)”场景，而且相对于笨重的分布式事务，可能是更优的处理方式
> * 当上下游系统处理能力存在差距的时候，利用消息队列做一个通用的“漏斗”。在下游有能力处理的时候，再进行分发

## About message queue

> 给人的直觉是——MQ是异步的，用来解耦的，但是这个只是MQ的效果而不是目的。MQ真正的目的是为了**通讯**，屏蔽底层复杂的通讯协议，定义了一套应用层的、更加简单的通讯协议。一个分布式系统中两个模块之间通讯要么是HTTP，要么是自己开发的TCP，但是这两种协议其实都是原始的协议。HTTP协议很难实现两端通讯——模块A可以调用B，B也可以主动调用A，如果要做到这个两端都要背上WebServer，而且还不支持长连接（HTTP 2.0的库根本找不到）。TCP就更加原始了，[粘包](https://zhida.zhihu.com/search?content_id=252330922\&content_type=Article\&match_order=1\&q=%E7%B2%98%E5%8C%85\&zhida_source=entity)、心跳、私有的协议，想一想头皮就发麻。MQ所要做的就是在这些协议之上构建一个简单的“协议”——生产者/消费者模型。MQ带给我的“协议”不是具体的通讯协议，而是更高层次通讯模型。它定义了两个对象——发送数据的叫生产者；消费数据的叫消费者， 提供一个SDK让我们可以定义自己的生产者和消费者实现消息通讯而无视底层通讯协议。

### 重Topic (Kafka)

<figure><img src="https://kbvr6jx29l.larksuite.com/space/api/box/stream/download/asynccode/?code=ZTY4ZGJlNWRhNThmYzc2NDI1MjM3ZjNlZjczMTRhNmZfY0J2SWw4NDhWeDRRNlp1V0RVbDA1VldoU3Fkb20wVDJfVG9rZW46T2lYdWI1c3p3b2lZN1l4UDN2NHVlSmJGczBkXzE3MzcyMjU3OTE6MTczNzIyOTM5MV9WNA" alt=""><figcaption></figcaption></figure>

### 轻Topic (RabbitMQ)

<figure><img src="https://kbvr6jx29l.larksuite.com/space/api/box/stream/download/asynccode/?code=NTdmMWU5MTBlM2NkMGIxYWFkMjU0NWU2NTYyMDUxOGFfTnpoNHlRM3NmU2pZTUFpVGNpV3paY3VTQjFZUjlvUXpfVG9rZW46Q3BPS2JYQXA2b09sWlN4Zkw5WHVteDdvc3lnXzE3MzcyMjU3OTE6MTczNzIyOTM5MV9WNA" alt=""><figcaption></figcaption></figure>

### 无broker (zeromq)

\


<figure><img src="https://kbvr6jx29l.larksuite.com/space/api/box/stream/download/asynccode/?code=OWU3OTUyOWZjYjI3NjUxZDlmMWRlNzUyNjAzMDY4YTlfYmM1UlJad2puR0hEN0Z6UXdRV1FoMWV4dGJmZlc0RWRfVG9rZW46U2pUdWJBUGZJb1ZIU0N4R1hCQnU4WFNkc1Z2XzE3MzcyMjU3OTE6MTczNzIyOTM5MV9WNA" alt=""><figcaption></figcaption></figure>

> kafka，zeromq，rabbitmq代表了三种完全不同风格的MQ架构；关注点完全不同：
>
> * kafka在乎的是性能，速度
> * [rabbitmq](https://zhida.zhihu.com/search?content_id=252330922\&content_type=Article\&match_order=2\&q=rabbitmq\&zhida_source=entity)追求的是灵活
> * zeromq追求的是轻量级、分布式
>
> 如果你拿zeromq来做大数据量的传输功能，不是生产者的内存“爆掉”就是消费者被“压死”；如果你用kafka做通讯总线那绝对的不会快只能更慢；你想要rabbitmq实现分布式，那真的是难为它

#### Reference

https://zhuanlan.zhihu.com/p/16544525980\
