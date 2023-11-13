# Redis做限流

下面是redis官方文档有相关描述限流和如何使用redis做限流的方案

```
https://redis.com/glossary/rate-limiting/#:~:text=The%20basic%20concept%20is%20that,every%20minute%20per%20API%20key.
```

> 使用英文搜索还是比使用中文搜索更容易找到官方的资料，从而让自己接触到一手的资料。

## 为什么要做限流

现实的网络环境中有有很多攻击，比如恶意抢购，DDOS攻击，证书填充攻击，暴力破解攻击等等。如果遇到这些清空，我们都可以使用redis来做限流，从而保护我们的应用系统。

下面是一些攻击的手段和方式

- DDOS攻击，这种攻击是通过挟持或者购买大量的电脑让这些电脑伪装成真正的用户来进行网站的访问，从而让真正的用户无法访问网站或者应用。
- 证书填充攻击，使用自动化脚本来拼接用户名和密码，然后来获取进入系统的权限。
- 暴力攻击；和证书攻击有一定的相似性，通过编写程序来尝试一些字符来获取系统的权限。
- 数据刮取攻击；不断获取到系统内部的敏感数据。
- 库存囤积攻击。这个说白了就是黄牛刷票或者刷其他商品，然后倒手进行售出二获利。

## 限流控制方式

ip-based：基于ip的限流攻击，这样能够限制源ip的攻击。缺点就是无法防止分布式攻击；

user-based：基于用户的限流；

token bucket ：token的漏斗防护。

## 限流算法

固定窗口算法

> **Fixed-window rate limiting:** This is a straightforward algorithm that counts the number of requests received within a fixed time window, such as one minute. Once the maximum number of requests is reached, additional requests are rejected until the next window begins. This algorithm is easy to implement and effective against DDoS attacks but may limit **legitimate** users.
>
> 而且对于零界点的攻击无能为力；

滑动窗口算法

> **Sliding-window rate limiting:** This algorithm tracks the number of requests received in the recent past using a sliding window that moves over time. This algorithm is more flexible than fixed-window rate limiting and can adjust to spikes in traffic, making it a better choice for applications with varying usage patterns. However, it may not be as effective against sustained attacks.
>
> 这个算法其实只是对固定窗口的改进，其实两者本质上没有太大的差异。

令牌桶攻击

> **Token bucket rate limiting:** This maintains a token bucket that is refilled at a fixed rate. Each request consumes a token, and additional requests are denied once the bucket is empty. Token bucket rate limiting is suitable for handling bursts of traffic, as it can allow a certain number of requests to be processed simultaneously. However, it may not be as effective against sustained traffic.
>
> 这个解决方案其实就是按照系统或者应用的承受能力给颁发令牌。谁能拿到令牌谁就能访问系统或者应用，反过来没有拿到token请求不能访问应用或者系统。

漏斗限流

> **Token bucket rate limiting:** This maintains a token bucket that is refilled at a fixed rate. Each request consumes a token, and additional requests are denied once the bucket is empty. Token bucket rate limiting is suitable for handling **bursts of** traffic, as it can allow a certain number of requests to be processed simultaneously. However, it may not be as effective against sustained traffic.
>
> 这个其实和令牌桶限流差不多，只不过可以让更多的流量先进来而已。其实就是先缓存一部分流量。

