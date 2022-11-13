## 引入消息队列后 如何保证高可用性
> 持久化、事务、签收、 以及带复制的 Leavel DB + zookeeper 主从集群搭建  

## 异步投递   Async  send
> 对于一个慢消费者，使用同步有可能造成堵塞，消息消费较慢时适合用异步发送消息 activemq  支持同步异步 发送的消息，默认异步。当你设定同步发送的方式和 未使用事务的情况下发持久化消息，这时是同步的。 如果没有使用事务，且发送的是持久化消息，每次发送都会阻塞一个生产者直到 broker 发回一个确认，这样做保证了消息的安全送达，但是会阻塞客户端，造成很大延时 。 在高性能要求下，可以使用异步提高producer 的性能。但会消耗较多的client 端内存，也不能完全保证消息发送成功。在 useAsyncSend = true 情况下容忍消息丢失
> 通过回调感知消息正常到达。

## 延迟投递和定时投递

## ActiveMQ 的消息重试机制
> 超过6次标记为有毒消息，broker将消息放入死信队列

## 如何保证消息不被重复消费，幂等性的问题
> 如果消息是做数据库的插入操作，给这个消息一个唯一的主键，那么就算出现重复消费的情况，就会导致主键冲突，避免数据库出现脏数据 。 如果不是，可以用redis 等的第三方服务，给消息一个全局 id ，只要消费过的消息，将 id ，message 以 K-V 形式写入 redis ，那消费者开始消费前，先去 redis 中查询有没消费的记录即可。

## Reference
* https://note.youdao.com/web/#/file/WEB1277b8f2da90c224c2c7a6a3175b2ea5/note/54AA1E33A4AE4AC9BA77EAA4384370B4/