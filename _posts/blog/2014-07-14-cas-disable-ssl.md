
---
layout:     post
title:      CAS 服务器端取消 https的配置 方法
category: blog
description: CAS 服务器端取消 https的配置 方法

---

 
需要修改的配置文件有：
WEB-INF/deployerConfigContext.xml 、 WEB-INF/spring-configuration/ticketGrantingTicketCookieGenerator.xml 、
WEB-INF\spring-configuration\warnCookieGenerator.xml
 
详细配置修改如下：
### 1 、 WEB-INF/deployerConfigContext.xml 在

    < bean class = "org.jasig.cas.authentication.handler.support.HttpBasedServiceCredentialsAuthenticationHandler"     p:httpClient-ref = "httpClient" />

增加参数 p:requireSecure="false" ，是否需要安全验证，即 HTTPS ， false 为不采用 如下：
    
    < bean class = "org.jasig.cas.authentication.handler.support.HttpBasedServiceCredentialsAuthenticationHandler" p:httpClient-ref = "httpClient" p:requireSecure= "false" />
      
### 2 、 WEB-INF/spring-configuration/ticketGrantingTicketCookieGenerator.xml 修改 p:cookieSecure="true" 为 p:cookieSecure=" false " ， 即不需要安全 cookie 如下部分：
    
    < bean id = "ticketGrantingTicketCookieGenerator" class = "org.jasig.cas.web.support.CookieRetrievingCookieGenerator"
       p:cookieSecure = " false "
       p:cookieMaxAge = "-1"
       p:cookieName = "CASTGC"
       p:cookiePath = "/cas" />
 
### 3 、 WEB-INF\spring-configuration\warnCookieGenerator.xml
修改 p:cookieSecure="true" 为 p:cookieSecure=" false " ， 即不需要安全 cookie 结果如下：
    
    < bean id = "warnCookieGenerator" class = "org.jasig.cas.web.support.CookieRetrievingCookieGenerator"
       p:cookieSecure = " false "
       p:cookieMaxAge = "-1"
       p:cookieName = "CASPRIVACY"
       p:cookiePath = "/cas" />