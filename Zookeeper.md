# Zookeeper

#### 概念

Zookeeper是一个开源的分布式的，为分布式框架提供协调服务的Apache项目

Zookeeper工作机制：（= 文件系统 + 通知机制）从设计模式角度来理解：是一个用于观察者模式设计的分布式服务管理框架，他负责存储和管理大家都关心的数据，然后接受观察者的注册，一旦这些数据的状态发生变化，Zookeeper就将负责通知已经在Zookeeper上注册的那些观察者做出相应的反应

Zookeeper的特点

1. 一个领导者，多个跟随者组成的集群
2. 集群中只要有半数以上节点存活，Zookeeper集群就能正常服务，所以Zookeeper适合安装奇数台服务器
3. 全局数据一致：每个server保存一份相同的数据副本，client无论连接到哪个server，数据都是一样的
4. 更新请求顺序执行，来自同一个client的更新请求按其发送顺序依次执行
5. 数据更新原子性：一次数据更新要么成功，要么失败
6. 实时性：在一定时间范围内，client能读到最新数据

应用场景：统一命名，统一配置管理，统一集群管理，服务器动态上下线，软负载均衡