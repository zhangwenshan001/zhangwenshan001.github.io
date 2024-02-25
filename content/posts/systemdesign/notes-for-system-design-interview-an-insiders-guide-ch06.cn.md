---
title: 【SD】CH06-设计key-value存储
summary: 本文为阅读Alex Xu的《系统设计面试》CH06的读书笔记
date: 2023-12-23
cover:
image: images/systemdesign.png
caption: "Generated using [OG Image Playground by Vercel](https://og-playground.vercel.app/)"

authors:
- admin

CNtags:
- 系统设计
---

Features如下：
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

