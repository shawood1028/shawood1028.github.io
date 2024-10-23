---
title: Kafka基础(一)
date: 2024-10-16
tags:
    - 技术
    - kafka
---

Kafka是一个基于发布订阅的分布式消息系统

1. Kafka核心功能
    1. 消息系统
    2. 存储系统
    3. 流处理

2. Kafka的特性以及对应设计实现
    - 高性能
        - 分区，分段，索引：基于分区机制提供并发控制能力。分段，索引提升了数据读写的查询效率。
        - 顺序读写：使用顺序读写提升磁盘IO性能。
        - 零拷贝：利用零拷贝技术，提升网络IO效率。（零拷贝是指数据在磁盘和网络进行传输时避免昂贵的内核态数据拷贝，从而实现快速的数据传输。）
        - 页缓存：利用操作系统的PageCache来缓存数据。（空间换时间）
        - 批量读写：批量读写可以有效提升网络IO效率。
        - 数据压缩：Kafka支持数据压缩，可以有效提升网络IO效率。
        - pull模式：Kafka架构基于pull模式，可以自主控制消费策略，提升传输效率。
    - 高可用
        - 持久化：Kafka所有的消息都存储在磁盘，天然支持持久化
        - 副本机制：Kafka的Broker集群支持副本机制，可以通过冗余保证其整体的可用性。
        - 选举Leader： Kafka基于Zookeeper支持选举Leader（3.0版本之后Kafka也可以使用Kraft），实现故障转移能力。
    - 伸缩性
        - 分区： Kafka的分区机制使得其具有良好的伸缩性。

2. Kafka系统架构
broker——>topic——>partition——>segment 
    1. 抽象组件
        - broker（实例节点）
            1. 负责partition中消息的读写操作
        - Topic
            - 不同topic的消息是物理隔离的，同一个主题的消息保存在一个或多个broker上,topic可以认为是一类信息,每个topic将被分成一个或多个partition
        - partition
            每个partition都是一个有序的，不可变的记录序列,不断附加到机构化的提交日志中
            partition用一个成为偏移(offset)的顺序ID号，唯一地标识分区中的每一个记录
            partition在存储层面是append log文件
            partition可设置备份到一或多个实例节点上
            partition充当并行单位
            partition物理上由多个segment组成。 segment file组成：由2大部分组成，分别为index file和data file
            - 补充
                自定义分区策略常见的负载均衡算法有随机算法，轮训算法，最小活跃数算法，源地址哈希算法。
3. 特点
    1. 
    2. 发布订阅模式
    3. 数据存储在磁盘上,具有很高的容错性
    4. 顺序读取
    5. 每条记录都是key+value+时间戳格式
4. 补充
    1. client server 通信通过TCP协议完成
    2. 基于replicated方案,那么就意味着需要对多个备份进行调度;每个partition都有一个server为"leader";leader负责所有的读写操作,如果leader失效,那么将会有其他follower来接管(成为新的leader);follower只是单调的和leader跟进,同步消息即可.由此可见作为leader的server承载了全部的请求压力,因此从集群的整体考虑,有多少个partitions就意味着有多少个"leader",kafka会将"leader"均衡的分散在每个实例上,来确保整体的性能稳定。


5. TODO
    - 压缩
    - 幂等性
    - 事务


相关书籍:

- 《Kafka 权威指南》
- 《深入理解 Kafka：核心设计与实践原理》
- 《Kafka 技术内幕》

参考：
    - https://dunwu.github.io/bigdata-tutorial/kafka