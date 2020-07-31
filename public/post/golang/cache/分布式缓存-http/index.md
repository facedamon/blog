
# 摘要

&emsp;&emsp;分布式缓存需要实现节点间通信，建立基于HTTP的通信机制是比较常见的做法。如果一个节点启动了HTTP服务， 那么这个节点就可以被其它节点访问。今天我们就为单机节点搭建HTTP Server.

&emsp;&emsp;不与其它部分耦合，我们将这部分代码放在新的`http.go`文件中，当前的代码结构如下: 

```shell
geec/
    |--lru/
        |--lru.go       // lru缓存淘汰策略
        |--lru_test.go
    |--byteview.go      // 缓存值的抽象
    |--cache.go         // 并发控制
    |--geec.go          // 服务与外部交互，控制缓存存储和获取的主流程
    |--http.go          // 提供被其它节点访问的能力
		
		&emsp;&emsp;首先我们创建一个结构体`HTTPPool`，作为承载节点间HTTP通信的核心数据(包括服务端和客户端，今天只实现服务端)。
		
