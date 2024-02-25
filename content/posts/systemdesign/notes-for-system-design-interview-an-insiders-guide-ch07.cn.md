---
title: 【SD】CH07-设计分布式系统中的unique id生成器
summary: 本文为阅读Alex Xu的《系统设计面试》CH07的读书笔记
date: 2023-12-24
cover:
image: images/systemdesign.png
caption: "Generated using [OG Image Playground by Vercel](https://og-playground.vercel.app/)"

authors:
- admin

CNtags:
- 系统设计
---

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

