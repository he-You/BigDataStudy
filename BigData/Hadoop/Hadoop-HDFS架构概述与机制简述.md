###一.组成部分

1. NameNode: 存储文件的元数据,如文件名,文件目录结构,文件属性(生成时间,副本数,文件权限),
以及每个文件的块列表和块所在的DataNode

2. DataNode:在本地文件系统存储文件块数据,以及块数据的校验和(即所有数据的本体)

3. Secondary NameNode:用来监控HDFS状态的辅助后台程序,每隔一段时间获取HDFS元数据的快照;

注: 
1. 元数据(metaData):
大数据框架都是分布式的，可能每个角色都运行在各个不同的服务器上面，需要进行通信的时候就要需要网络的支持，而在我们客户端需要读一个文件的信息时，必须知道我们这个文件被分成了多少个block，各个block又分别存储在哪个服务器上，这种用于描述文件的信息被称为文件的元数据信息（metaData），而metaData就是存储在NameNode的内存中的

2. 为什么元数据需要存储在NameNode的内存中呢，答案很简单，存储在内存中意味着快，当然也会存在问题，就是如果NameNode宕机了，内存就无法读取了，此时为了防止这种情况出现，也为了加快NameNode从故障中恢复的速度，就设计了一个SecondaryNameNode的角色
   
3. Secondary NameNode作用:
- 备份NameNode中的元数据信息
- 提高NameNode的重启速度
- 必要的时候可作为新的NameNode

###二.HDFS机制
1. 心跳机制:心跳机制解决了HDFS集群间的通信问题，还是NameNode命令DataNode执行操作的途径

- master namenode启动之后，会开一个ipc server
- slave DataNode启动，连接NameNode，每隔3s向NameNode发送一个心跳，并携带状态信息
- NameNode通过对这个心跳的返回值来给DataNode传达任务指令

2. 心跳机制的作用:
- NameNode全权管理数据块的复制，它周期性从集群中的每个DataNode接收心跳信号和块状态报告（blockReport），接收到心跳信号意味着该DataNode节点工作正常，块状态报告包含了该DataNode上所有数据块的列表
- DataNode启动时向NameNode注册，通过后周期性地向NameNode上报blockReport，每3秒向NameNode发送一次心跳，NameNode返回对该DataNode的指令，如将数据块复制到另一台机器，或删除某个数据块等···而当某一个DataNode超过10min还没向NameNode发送心跳，此时NameNode就会判定该DataNode不可用，此时客户端的读写操作就不会再传达到该DataNode上
- hadoop集群刚开始启动时会进入安全模式（99.99%），就用到了心跳机制，其实就是在集群刚启动的时候，每一个DataNode都会向NameNode发送blockReport，NameNode会统计它们上报的总block数，除以一开始知道的总个数total，当 block/total < 99.99% 时，会触发安全模式，安全模式下客户端就没法向HDFS写数据，只能进行读数据。

2. 负载均衡
其实就是节点的增加或减少，或者节点的磁盘使用率高低的问题，主要就是通过网络进行数据的迁移工作以达到高可用率
触发命令:
```
$ HADOOP_HOME/sbin/start-balancer.sh -t 5%
```
其中:5%为磁盘的利用率差值，大于5%时会触发负载均衡策略