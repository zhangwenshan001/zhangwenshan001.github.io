---
title: 《系统设计面试》读书笔记
summary: 本文为阅读Alex Xu的《系统设计面试》一书过程中做的读书笔记的总结
date: 2023-12-20
cover:
  image: images/systemdesign.png
  caption: "Generated using [OG Image Playground by Vercel](https://og-playground.vercel.app/)"

authors:
  - admin

CNtags:
  - 系统设计
---

最近一个多月一直在断断续续阅读本书。书中会涉及到一些系统设计相关的技术内容，但个人认为该书更倾向于是一本关于面试技巧的书籍。关于具体的技术细节，如果想要深入了解，那么还是需要另外找其它材料来做补充（当然作者其实在书中也推荐了很多相关材料）。
由于之前没有中文版，自己在阅读过程中也进行了一些翻译，同时记了一些读书笔记，方便在有需要时快速回忆。（昨天看到作者本人在社交媒体上发布了中文版出版的消息，感兴趣的话也请支持正版w）

---
## 第一章 从零扩展到百万用户

简单概括系统的演进过程：
1. 单机（仅有web/client，CDN，以及server）
2. 数据库服务器与应用服务器分离
3. 添加负载均衡器（好处是故障转移，易于横向扩展等）
4. 数据库主从复制，读写分离（提升性能，可靠性及可用性）
5. 添加缓存
6. 使用CDN缓存静态资源
7. 分离会话数据，保持web层无状态（便于扩展）
8. 引入多数据中心
9. 使用消息队列实现异步通信
10. 添加日志监控，指标以及自动化工具
11. 数据库sharding

总结感想：第一章是在high-level上概括了系统演进的过程。在系统设计面试中也可以按照这个思路，从最简单的设计入手，逐步分析可能产生的问题和瓶颈，以及可以通过怎样的手段来解决。

---
## 第二章 估值

（back-of-the-envelope estimation不知道该怎么准确翻译）

第二章主要提到了一些对系统容量或性能进行评估时较为常见的数据，如：

- **2的次方与数据量单位的对应关系，可方便速算**

一些典型场景的latency，从中可以提取出一些结论来帮助设计（如memory比disk快，应尽量减少磁盘寻址，在传输数据前可尽量对其压缩等等）
	
- **代表可用性的百分比数据**

  - 三个九：平均每天宕机约1.44分钟，每年约8.77小时
  - 四个九：平均每天宕机约8.64秒，每年约52.6分钟


常被问到的估计数据包括：
- DAU，QPS，峰值QPS
- 单条数据的大小，每天的数据量，5年的数据量
- 缓存，服务器数量等等

---
## 第三章 面试框架

第三章描述了一个系统设计面试的面试框架。
	
**step.1 理解问题，确定设计范围**

  不要一上来就开始设计，先与面试官进行沟通。对系统的功能，需求，用户量，预计增长速度和规模等等进行充分的沟通后再开始设计。

**step.2 提出一个high-level的设计，并确认与面试官达成一致**

  先不要急着讨论细节，提出初步的设计方案，并积极与面试官沟通寻求反馈。（API endpoints和DB schema是否要在这个环节进行讨论要根据实际情况来定）
	

**step.3 design deep dive**


  注意选择和明确具体要deep dive的topic。也要注意时间管理，有些topic并不值得花费太多时间来讨论。
	

**step.4 总结**

一些follow up或自由讨论其它问题，包括但不限于：
- 系统瓶颈，潜在改进点
- 对故障，错误等讨论
- 监控指标，错误日志，发布
- 如何处理下一个scale curve
- ...
	
对于45mins的面试，建议的时间分配
- 第一步 3-10 mins
- 第二步 10-15 mins
- 第三步 10-25 mins
- 第四步 3-5 mins

---
## 第四章 设计限流器

第四章开始将会举一些具体的实例来讲解，本章介绍了限流器的设计。

限流器的优点：
  - 防止Dos攻击
  - 降低成本
  - 减少服务器负载
  - ...

根据第三章提出的框架来推进。

**第一步：沟通，明确需求**
  - 需要客户端还是服务端限流？
  - 是否是分布式系统？
  - 单独的限流服务还是实现在应用代码中？
  - 基于IP还是用户ID进行限流？
  - 系统规模是多大？
  - 若被限流，是否需要告知用户？
  - ...

**第二步：提出high-level设计**

根据需求确定将功能实现在客户端or服务端，api gateway中还是添加一个middleware。
另外可明确使用的算法，常见算法有：
  - Token bucket
  - Leaking bucket
  - Fixed window counter
  - Sliding window log
  - Sliding window counter

了解上述算法各自的优缺点

**第三步：design deep dive**
  - 限流规则如何创建，如何存储
  - 被限流后如何告知用户（可以使用一些headers）
  - 分布式环境下存在的问题（并发更新，数据同步等）
  - 性能优化
  - 监控，观察分析设置的规则和算法是否有效

**第四步：总结和扩展**
  - OSI模型应用层以外也可做限流
  - 客户端应当怎么做来防止限流
  - ...

---
## 第五章 一致性哈希

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

---
## 第六章 设计一个k-v存储

features如下：
1. 单个k-v小于10k
2. 支持大量数据
3. 高可用
4. 高可扩展
5. 自动扩展
6. 可调节的一致性
7. 低延迟

	
CAP理论
  - CP/AP/CA系统分别的特征
  - 分布式系统必须保证P
  - CP/AP有代表性的系统举例
	
数据分区（存储大量数据的能力、增量扩容）：采用第五章中提到的一致性哈希
	
数据复制（高可用读）：同样利用哈希环，在环上的多个节点复制数据
	
可调节的一致性（tunable consistency）：quorum consensus方式去保证读和写操作的一致性。（调整N W R数值）
  - W+R>N：保证强一致性
  - R=1 W=N：快速读
  - W=1 R=N：快速写
	
一致性模型：关于强一致性和弱一致性（最终一致性）
	
解决不一致问题（高可用写）：版本控制&向量时钟(vector clock)
	
故障检测：gossip协议
	
处理临时故障： sloppy quorum/hinted handoff
	
处理永久性故障：merkle tree
	
（本章有点理论和抽象，需要再巩固）

---
## 第七章 设计一个唯一ID生成器
	
**第一步，提问明确需求**
本例中需求：
  - id唯一
  - id只含数字
  - 长度64bit
  - 可通过日期排序
  - 每秒支持生成10000个id
	
**第二步 high-level设计**

考虑四种方法：
  - Multi-master replication(db auto increment by k)
  - UUID (128bit 优点各服务器可单独负责生成id)
  - Ticket Server(单独db专门负责id自增，缺点是单点故障)
  - Twitter snowflake approach（符号位0+41位timestamp+5位datacenterID+5位machineID+12位序列号）
	
**第三步 deep dive**
  - Twitter snowflake approach满足需求
  - 41位timestamp够用69年
  - 12位序列号，每台机器每毫秒可支持4096个新id
	
**第四步 总结**

其它讨论：
  - 时钟同步
  - 各个section的长度可根据需求适当调整
  - 高可用

---
## 第八章 设计url短网址
	
**第一步：明确需求**
  - 可通过long url生成short url
  - 可通过short url重定向回long url
  - 支持每天1亿（100million）url
  - short url尽可能短
  - short url支持0-9/a-z/A-Z
  - short url无需更新或删除
  - 高可用，可扩展，容错
	
Back of the envelope estimation
  - 写操作：1million/24/3600 = 1160/s
  - 读操作：假设为写操作的10倍： 11600/s
  - 假设存储10年的数据：100million * 365 * 10 = 365 billion
  - 假设平均长度100bytes，则总存储 365billion * 100 bytes = 36T（书里好像算错了）
	
**第二步 high-level设计**

API endpoints
```sh
POST /api/v1/data/shorten
# 请求body {longUrl: longUrl}   
# 返回 short url
```

```sh
GET /api/v1/shortUrl
# 返回 longUrl
```
URL重定向：301（永久，负载小） vs 302（临时，便于收集和分析数据）
	
**第三步：deep dive**

数据模型

SQL：{id, longUrl, shortUrl}
（补充：NoSQL，需要分别存储两张表）


哈希
  - length≥6 → 56.8 billion
  - length≥7 → 3.6 trillion

两种方式：
1. Hash + 冲突解决
2. Base62

URL重定向：读多写少，加LB，加缓存
	
**第四步 总结**

可讨论的点：
  - 限流
  - web服务器扩展（stateless扩展容易）
  - db扩展
  - 数据收集分析
  - 可用性, 一致性, 可靠性

---
## 第十一章 设计news feed
	
**第一步 提问，理解问题**
	
**第二步 high-level设计**

关键设计为两块：feed发布和feed获取（可把endpoint和参数也设计一下）
  - feed发布：可包含post service & fanout service & 对应db&cache
  - feed获取：news feed service & 对应cache
	
**第三步 深入讨论**
1. fanout的两种方式讨论，具体存储的数据结构是怎样的，pros&cons分别是什么（可以结合两种方式对不同场景进行优化）
	
2. 媒体文件的存储，CDN
（补充，可以讨论file storage的优点）
	
3. 缓存（feed缓存，热门content缓存，社交关系，用户行为缓存，计数器）
	
**第四步 总结**

其它可讨论的点：
  - 垂直/水平扩展
  - SQL&NoSQL
  - 主从复制&replica
  - sharding&一致性模型
  - stateless
  - 多数据中心 
  - MQ
  - 监控Metrics

补充：本题做mock时存在的问题
1. 对db和cache的选择讨论较少，可有针对性加强
2. 尽量不要长时间沉默思考，speak out loudly or ask questions