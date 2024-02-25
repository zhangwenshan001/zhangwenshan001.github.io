---
title: 【SD】CH10-设计通知系统
summary: 本文为阅读Alex Xu的《系统设计面试》CH10的读书笔记
date: 2024-01-08
cover:
image: images/systemdesign.png
caption: "Generated using [OG Image Playground by Vercel](https://og-playground.vercel.app/)"

authors:
- admin

CNtags:
- 系统设计
---

本章将会设计一个通知系统。
通知不仅仅包含推送通知，通知的三种形式为：
- 推送通知
- SMS通知
- 邮件通知

**第一步 理解问题，确定设计范围**

本例的设计需求：
- 支持上述三种通知
- 软实时，用户尽快收到通知。但高负载下可接受轻微延迟
- 支持设备：ios，android，laptop，pc
- 客户端和服务端均可触发通知
- 用户可自主设置是否关闭通知
- 通知的数量：每天10million推送，1million SMS消息，5million邮件

**第二步 high-level设计**

- 不同通知类型需要的组件
    - iOS
        - provider提供以下数据
            - device token
            - payload
        - APNs
        - device
    - Android
        - 与上述类似，替换FCM
    - SMS消息
        - 第三方的SMS服务例如Twilio，Nexmo
    - Email
        - Sendgrid，Mailchimp等商业服务
- 联系方式收集工作流
    - 用户安装或首次登录时记录数据到DB
    - 用户与设备可能是一对多
- 通知发送/接收工作流
    - Service 1 to N
    - 通知系统
    - 第三方服务
    - 客户端设备

存在的一些改进点
- SPOF
- 单个通知服务器难以扩展
- 性能瓶颈

改进
- 将数据库、缓存移出通知服务器
- 增加通知服务器，水平扩展
- 引入消息队列，解耦

改进后的工作流：
- Service 1 to N
- 通知系统
- 缓存
- DB
- MQ
- workers
- 第三方服务
- 客户端设备

**第三步 design deep dive**

- 可靠性
    - 如何防止数据丢失：持久化
    - 接收者是否会准确地收到一次通知：分布式的特性会导致重复，引入去重复机制减少重复发生率
- 额外的组件以及考虑点
    - 通知模板
    - 通知设置
    - 限流
    - 重试机制
    - 安全性
    - 监控队列通知
    - 事件追踪

**第四步 总结**

整理并回顾下上述的内容。

