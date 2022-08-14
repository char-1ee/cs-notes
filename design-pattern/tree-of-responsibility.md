---
description: Tree-of-responsibility pattern and its application in business scenario.
---

# Tree-of-responsibility

My notes on an [article](https://developer.aliyun.com/article/781471) in Alibaba Cloud developer community.

This article introduces a design pattern, Tree of Responsibility, and how to apply it on if-else logic. Tree of Responsibility pattern, noted as ToR below, is a generalized chain design pattern combines of Chain-of-responsibility pattern and Strategy pattern. ToR not only can implment a level-to-level delegation as Chain-of-responsibiliy, but provides availiability to choose next-level strategy when currently in any level, as a high-level abstraction.

### Preface

> _We should forget about small efficiencies,say about 97% of the time:premature optimization is the root of all evil._
>
> _-- Donald Knuth_

Check another two of my gists

* [How to optimize if-else writing](https://gist.github.com/char-1ee/6bef696136cfa5b7a0df07c8b2aa8892)
* [Chain of Responsibility](https://gist.github.com/char-1ee/87855e4d7f197695449f7afc9f066f14)

### Scenario

An interface returns according to different combination of parameters `p1, p2, p3, version` and their corresponding business strategies.

```
function getBizData(p1, p2, p3) {
    if (version == v2) {
        return getBizData(p2, p1, p3);
    }
    if (version == v3) {
        return getBizData(p3, p1, p2);
    }
    if (condition1) {
        return x1;
    } else if (condition2) {
        return x2;
    } else {
        return x3;
    }
}
```

When new requirements appear, the snippet keeps extending. So the crux of problems are:

1. How to update the interface, to add new business logic while to be compatible to old versions?
2. How to choose strategies according to different combinations of `p1, p2, p3`?

### Solution

Chain-of-responsibility pattern links concrete handlers which implments abstract handlers. If current handler solve the tasks if can, or throw tasks to next handler on the chain until found one handler to solve them. For example, handler 1 cope with request of `version == 1`, handler 2 cope with request of `version == 2`. But in this way, we cannot decide strategies. Strategy pattern is to route to different strategy classes according to the context. We are able to execute different logic flows.

![](https://camo.githubusercontent.com/55ecbab2b3522ce7b04ec6e84449d23e07f7e425b6bc4a1f18e53acfba72d1b8/68747470733a2f2f7563632e616c6963646e2e636f6d2f7069632f646576656c6f7065722d65636f6c6f67792f33613832333239383830386534396433626331323734326635663438313465392e706e67)

How TOR solve our crux problems?

First is to solve compatibility of old and new interfaces. The first-level nodes can be regarded as

### Implementation

* `Router` is a abstract class, defines how to route to next-level nodes.
* `Handler` is an interface, in charge of business logic of each node.&#x20;

![](https://camo.githubusercontent.com/e2f9734b585b0f50c13d6d9fd5d82515b415fe95daa1f63868ac728ebb8c5750/68747470733a2f2f7563632e616c6963646e2e636f6d2f7069632f646576656c6f7065722d65636f6c6f67792f35303665326335343133636634653034383661343864386464656233373763622e706e67)

```java
/**
 * 通用的“策略树“框架，通过树形结构实现分发与委托，每层通过指定的参数进行向下分发委托，直到达到最终的执行者。
 * 该框架包含两个类：{@code StrategyHandler} 和 {@code AbstractStrategyRouter}
 * 其中：通过实现 {@code AbstractStrategyRouter} 抽象类完成对策略的分发，
 * 实现 {@code StrategyHandler} 接口来对策略进行实现。
 * 像是第二层 A、B 这样的节点，既是 Root 节点的策略实现者也是策略A1、A2、B1、B2 的分发者，这样的节点只需要
 * 同时继承 {@code StrategyHandler} 和实现 {@code AbstractStrategyRouter} 接口就可以了。
 *
 * <pre>
 *           +---------+
 *           |  Root   |   ----------- 第 1 层策略入口
 *           +---------+
 *            /       \  ------------- 根据入参 P1 进行策略分发
 *           /         \
 *     +------+      +------+
 *     |  A   |      |  B   |  ------- 第 2 层不同策略的实现
 *     +------+      +------+
 *       /  \          /  \  --------- 根据入参 P2 进行策略分发
 *      /    \        /    \
 *   +---+  +---+  +---+  +---+
 *   |A1 |  |A2 |  |B1 |  |B2 |  ----- 第 3 层不同策略的实现
 *   +---+  +---+  +---+  +---+
 * </pre>
 *
 * @author
 * @date
 * @see StrategyHandler
 */
@Component
public abstract class AbstractStrategyRouter<T, R> {

    /**
     * 策略映射器，根据指定的入参路由到对应的策略处理者。
     *
     * @param <T> 策略的入参类型
     * @param <R> 策略的返回值类型
     */
    public interface StrategyMapper<T, R> {
        /**
         * 根据入参获取到对应的策略处理者。可通过 if-else 实现，也可通过 Map 实现。
         *
         * @param param 入参
         * @return 策略处理者
         */
        StrategyHandler<T, R> get(T param);
    }

    private StrategyMapper<T, R> strategyMapper;

    /**
     * 类初始化时注册分发策略 Mapper
     */
    @PostConstruct
    private void abstractInit() {
        strategyMapper = registerStrategyMapper();
        Objects.requireNonNull(strategyMapper, "strategyMapper cannot be null");
    }

    @Getter
    @Setter
    @SuppressWarnings("unchecked")
    private StrategyHandler<T, R> defaultStrategyHandler = StrategyHandler.DEFAULT;

    /**
     * 执行策略，框架会自动根据策略分发至下游的 Handler 进行处理
     *
     * @param param 入参
     * @return 下游执行者给出的返回值
     */
    public R applyStrategy(T param) {
        final StrategyHandler<T, R> strategyHandler = strategyMapper.get(param);
        if (strategyHandler != null) {
            return strategyHandler.apply(param);
        }

        return defaultStrategyHandler.apply(param);
    }

    /**
     * 抽象方法，需要子类实现策略的分发逻辑
     *
     * @return 分发逻辑 Mapper 对象
     */
    protected abstract StrategyMapper<T, R> registerStrategyMapper();
}
```
