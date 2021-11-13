## kafka
### 功能
1. 解耦.
2. 可恢复性.  
系统的一部分组件失效,例如一个处理消息的进程挂掉,不会影响整个系统,加入队列的消息仍可以在系统恢复后被处理.
3. 缓冲.
控制和优化数据流经系统的速度.
4. 灵活性&削峰.

### 消费模式
1. 点对点  
生产者生产消息到Queue中,消费者从Queue中取出并消费,消费后queue中不在存储.
queue存在多个消费者,但是一条消息只能有一个消费者可以消费.
2. 发布/订阅
消费者消费后不会清除消息.所有消费者都会消费同一条消息.

### 基础架构
1. producer: 生产者.
2. consumer: 消费者.
3. consumer group: 消费者组,由多个consumer组成.消费者组是逻辑上的一个订阅者.消费者组之间互相不影响,只消费自己分区的消息,通常使用场景下生产者会将消息发布到所有分区. 常用场景:每个消费组多个消费者处于点对点模式,只能有一个消费消息,但多个消费组之间不影响.(多个服务订阅了一个消息,每个服务有多个实例.那每个服务的多个实例就是一个消费组,只有一个实例可以消费,但多个服务之间互不影响).
4. broker: 一台kafka服务器就是一个broker.一个集群由多个broker组成,可以容纳多个topic.
5. topic: 话题,可以理解为一个消费队列,是发布和订阅的对象.
6. partition: 为了扩展性,一个topic可以分布到多个broker上,此时一个topic会分为多个partition,每个partition是一个有序队列.
7. replica: 副本,为了保证集群中某个节点故障时该节点的partition数据不丢失,kafka提供了副本机制,一个topic的每个分区都有多个副本,一个leader和若干个follower.
8. leader: 每个分区多个副本会存在一个主,生产者消费者都直接面向leader.
9. folower: 每个分区的多个从,实时从leader中同步数据,保持和leader数据的同步.

### ZooKeeper
kafka使用ZooKeeper来进行成员管理、存放集群元数据、controller选举.在kip500后,kafka将不再依赖ZooKeeper.kip500是使用kafka社区自研的基于Raft的共识算法,替代Zookeeper.

### kafka存储
topic的每个partition都对应一个log文件,文件中存储的就是producer生产的数据.
生产的数据会被不断追加到log文件的末端,每条数据都有自己的offset,consumer组中每个consumer都会实时记录自己消费到哪个offset,出错恢复时,会从上次的位置继续消费.


### 零拷贝
