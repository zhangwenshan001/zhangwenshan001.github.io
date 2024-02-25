---
title: 【SD】CH04-设计限流器
summary: 本文为阅读Alex Xu的《系统设计面试》CH04的读书笔记
date: 2023-12-21
cover:
image: images/systemdesign.png
caption: "Generated using [OG Image Playground by Vercel](https://og-playground.vercel.app/)"

authors:
- admin

CNtags:
- 系统设计
---

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
