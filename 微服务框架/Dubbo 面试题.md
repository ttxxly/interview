#### Dubbo 是什么？

```Java
Dubbo 是一块高性能、轻量级的开源 RPC 框架，提供服务自动发现与注册等高效服务治理方案，
可以和 Spring 框架集成。

使用场景：
  透明化的远程方法调用：就像调用本地方法一样调用远程方法， 只需简单配置，没有任何API侵入。
  软负载均衡及容错机制：可在内网替代 F5 等硬件负载均衡器，减低成本，减少单点。
  服务自动注册和发现：服务提供方地址，注册中心基于接口名查询服务提供者的IP地址，并且能够
平滑添加或删除服务提供者。

核心功能：
  Remoting：网络通信框架，提供对多种NIO框架抽象封装，包括“同步转异步”和“请求-响应”模式的信息交换方式。
  CLuster：服务框架，提供基于接口方法的透明远程过程调用，包括多协议支持，以及软负载均衡，失败容错，
地址路由，动态配置等集群支持。
  Registry：服务注册，基于注册中心目录服务，使服务消费方能动态的查找服务提供方，使地址透明，是服务提供
方可以平滑增加或减少机器。

核心组件：
  Provider：暴露服务的服务提供方
  Consumer：调用远程服务消费方
  Registry：服务注册与发现注册中心
  Monitor：监控中心和访问调用统计
  Container：服务运行容器
```



#### Dubbo 的整体架构设计有哪些分层？

```Java
1. 接口服务层（Service）：该层与业务逻辑相关，根据 provider 和consumer 的业务设计对应的接口与实现。
2. 配置层（Config）：对外配置接口，一 ServiceConfig 和 ReferenceConfig 为中心。
3. 服务治理层（Proxy）：服务接口透明代理，生成服务的客户端 Stub 和 服务端的 Skeleton，以 ServiceProxy 为中心，
  扩展接口为 ProxyFactory
4. 服务注册层（Registry）：封装服务地址的注册和发现，以服务 URL 为中心，扩展接口为 RegistryFactory、Registry
  RegistryService
5. 路由层（Cluster）：封装多个提供者的路由和负载均衡，并桥接注册中心，以 invoke 为中心，扩展接口为
  Cluster、Directory、Router和LoadBalance
6. 监控层（Monitor）：rpc 调用次数和调用时间监控，以 Statistic 为中心，扩展接口为 MonitorFactory、Monitor
  和 MonitorService
7. 远程调用层（Protocol）：封装 rpc 调用，以 invocation 和 Result 为中心，扩展接口为 Protocol、Invoker
  和 Exporter
8. 信息交换层（Exchange）：封装请求响应模式，同步转异步。以 Request 和 Response 为中心，扩展接口为 Exchanger
  ExchangeChannel、ExchangeClient 和 ExchangeServer
9. 网络传输层（Transport）：抽象 mina 和 netty 为同一接口，以 Message 为中心，扩展接口为 Channel、Transporter
  Client、Server和Codec
10. 数据序列化层（Serialize）：可复用的一些工具，扩展接口为 Serialzation、ObjectInput、ObjectOutput 和
  ThreadPool

```



#### Dubbo 服务注册与发现流程？

```Java
1. Provider（提供者）绑定指定端口并启动服务。
2. 提供者连接注册中心，并将本机IP、端口、应用信息和提供服务信息发送至注册中心存储。
3. COnsume（消费者），连接注册中心，并发送应用信息、所求服务信息至注册中心。
4. 注册中心根据消费者所求服务信息匹配对应的提供者列表发送至COnsumer应用缓存
5. consumer 在发送远程调用基于缓存的消费者里诶博爱择其一发起调用
6. Provider 状态变更会实时通知注册中心，再由注册中心实时推送至 consumer
```



#### Dubbo 支持哪些协议，他们的优缺点有哪些？

```Java
  Dubbo（推荐）：单一长连接和NIO异步通讯，适合大并发小数据量的服务调用，以及消费者远大于提供者。传输协议 
TCP，异步 Hessian 序列化。

  RMI：采用 jdk 标准的 RMI 协议实现，传输参数和返回参数对象需要实现 Serializable 接口，使用 java 标准序列化机制。
  
  webservice：基于 webservice 的远程调用协议，集成 CXF 实现，提供和原生 webservice 的互操作。多个短连接，
基于 HTTP 同步传输，使用西戎集成和跨语言调用。

  HTTP：基于 HTTP 表单提交的远程调用协议，使用 Spring 的 HttpInvoke 实现。
  
  Hessian：集成 Hessian 服务，基于http 通讯，采用 servlet 暴露服务，Dubbo 内嵌 jetty 作为服务器时默认实现，
提供于 Hessian 服务互操作。

  Memcache：基于memcache 实现的 rpc 协议。
  
  redis：基于 redis 实现的 rpc 协议。
```



#### Dubbo 有哪些注册中心？

```Java
推荐：Zookeeper 注册中心：基于分布式协调系统 Zookeeper 实现，采用 Zookeeper 中的 watch 机制实现数据变更。
multicast 注册中心：multicast 注册中心不需要任何中心节点，只要广播地址，就能进行服务注册和发现，
  基于网络中组播传输实现。
Redis注册中心：基于 redis 实现，采用 key-map 存储，key 存储服务名和类型，map中key存储服务url，
  value 服务过期时间。基于redis的发布-订阅模式通知数据变更。
Simple 注册中心。
```



#### Dubbo 的注册中心集群挂掉，发布者和订阅者之间还能通信么？

```Java
可以通讯。
  启动 Dubbo 时，消费者会从 Zookeeper 拉取注册的生产者的地址接口等数据，缓存在本地。每次调用时，
按照本地存储的地址进行调用
```



#### Dubbo 集群提供了哪些负载均衡策略？

```Java
Random LoadBalance：**随机选取提供者策略**。有利于动态调整提供者权重。截面碰撞率高，调用次数越多，分布越均匀

RoundRobin LoadBalance：**轮询选取提供者策略**，平均分布，但是存在请求累计的问题。

LeastActive LoadBalance：**最少活跃调用策略，**解决慢提供者接收更少的请求。

ConstantHash LoadBalance：一致性 Hash 策略， 使相同参数请求总是发到同一提供者，一台机器宕机，可以基于虚拟节点分摊至
  其他提供者，避免引起提供者的剧烈变动。

```



#### Dubbo 的集容错方案有哪些？

```Java
Failover Cluster:失败自动切换，当出现失败，重试其它服务器。通常用于读操作，但重试会带来更长延迟。

Failfast Cluster:快速失败，只发起—次调用，失败立即报错。通常用于非幂等性的写操作，比如新增记录。

Failsafe Cluster:失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作。

Failback Cluster:失败自动恢复，后台记录失败请求，定时重发。通常用于消息通知操作。

Forking Cluster:并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。
  可通过forks=”2”来设置最大并行数。

Broadcast Cluster:广播调用所有提供者，逐个调用，任意一台报错则报错。
  通常用于通知所有提供者更新缓存或日志等本地资源信息。

默认的容错方案是Failover Cluster.

```



#### Dubbo 支持哪些序列化方式？

```Java
默认使用 Hessian 序列化，还有 Dubbo、fastjson、java 自带序列化。
```



#### Dubbo 超时设置有哪些方式？

```Java
Dubbo 超时设置有两种方式：
  **服务提供者端设置超时时间，**推荐能在服务端多配置就尽量多配置，因为服务提供者比消费者更清楚自己提供的服务特性。**
  服务消费者端设置超时时间，**如果在消费者端设置了超时时间，以消费者端为主，优先级更高。
```



#### 服务调用超时会怎么样？服务调用是阻塞的吗？

```Java
dubbo 在调用服务不成功时，**默认会重试两次**。

默认是阻塞的，可以异步调用，没有返回值的可以这么做。

Dubbo 是基于 NIO 的非阻塞实现并行调用，客户端不需要启动多线程即可完成并行调用多个远程服务，相对多线程开销较小，
  异步调用会返回一个 Future 对象。
```



#### Dubbo 在安全方面有哪些措施？

```Java
Dubbo 通过 Token 令牌防止用户绕过注册中心直连，然后在注册中心上管理授权。

Dubbo 还提供服务黑白名单，来控制服务所允许的调用方。
```



#### Dubbo 和 Spring Cloud 有什么区别？

```Java
Dubbo 底层使用 Netty 这样的 NIO 框架，是基于 TCP 协议传输的，配合以 Hessian 序列化完成 rpc 通信。

Spring Cloud 是基于 HTTP 协议 REST 接口调用远程过程的通信。

相对来说 http 请求会有更大的报文，占的带宽会更多。但是 REST 相对 rpc 更为灵活。
```



#### 默认使用的是什么通信框架，还有别的选择吗？

```Java
默认使用 netty 框架，还有mina
```



#### 服务提供者能实现失效踢出是什么原理？

```Java
服务失效踢出基于 Zookeeper 的临时节点原理
```



#### 如何解决服务调用链过长的问题？

```Java
结合 Zipkin 实现分布式服务追踪
```



#### 说说核心的配置有哪些？

```Java
dubbo：service  服务配置
dubbo：reference 引用配置
dubbo：protocol  协议配置
dubbo：application  应用配置
dubbo：module  模块配置
dubbo：registry 注册中心配置
dubbo：monitor 监控中心配置
dubbo：provider 提供方配置
dubbo：consumer 消费方配置
dubbo：method  方法配置
dubbo：argument 参数配置

```



#### 同一个服务多个注册的情况下可以直连某一个服务吗？

```Java
可以点对点直连，修改配置即可，也可以通过 telnet 直连某个服务
```



#### Dubbo 服务降级，失败重试怎么做？

```Java
可以通过 Dubbo：reference 中设置 mock="return null"。 mock 的值也可以修改为 true，然后跟接口同一个路径下实现一个
mock 类，命名规则是 “接口名称 + Mock”后缀。然后在 mock 类里实现自己的降级逻辑
```



#### Dubbo 使用过程中都遇到了些什么问题？

```Java
1. 在注册中心找不到对应的服务，检查 service 实现类是否添加了 @service 注解
2. 无法连接到注册中心，检查配置文件中的对应的测试IP是否正确
```



#### Dubbo Monitor 实现原理？

```Java
Consumer 端发起调用之前会先走 filter 链；provider 端在接收到请求时也是先走 filter 链，
  然后才进行真正的业务逻辑处理。
默认情况下，在 consumer 和provider 的filter 的 filter链中都会有 MonitorFilter。

1. MonitorFilter 向 DubboMonitor 发送数据
2. DubboMonitor 将数据进行聚合后（默认聚合 1 min 中的统计数据）暂存到 ConcurrentMap<Statistic，AtomicReference>
  statisticMap，然后使用一个含有 3 个线程（线程名字：DubboMonitorSendTimer）的线程池每隔 1 min 中，调用
  SimpleMonitorService 遍历发送 statisticMap 中的统计数据，每发送完毕一个，就重置当前的 statistic 的 
  AtomicReference
3. SimpleMonitorService 将这些聚合数据塞入 BlockingQueue 中（队列大写为 100000）
4. SimpleMonitorService 使用一个后台线程（线程名为：DubboMonitorAsyncWritLogThread）将queue中的数据写入
  文件（该线程以死循环的形式来写）
5. SimpleMonitorService 还会使用一个含有 1 个线程（线程名字：DubboMonitorTimer）的线程池每隔 5 min 钟，将文件
  中的统计数据画成图表。

```



#### Dubbo 用到哪些设计模式？

```Java
Dubbo 框架在初始化和通信过程中使用了多种设计模式，可灵活控制类加载、权限控制等功能

工厂模式
  Provider 在 Export 服务时，会调用 ServiceConfig 的 export 方法。ServiceConfig 中有个字段
    ExtensionLoader.getExtensionLoader(Protocol.class).getAdaptiveExtension();
  Dubbo 里有很多这种代码。这也是一种工厂模式，只是实现类的获取采用了 JDK SPI机制。这么实现的优点是可扩展性强，
  想要扩展实现，只需要在 classpath 下增加个文件就可以了，代码零侵入。另外，像上面的 Adaptive 实现，可以做到调用时
  动态决定调用哪个实现，但是由于这种实现采用了动态代理，会造成代码调试比较麻烦，需要分析出实际调用的实现类。
装饰器模式
  以 Provider 提供的调用链为例。具体的调用链代码是在 ProtocolFilterWrapper 的 buildInvokeChain 完成的，
  具体是将注解中含有 group=provider 的 Filter实现，按照 order 排序，最后的调用顺序是：
    EchoFilter -> ClassLoaderFilter -> GenericFilter -> ContextFilter ->ExecuteLimitFilter 
    ->TraceFilter ->TimeoutFilter -> MonitorFilter ->ExceptionFilter
  确切地说，这里是装饰器和责任链模式的混合使用。例如，EchoFilter 的作用是判断是否是回声测试请求，是的话直接返回内容，
  这是一种责任链的体现。
观察者模式
  Dubbo 的 Provider 启动时，需要与注册中心交互，先注册自己的服务，在订阅自己的服务，订阅时，采用了观察者模式，开启一个
  listener。注册中心会每 5 秒定时检查是否有服务更新，如果有更新，向该服务的提供者发送一个 notify 消息，provider
  接收到 notify 消息后，即运行 NotifyListener 的 notify 方法，执行执行监听器方法。
代理模式
  Dubbo 扩展 JDK SPI 的类 ExtensionLoader 的 Adaptive实现是典型的动态代理实现。

```



#### Dubbo 配置文件是如何加载到 Spring 中的？

```Java
Spring 容器在启动的时候，会读取到 Spring 默认的一些 schema 以及Dubbo自定义的schema,每个 schema 都会对应一个自己的
  NamespaceHandler，namespaceHandler 里面通过 BeanDefinitionParser 来解析配置信息并转化为需要加载的 bean 对象

```



#### Dubbo SPI 和 java SPI区别？

```Java
JDK SPI
  JDK标准的SPI会一次性加载所有的扩展实现，有的扩展很耗时，但也没用上，很浪费资源。
Dubbo SPI
  1. 对 Dubbo 进行扩展，不需要改动 Dubbo 的源码
  2. 延迟加载，可以一次只加载自己想要加载的扩展实现
  3. 增加了对扩展点 IOC 和AOP的支持，一个扩展点可以直接 setter 注入其他扩展点。
  4. Dubbo 的扩展机制能很好的支持第三方 IOC 容器，默认支持 Spring Bean。

```



#### Dubbo 支持分布式事务吗？

```Java
目前暂时不支持，可通过 tcc-transaction 框架实现

TCC-Transaction 通过 Dubbo 隐式传参的功能，避免自己对业务代码的入侵。
```



#### Dubbo 可以对结果进行缓存吗？

```Java
为了提高数据访问的速度。Dubbo提供了声明式缓存，以减少用户加缓存的工作量。
  <dubbo:reference **cache="true"**/>
```



#### 你还了解别的分布式框架吗？

```Java
别的还有
  Spring的 spring Cloud
  facebook的 thrift
  Twitter的 finagle 等

```

