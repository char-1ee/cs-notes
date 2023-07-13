# Load Balance

Requests are sent at a rate of x **Request Per Second** (RPS). If a request arrives at the server while another request is being processed, the server will **drop** it. One of the ways to mitigate this is to have a **Request Queue** on each server. Here are some common load balancer algorithms:

### Round Robin

* Round Robin is a simple and widely used algorithm that evenly distributes incoming requests across a pool of servers in a cyclical manner.
* It works by sequentially allocating each new request to the next server in the rotation order.

### Weighted Round Robin

* Weighted Round Robin extends the Round Robin algorithm by assigning a weight to each server based on its capacity or performance.
* Servers with higher weights receive a larger proportion of requests, allowing better resource utilization.

> While this handles the variance of server power better than vanilla round robin, we still have request variance to contend with. In practice, getting humans to set the weight by hand falls apart quickly. Boiling server performance down to a single number is hard, and would require careful load testing with real workloads. This is rarely done, so another variant of weighted round robin calculates weights dynamically by using a proxy metric: latency.
>
>
>
> I've added text to each server this time that shows the average latency of the last 3 requests served. We then decide whether to send 1, 2, or 3 requests to each server based on the relative differences in the latencies. The result is very similar to the initial weighted round robin simulation, but there's no need to specify the weight of each server up front. This algorithm will also be able to adapt to changes in server performance over time. This is called "dynamic weighted round robin."

### Least Connection

\-Because the load balancer sits between the server and the user, it can accurately keep track of how many outstanding requests each server has. Then when a new request comes in and it's time to determine where to send it, it knows which servers have the **least work to do and prioritises those.**

* Simple to implement: tThis algorithm performs extremely well regardless how much variance exists. It cuts through uncertainty by maintaining an accurate understanding of what each server is doing.
* Not immune to drop: while this algorithm is a great balance between simplicity and performance, it's not immune to dropping requests. However, what you'll notice is that the only time this algorithm drops requests is when there is literally no more queue space available.

### PEWMA

Peak Exponentially Weighted Moving Average.

For each server, the algorithm keeps track of the latency from the last N requests. Instead of using this to calculate an average, it sums the values but with **an exponentially decreasing scale factor.** This results in a value where the older a latency is, the less it contributes to the sum. Recent requests influence the calculation more than old ones.

That value is then taken and multiplied by **the number of open connections to the server** and the result is the value we use to choose which server to send the next request to. **Lower is better.**

## Reference

{% embed url="https://samwho.dev/load-balancing/#when-round-robin-doesn-t-cut-it" %}
blog
{% endembed %}

{% embed url="https://en.wikipedia.org/wiki/Load_balancing_(computing)" %}
