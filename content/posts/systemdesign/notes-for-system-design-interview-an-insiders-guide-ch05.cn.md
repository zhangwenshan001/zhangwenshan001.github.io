---
title: 【SD】CH05-设计一致性哈希
summary: 本文为阅读Alex Xu的《系统设计面试》CH05的读书笔记
date: 2023-12-22
cover:
image: images/systemdesign.png
caption: "Generated using [OG Image Playground by Vercel](https://og-playground.vercel.app/)"

authors:
- admin

CNtags:
- 系统设计
---

第五章讲解了一致性哈希。

哈希函数存在rehashing的问题。服务器数量变化时简单的模运算会导致大量的key需要重新映射，因此有了一致性哈希算法。一致性哈希算法平均只需要重新映射k/n个key

假设使用`SHA-1`作为哈希函数。

**哈希环**：0~2^160-1首尾相连成为哈希环。
- 根据服务器IP或名称将服务器映射到哈希环上
- 将key也响应地映射到环上

**查找过程**：对于某个key，从其位置顺时针找到的第一个服务器即为该key所在服务器

**添加服务器**：假设在`s0`和`s1`之间添加一个`s4`，则只需把`s0~s4`之间的key重新映射给`s4`

**删除服务器**：同样，假设删除`s1`，则只需把`s0~s1`之间的key重新映射给`s2`

**存在的两个问题**：
- 每个服务器的分区大小不一致
- key分布若不均匀，服务器映射也会不均匀

**解决方法**：虚拟节点。虚拟节点越多，数据分布越均匀（tradeoff：需要更多的空间来存储虚拟节点相关数据）

**总结**：

- 一致性哈希优点：
    - 服务器数量变化时重新分配的key少
    - 数据分布均匀
    - 缓解热点key

- 一致性哈希在现实中的应用实例：
    - Amazon Dynamo 数据库的分区组件
    - Apache Cassandra 中跨集群的数据分区
    - Discord 聊天应用程序
    - Akamai 内容交付网络
    - Maglev 网络负载均衡器
