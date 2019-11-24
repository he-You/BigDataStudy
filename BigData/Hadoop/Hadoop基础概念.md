### **一. Hadoop介绍**

**1.Hadoop的定义**
- Hadoop是一个有Apache基金会开发的分布式系统基础架构
- 主要解决海量数据的存储(HDFS)和分析计算(MapReduce)问题
- 广义上是指Hadoop生态圈 

**2.主流三大发行版本:**
Apache(开源版本),Cloudera,Hortonworks

**3.Hadoop优势**
- 高可靠性:Hadoop底层维护多个数据副本,即使某个计算元素或存储出现故障,也不会导致数据的丢失
- 高扩展性:在集群建分配任务数据,可方便的扩展数以千计的节点
- 高效性:在MapReduce的思想下,Hadoop是并行工作的,以加快任务处理速度
- 高容错性:能够自动将失败的任务重新分配

###二. Hadoop组成
在Hadoop1.x版本中,主要组成有:
- MapReduce(计算+资源调度),
- HDFS(数据的存储),
- Common(辅助工具)

而在Hadoop2.x&3.x版本中:
- 分布式存储HDFS，
- 分布式计算MapReduce，
- 资源调度框架Yarn,
- Common(辅助工具)

也就是说最大变化就是为了解耦Yarn独立出来了