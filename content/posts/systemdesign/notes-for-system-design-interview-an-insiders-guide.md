---
title: Notes for System Design
summary: 
date: 2023-12-20
cover:
  image: images/systemdesign.png
  caption: "Generated using [OG Image Playground by Vercel](https://og-playground.vercel.app/)"
authors:
  - admin

tags:
  - SystemDesign
---

Recently I'm reading [System Design Interview](https://www.amazon.co.jp/System-Design-Interview-insiders-Second/dp/B08CMF2CQF) written by Alex Xu. This book covers some technical topics related to system design, but personally I think it is more like a book about interview skills. If you want to dive deep into details about the technologies mentioned in this book, it will be necessary to refer to other materials (the author actually recommends a lot of related materials in the book as well).

In this article, I will record some reading notes for this book.

## Chapter 1: Scale From Zero to Millions of Users

This chapter introduces the evolution of a system:
1. Single server setup（only web/client, CDN and the web server）
2. Separate web/mobile traffic (web tier) and database (data tier) servers
3. Add load balancer（solve no failover issue; improve the availability）
4. Database replication（better performance; Reliability; High availability）
5. Add cache
6. Use content delivery network (CDN)
7. Keep the web tier stateless (eg. store session data in the persistent storage) 
8. Multi-data center setup
9. Use message queue to support asynchronous communication
10. Logging, metrics, automation
11. Database scaling (Sharding)

## Chapter 2: Back-of-the-envelope Estimation