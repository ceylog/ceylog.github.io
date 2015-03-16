---
layout:     post
title:      SSO实现机制:Cookie机制和Session机制
category: blog
description: SSO实现机制:Cookie机制和Session机制

---

SSO 的实现机制不尽相同，大体分可为 Cookie 机制和 Session 机制两大类。

* WebLogic 通过 Session 共享认证信息。 Session 是一种服务器端机制，当客户端访问服务器时，服务器为客户端创建一个惟一的 SessionID ，以使在整个交互过程中始终保持状态，而交互的信息则可由应用自行指定，因此 用 Session 方式实现 SSO ，不能在多个浏览器之间实现单点登录，但却可以跨域 。
* WebSphere 通过 Cookie 记录认证信息。 Cookie 是一种客户端机制，它存储的内容主要包括 : 名字、值、过期时间、路径和域，路径与域合在一起就构成了 Cookie 的作用范围，因此 用 Cookie 方式可实现 SSO ，但域名必须相同 。

目前 大部分 SSO 产品采用的是 Cookie 机制。 目前能够找到的最好的开源单点登录产品 CAS 也是采用 Cookie 机制。 CAS 单点登录系统最早由耶鲁大学开发。 2004 年 12 月， CAS 成为 JA-SIG 中的一个项目。 JA-SIG 的全称是 Java Architectures Special Interest Group ，是在高校中推广和探讨基于 Java 的开源技术的一个组织。 CAS 的优点很多，例如设计理念先进、体系结构合理、配置简单、客户端支持广泛、技术成熟等等。