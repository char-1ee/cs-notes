# Token Bucket

Token Bucket algorithm can be used as limiter strategy under high concurrency scenario (flash sale, 秒杀场景). In golang, there is already an official implementation: _Limiter_ (golang.org/x/time/rate). To implement Token Bucket, can use _Channel_ in Go and _BlockQueue_ in Java.

### Algorithm

* r tokens are added into bucket per second. In other words, a token is added to the bucket every 1/r seconds.
* The bucket can hold at the most b tokens. If a token arrives when the bucket is full, it is discarded.
* When someone requests for n tokens, if #tokens smaller than n then the request blocks or quit, else get n tokens from bucket.&#x20;

### Scenarios

Token can be seen as any resource.&#x20;

* Limited amount of commodity under flash sale circumstance. You have only 10 commodities in your warehouse, you must ensure only 10 commodities are ready for snapping up. Also, need ensure there wont be 11 commoditesi sold.
* Number of request as we want to limit QPS.

When b > 1:

The bucket capacity b ensures that within 1/r second such a short time period, the max amount of resources users consume is b. Then r is set how short in a while as we want.

When b = 1:

Up to r tokens can be taken away per second.

Therefore,&#x20;

if you want to limit a highest QPS, then set b = 1 and set r = (highest QPS);

if you dont mean to limit request per second, but a cocurrency for a short instant, then set b = (that cocurrency) and set r = (the short instant you want, 1/r, in second)

### Reference

[https://www.bilibili.com/video/BV1xh4y117fw/?spm\_id\_from=444.41.list.card\_archive.click\&vd\_source=846b90373d9e33ef71ea62800af315c4](https://www.bilibili.com/video/BV1xh4y117fw/?spm\_id\_from=444.41.list.card\_archive.click\&vd\_source=846b90373d9e33ef71ea62800af315c4)
