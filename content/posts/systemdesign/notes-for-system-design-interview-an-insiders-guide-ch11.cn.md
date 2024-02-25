---
title: 【SD】CH11-设计news feed
summary: 本文为阅读Alex Xu的《系统设计面试》CH11的读书笔记
date: 2024-01-08
cover:
image: images/systemdesign.png
caption: "Generated using [OG Image Playground by Vercel](https://og-playground.vercel.app/)"

authors:
- admin

CNtags:
- 系统设计
---
**第一步 提问，理解问题**

本例设计需求：
- 支持mobile/web
- 主要功能：发布，查看news feed
- 简单起见，按时间倒序
- 一个用户可以有5000个好友
- 100 millions DAU
- 可包含图像，视频媒体文件

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
