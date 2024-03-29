> 网页中的图片资源为什么分放在不同的域名下?

浏览器对并发请求的数目限制是针对域名的，即针对同一域名（包括二级域名）在同一时间支持的并发请求数量的限制。如果请求数目超出限制，则会阻塞。因此，网站中对一些静态资源，使用不同的一级域名，可以提升浏览器并行请求的数目，加速界面资源的获取速度。

> 浏览器与服务器建立一个TCP连接后，是否会在完成一个http请求后断开？什么条件下会断开？

在HTTP/1.0中，一个http请求收到服务器响应后，会断开对应的TCP连接。这样每次请求，都需要重新建立TCP连接，这样一直重复建立和断开的过程，比较耗时。所以为了充分利用TCP连接，可以设置头字段Connection: keep-alive，这样http请求完成后，就不会断开当前的TCP连接，后续的http请求可以使用当前TCP连接进行通信。

HTTP/1.1将Connection写入了标准，默认值为keep-alive。除非强制设置为Connection: close，才会在请求后断开TCP连接。

所以这一题的答案就是：默认情况下建立的TCP连接不会断开，只有在请求头中设置Connection: close才会在请求后关闭TCP连接。



> 一个TCP连接可以同时发送几个HTTP请求？

HTTP/1.1中，单个TCP连接，在同一时间只能处理一个http请求，虽然存在Pipelining技术支持多个请求同时发送，但由于实践中存在很多问题无法解决，所以浏览器默认是关闭，所以可以认为是不支持同时多个请求。

HTTP2提供了多路传输功能，多个http请求，可以同时在同一个TCP连接中进行传输。



> 浏览器http请求的并发性是如何体现的？并发请求的数量有没有限制？

页面资源请求时，浏览器会同时和服务器建立多个TCP连接，在同一个TCP连接上顺序处理多个HTTP请求。所以浏览器的并发性就体现在可以建立多个TCP连接，来支持多个http同时请求。

Chrome浏览器最多允许对同一个域名Host建立6个TCP连接，不同的浏览器有所区别。


[各个浏览器host建立tcp数量限制参考](https://cloud.tencent.com/developer/article/1683127)

对于同一个协议、域名、端口，浏览器允许同时打开个 TCP 连接，一般上限为 6 个。
同一个 TCP 连接允许发起多次 HTTP 请求，但必须等待前一个请求的首个字节响应到达客户端。
由于队首阻塞问题，不允许客户端同时发送队列中所有请求，这个问题在 HTTP/2.0 得已解决。



> 建立多个tcp连接的原理是什么？

基于域名分片,利用域名分片来解决http1.x的不能并行处理资源的问题；

[域名分片概述](https://developer.mozilla.org/zh-CN/docs/Glossary/Domain_sharding)



> JS实时通信三把斧


1. websocket: WebSocket协议是一个独立的基于TCP的协议。 它与HTTP唯一的关系是它的握手是由HTTP服务器解释为一个Upgrade请求
2 socket.io: 将会使用特性检测的方式来决定以websocket/ajax长轮询/flash等方式建立连接
3. EventSource: eventsource其实是单向通信，而websocket是双向通信。在股票行情、新闻推送的这种只需要服务器发送消息给客户端场景中，使用SSE更加合适, 一个客户端去从服务器端订阅一条“流”，之后服务端可以发送消息给客户端直到服务端或者客户端关闭该“流”，所以eventsource也叫作"server-sent-event"

详细介绍详见[传送门](https://github.com/liangxianh/nodejs-rtc-demo)
[websocket](https://blog.5udou.cn/#/blog/detail/JSShi-Shi-Tong-Xin-San-Ba-Fu-Xi-Lie-Zhi-Yi-websocket62)
[socket.io](https://blog.5udou.cn/#/blog/detail/JSShi-Shi-Tong-Xin-San-Ba-Fu-Xi-Lie-Zhi-Er-socketio40)
[eventsource](https://www.jianshu.com/p/3d7b0bbf435a)





