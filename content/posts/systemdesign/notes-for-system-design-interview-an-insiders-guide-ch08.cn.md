---
title: 【SD】CH08-设计URL短网址
summary: 本文为阅读Alex Xu的《系统设计面试》CH08的读书笔记
date: 2023-12-25
cover:
image: images/systemdesign.png
caption: "Generated using [OG Image Playground by Vercel](https://og-playground.vercel.app/)"

authors:
- admin

CNtags:
- 系统设计
---

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


