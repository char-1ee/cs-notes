# Actor model

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption><p>Actor system</p></figcaption></figure>

分布式计算中的Actor模型是一种并发计算的编程模型，它将计算实体视为Actor，这些实体可以独立地执行计算、保存状态、通过消息传递与其它Actor通信，并在接收到消息时触发计算。这种模型非常适合构建高并发、分布式系统，因为它天然支持并行处理、解耦和容错。以下是Actor模型的一些核心概念和特性：

#### 1. **Actor的基本概念**

* **自主性**：每个Actor都是一个独立的执行单元，拥有自己的状态和行为逻辑，对外界完全封装，仅通过消息传递进行交互。
* **消息传递**：Actors之间不直接调用方法或共享内存，而是通过发送异步消息来进行通信。消息发送者无需等待响应即可继续执行，实现了非阻塞通信。
* **邮箱**：每个Actor都有一个或多个邮箱，用于接收消息。消息按先进先出（FIFO）或其他策略排序。
* **反应式**：当Actor接收到消息时，它会根据内部状态和消息内容决定如何响应，这可能包括修改自身状态、发送更多消息给其他Actor或创建新的Actor。

#### 2. **Actor的生命周期**

* **创建**：Actors通常由其他Actors创建，创建过程可以携带初始化消息。
* **处理消息**：Actor的主要活动是处理从其邮箱中取出的消息。
* **终止**：Actors可以在完成任务、遇到错误或收到特定消息后被停止。终止可以是临时的也可以是永久的，且可以有清理操作。

#### 3. **故障处理与容错**

* **隔离性**：由于Actors之间的通信是异步的且不共享状态，因此单个Actor的失败不会直接影响到其他Actor，提供了天然的故障隔离。
* **supervision**: Actor模型常常伴随着一个监督机制，即所谓的"Supervisor Actors"，它们负责监控子Actor的行为，当子Actor失败时，可以选择重启、停止或忽略该子Actor，实现系统的自我修复能力。

#### 4. **实例：Erlang/OTP和Akka**

* **Erlang/OTP**：Erlang语言及其OTP库是最早采用Actor模型的，广泛应用于需要高度可靠性的电信系统中。Erlang的进程就是轻量级的Actors，支持热更新、故障恢复等高级特性。
* **Akka**：Akka是一个开源的Java和Scala库，它实现了Actor模型，支持构建高性能的分布式系统。Akka提供了强大的消息传递机制、故障处理框架以及对并发、并行计算的良好支持。

#### 5. **应用场景**

Actor模型特别适合于构建大型、分布式、高并发系统，如：

* 大规模在线服务和Web应用的后端处理。
* 实时数据分析和流处理系统。
* 高可用性和容错要求高的系统，如金融交易系统、云基础设施。
* 物联网(IoT)平台，处理大量设备的异步通信和数据处理。

### Reference

{% embed url="https://en.wikipedia.org/wiki/Actor_model" %}

{% embed url="https://betterprogramming.pub/implementing-the-actor-model-in-golang-3579c2227b5e" %}
