# dubbo-registry 模块
## 模块结构
![](https://ws2.sinaimg.cn/large/006tNc79ly1g1udy6htk3j30i809gdgu.jpg)
### dubbo-registry-api
为什么先看dubbo-registry-api ？当然是想看它到底做了什么，而不是它怎么做的。
![](https://ws3.sinaimg.cn/large/006tNc79ly1g1ue1nmjszj30kg0g23zy.jpg)
快速了解到registry模块干了几件事情：
- integration 集成，看起来像是和其他模块的交互
- retry 失败重试
- status 包的定义有点泛。点进去看，其实就是对所有的Registry进行了遍历，和可达性检查。
- support 包的定义有点泛。点进去看,具体看
    - AbstractRegistry
![](https://ws4.sinaimg.cn/large/006tNc79ly1g1ughiyl1qj30u00zyq83.jpg)
        - Registry:
            - Node: node主要定义了网络客户端节点所有的特性，是否可达，怎样释放资源
            - RegistryService: 服务提供者的注册，取消注册，消费者订阅，解除订阅，服务查找。
        - 除了Registry 接口定义的行为外，AbstractRegistry 多了缓存的一些行为。这些缓存机制使得，注册中心服务节点挂掉后，依然可以从本地获取
        服务提供者列表.
    - ConsumerInvokerWrapper:对invoker 做了一些增强,添加了Url的一些属性，这是从JVM到网络Distribution的基础。
    - ProviderInvokerWrapper:同ConsumerInvokerWrapper。
    - FailbackRegistry:
    ![](https://ws2.sinaimg.cn/large/006tNc79ly1g1ugsiak8hj30u00yddnw.jpg)
    FailbackRegistry主要维护了注册，取消注册，消费者订阅，解除订阅 的相关失败任务。
    - ProviderConsumerRegTable:维护了服务提供者和接口，服务消费者和接口的关系。
        - public static ConcurrentHashMap<String, ConcurrentMap<Invoker, ProviderInvokerWrapper>> providerInvokers = new ConcurrentHashMap<>();
        - public static ConcurrentHashMap<String, Set<ConsumerInvokerWrapper>> consumerInvokers = new ConcurrentHashMap<>();
- NotifyListener: 观察者模式中的被观察者。用来notify 不同类别的URL变动。
