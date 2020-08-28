# 远程通信抽象模块

## 抽象概念

### Endpoint
通信的对象，抽象为"端"（endpoint），也就是一个点，点对点之间可以双向数据传输.
在点的基础树，衍生出通道（channel）、客户端（client）和服务端（server）.

### Channel
通道接口，通道是通讯的载体. Channel接口继承Endpoint接口，端的抽象是通道的组成部分。

### ChannelHandler
负责Channel的处理逻辑。接口包含connected/disconnected/sent/received对连接的处理方法。
作为SPI扩展接口。

### Client
继承Endpoint和Channel接口，通信的客户端也是一个“端”，也是“通道”一一对应的抽象。


### RemotingServer
集成Endpoint接口，通信的服务端也是一个“端”。
包含方法 getChannels(), 表示一个服务端可以连接多个客户端，包含对个Channel。

### Codec & Codec2
解码器，负责把网络通信的字节序列，与应用程序数据相互转换。

### Dispatcher
调度接口。默认实现为AllDispatcher，也就是所有消息都派发到线程池，包括请求，响应，连接事件，断开事件，心跳等。
用了Adaptive注解的dispatch方法，也就是按照URL中配置来加载实现类，后面两个参数是为了兼容老版本，如果这是三个key对应的值都为空，就选择AllDispatcher来实现。

### Transporter
网络传输接口。为Transport层抽象，mina、netty3/netty4、grizzly四个NIO通信框架的统一接口。
主要抽象接口为：AbstractChannel、AbstractServer、AbstractClient。
该接口是一个可扩展的接口，并且默认实现NettyTransporter。
用了dubbo SPI扩展机制中的Adaptive注解，加载对应的bind方法，使用url携带的server或者transporter属性值，加载对应的connect方法，使用url携带的client或者transporter属性值

### Exchanger
传输从Transport的上一层，信息交换层Exchange。
官方文档对这一层的解释是封装请求响应模式，同步转异步，以 Request, Response为中心，扩展接口为 Exchanger, ExchangeChannel, ExchangeClient, ExchangeServer。

这一层的设计意图是什么？
它应该算是在信息传输层上又做了部分装饰，为了适应rpc调用的一些需求，比如rpc调用中一次请求只关心它所对应的响应，这个时候只是一个message消息传输过来，是无法区分这是新的请求还是上一个请求的响应，这种类似于幂等性的问题以及rpc异步处理返回结果、内置事件等特性都是在Transport层无法解决满足的，所有在Exchange层讲message分成了request和response两种类型，并且在这两个模型上增加一些系统字段来处理问题。
dubbo把一条消息分为协议头和内容两部分：协议头包括系统字段等，内容包括具体请求的参数和响应的结果等。exchange层中的大量逻辑都是基于协议头的。

