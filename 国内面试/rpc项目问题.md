





实现的rpc跟dubbo有什么区别，为什么不用dubbo要用这个rpc？  
如果把rpc改成分布式架构，应该怎么改 ，哪一部分要改成分本式？注册中心改成分布式，客户端的接口的幂等性和多个分布式注册中心之间的数据一致性怎么保证或者平衡？心跳检测是在一个单线程定时器里，这一部分要不要分布式一下？  
服务端节点应该怎么优雅地上下线？客户端什么时候会拉取最新的服务节点？拉取的是所有的服务节点还是部分服务节点？为什么？  
客户端和服务端的定时器的区别？  
rpc为什么要基于tcp？能不能用udp？该怎么改或者定怎样的传输协议，可靠性传输相关的要不要考虑一下？  
能不能在除了rpc的应用层，在别的层（比如传输层和网络层）做负载均衡？大概该怎么做？

自己重写了一下代码，发现虽然说到了SPI的思想跟抽象，但是目前代码实现中并没有用到SPI相关的类，还是通过文件读取跟类加载来实现的具体接口的具体实现注入？这就是spi。 spi核心就是ServiceLoad 




想问下哪里有文档，up说的git_doc也没看到啊
在xhy-rpc-master\Xhy-Rpc\doc里


up好，看rpc项目都说核心是他的动态代理，但是我看虽然是重写了invoke方法，，也说用到了代理设计模式，但是感觉没有用到动态代理啊，可能我对这个动态代理的理解没有那么多哈，就想问一下不太懂，也可能我理解的太肤浅谢了
代理的体现是对目标方法的增强行为，而动态代理在java中分别为jdk动态代理和cglib动态代理。这实现方式就是动态代理