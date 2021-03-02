# 分布式唯一 ID



#### 概述

并发量大时，业务系统可提前批量生成 ID 存储到 Redis，以减轻大量获取 ID 时业务系统压力过大性能问题。ID 生成服务器通过 Redis 分布式锁策略保证同一时刻只有一台调度以避免 ID 重复。



#### ID 生产者

ID 生产者一般采用调度任务定时生产，预先存储在 Redis 队列中，借用 Redis LPOP 原理，右边 push 存储，获取 lpop 返回队列中左侧第一个元素并删除（先进先出）



queueSize 定义每次生成 Redis 队列的大小

redis.lSize 队列目前大小（设定生成的触发时机）

Ids id 集合

redis.lRightPushAll("idQueue", ids)



#### ID 消费者

redis.lleftPop("idQueue")



