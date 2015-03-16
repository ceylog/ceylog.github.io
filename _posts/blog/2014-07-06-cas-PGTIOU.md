---
layout:     post
title:      CAS-PGTIOU
category: blog
description: PGTION 作用，为什么要使用PGTIOU

---

CAS中的PGTIOU全称:Proxy Granting Ticket I Owe You
##PGTIOU作用：
    CAS调用Call back 返回 PGTIOU和PGT，其它人也可以调用该URL并传这两个参数，如果proxy不通过其它途径获取PGTIOU对call back返回的进行校验，就会造成被恶意干扰而取得不正确的PGT。 
     
## 为什么要PGTURL呢？为什么不是验证St时直接返回PGT？ 
     主要是因为：假如hacker获取了ST（有效的），那么其他人就可以通过ST到Server验证并获取PGT，这样导致所有应用都可以访问，而不只是ST所对应的服务了。 CAS要求PGTURL必须为Https，可以保证CAS回调时信任该URL（有PGTURL服务的证书）。