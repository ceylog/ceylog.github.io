---
layout:     post
title:      CAS logout
category: blog
description: CAS 登出

---

网友不停问为什么CAS Logout后，仍然能够访问应用？ 

http://dev2dev.bea.com.cn/bbs/thread.jspa?forumID=29304&threadID=37715&messageID=221727#221727 假设有webapp1, webapp2, cas server，webapp1, webapp2均受cas server保护，首先，在这里简单解释一下： 

* 第1种不能logout的情况：
 1. 登录了WebApp1，redirect到caserver，casserver认证后，再redirect到webapp1，ok！
 2. http方式 lougout casserver1，即http://yale_casserver:8080/cas/lougout 显示logout成功
 3. 访问webapp2，还能访问！这是非常正常的一种情况，因为你不通过https来注销，casserver怎么"杀"掉它通过https发给你的TGC Cookie? 

* 第2种不能logout的情况： 
 1. 登录了WebApp1，redirect到caserver,casserver认证后，再redirect到webapp1，ok！
 2. https方式 lougout casserver1，即https://yale_casserver:8443/cas/lougout 显示logout成功
 3. 访问webapp1，还能访问！访问webapp2，不能访问，重定向到casserver要求登录！这也是非常正常的一种情况，因为你已经能够访问，你继续可以继续访问，
CASLogout不能阻止你访问webapp1，它只能阻止你访问webapp2，因为你已经被允许访问webapp1，而webapp2则还没有，如果你在(1)的时候，顺带也访问webapp2，那么你的注销将毫无作用了，CAS无法阻止你访问这两个webapp，因为你有Service Ticket。 

如果你对此费解，那时因为你已为Logout就是退出系统，那我只能表示遗憾，因为CAS Logout的作用不是这样，它的作用是阻止你继续通过TGC（它简单地清楚了IE的TGC Cookie）来获取ST，阻止你获取通向其他web应用的Ticket。 

所以，用完webapp1的时候，注销，然后再关闭掉IE就彻底Logout了。