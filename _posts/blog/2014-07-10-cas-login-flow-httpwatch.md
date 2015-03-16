---
layout:     post
title:      CAS 登录流程httpwatch解析
category: blog
description: CAS 登录流程httpwatch解析

---

cas sso 登录流程解析，使用httpwatch查看
## 1. 第一次访问 http://localhost:8080/a , 
CLIENT：没票据且SESSION中没有Assertion所以跳转至CAS 

CAS：拿不到TGC故要求用户登录 
![image8](/images/cas/cas_clip_image008.jpg) 
## 2. 认证成功后回跳 
CAS：通过TGT生成ST发给客户端，客户端保存TGC，并重定向到http://localhost:8080/a 

CLIENT：带有票据所以不跳转只是后台发给CAS验证票据（浏览器中无法看到这一过程） 
![image9](/images/cas/cas_clip_image009.jpg)
## 3. 第一次访问 http://localhost:8080/b 
CLIENT：没票据且SESSION中没有消息所以跳转至CAS 

CAS：从客户端取出TGC，如果TGC有效则给用户ST并后台验证ST，从而SSO。【如果失效重登录或注销时，怎么通知其它系统更新SESSION信息呢？？TicketGrantingTicketImpl类grantServiceTicket方法里this.services.put(id, service);可见CAS端已经记录了当前登录的子系统】 
![image010](/images/cas/cas_clip_image010.jpg)
## 4. 再次访问 http://localhost:8080/a 
CLIENT：没票据但是SESSION中有(Assertion)消息故不跳转也不用发CAS验证票据，允许用户访问 
![image011](/images/cas/cas_clip_image011.jpg)

CAS Authentication Filter认证： 
    if (CommonUtils.isBlank(ticket) && request.setAttribute(CONST_CAS_ASSERTION, assertion)== null && !wasGatewayed) { 
     //即没有票据且没有SESSION通过信息则跳转 
    }

CAS Validation Filter认证： 

    if (CommonUtils.isNotBlank(ticket)) { 
        //即如果发现ST则发给CAS认证，否则不认证。 
        final Assertion assertion = this.ticketValidator.validate( 
                        ticket, constructServiceUrl(request, response)); 
        request.setAttribute(CONST_CAS_ASSERTION, assertion);//认证通过后将信息保存SESSION中
    }