# 远程通信模块
> 官方文档的解释：相当于 Dubbo 协议的实现，如果 RPC 用 RMI协议则不需要使用此包。
提供了多种客户端和服务端通信功能，比如基于Grizzly、Netty、Tomcat等等，RPC用除了RMI的协议都要用到此模块。

## 子模块
* dubbo-remoting-api：定义了客户端和服务端的接口。
* dubbo-remoting-grizzly：基于Grizzly实现的Client和Server。
* dubbo-remoting-http：基于Jetty或Tomcat实现的Client和Server。
* dubbo-remoting-mina：基于Mina实现的Client和Server。
* dubbo-remoting-netty：基于Netty3实现的Client和Server。
* dubbo-remoting-netty4：基于Netty4实现的Client和Server。
* dubbo-remoting-p2p：P2P服务器，注册中心multicast中会用到这个服务器使用。
* dubbo-remoting-zookeeper：封装了Zookeeper Client ，和 Zookeeper Server 通信。


## dubbo-remoting-api
通信模块的主要逻辑都在这个module中，这个module主要个5部分：
1. buffer包：
   缓冲在NIO框架中是很重要的存在，各个NIO框架都实现了自己相应的缓存操作。这个buffer包下包括了缓冲区的接口以及抽象
2. exchange包：
   信息交换层，其中封装了请求响应模式，在传输层之上重新封装了 Request-Response 语义，为了满足RPC的需求。这层可以认为专注在Request和Response携带的信息上。
   该层是RPC调用的通讯基础之一。
3. telnet包：
   dubbo支持通过telnet命令来进行服务治理，该包下就封装了这些通用指令的逻辑实现。
4. transport包：
   网络传输层，它只负责单向消息传输，是对 Mina, Netty, Grizzly 的抽象，它也可以扩展 UDP 传输。该层是RPC调用的通讯基础之一。
5. 其他

## 通信模块&dubbo
![alt 远程通信在dubbo框架中的角色](pics/remoting-communication.jpg)