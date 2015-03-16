---
layout:     post
title:      CAS 服务器端配置文件
category: blog
description: CAS 服务器端配置文件

---

CAS 服务器端主要配置文件如下：

* login-webflow.xml：其中内容指定了当访问cas/login时的程序流程，初始“initialFlowSetup”
* cas-servlet.xml：servlet与class对应关系
* deployerConfigContext.xml：认证管理器相关
* cas.properties：系统属性设置
* applicationContext.xml：系统属性相关
* argumentExtractorsConfiguration.xml：不是很了解它的用途
* ticketExpirationPolicies.xml：ticket过期时间设置
* ticketGrantingTicketCookieGenerator.xml：TGT cookie属性相关，是否支持http也在这儿修改
* ticketRegistry.xml：保存ticket的类相关设置
* uniqueIdGenerators.xml：ticket自动生成类设置
* warnCookieGenerator.xml：同ticketGrantingTicketCookieGenerator.xml，生成的 cookie名为CASPRIVACY

## /login ：
当访问/login时，会调用login-webflow.xml中的流程图： 
![cas flow](/images/cas/cas_clip_image006.jpg)    
##/serviceValidate: 
对应的处理类是org.jasig.cas.web.ServiceValidateController，主要负责对service ticket的验证，失败返回casServiceValidationFailure.jsp，成功返回casServiceValidationSuccess.jsp对service ticket的验证是通过client端向server端发送http（或https）实现的逻辑： 

1. 通过由client端传来的ticket到DefaultTicketRegistry中获取缓存的ServiceTicketImpl对象，并判断其是否已经过期（ST过期时间默认是5分钟，TGT默认是2个小时，可以在ticketExpirationPolicies.xml中进行修改）以及与当前service的id是否相一，以上都满足则表示验证通过。
2. 通过ServiceTicketImpl对象获取到登录之后的Authentication对象，借助于它生成ImmutableAssertionImpl对象并返回
3. 成功返回

## CAS数据流程 
Credentials-->Principal-->Authentication 

## 定义自己的AuthenticationHandler 
在中心认证进行认证的过程中会调用deployerConfigContext.xml中设置的AuthenticationHandler来进行认证工作。 
  
    <property name="authenticationHandlers">   
        <list>   
            <!--  
                This is the authentication handler that authenticates services by means of callback via SSL, thereby validating a server side SSL certificate.   
            -->   
            <bean class="org.jasig.cas.authentication.handler.support.HttpBasedServiceCredentialsAuthenticationHandler"  
                p:httpClient-ref="httpClient" />   
            <!--   
                 This is the authentication handler declaration that every CAS deployer will need to change before deploying CAS    
                 into production.  The default SimpleTestUsernamePasswordAuthenticationHandler authenticates UsernamePasswordCredentials   
                 where the username equals the password.  You will need to replace this with an AuthenticationHandler that implements your   
                 local authentication strategy.  You might accomplish this by coding a new such handler and declaring   
                 edu.someschool.its.cas.MySpecialHandler here, or you might use one of the handlers provided in the adaptors modules.   
                -->   
            <bean   
                class="org.jasig.cas.authentication.handler.support.SimpleTestUsernamePasswordAuthenticationHandler" />   
            <bean class="com.goldarmor.live800.cas.Live800CasAuthenticationHandler">   
                <property name="dataSource" ref="casDataSource" />   
            </bean>   
        </list   
    </property> 
    <property name="authenticationHandlers"> 
        <list> 
            <!--
            This is the authentication handler that authenticates services by means of callback via SSL, thereby validating
            a server side SSL certificate. 
            --> 
            <bean class="org.jasig.cas.authentication.handler.support.HttpBasedServiceCredentialsAuthenticationHandler" 
            p:httpClient-ref="httpClient" /> 
            <!-- 
            This is the authentication handler declaration that every CAS deployer will need to change before deploying CAS 
            into production.  The default SimpleTestUsernamePasswordAuthenticationHandler authenticates UsernamePasswordCredentials 
            where the username equals the password.  You will need to replace this with an AuthenticationHandler that implements your 
            local authentication strategy.  You might accomplish this by coding a new such handler and declaring
            edu.someschool.its.cas.MySpecialHandler here, or you might use one of the handlers provided in the adaptors modules. 
            --> 
            <bean 
            class="org.jasig.cas.authentication.handler.support.SimpleTestUsernamePasswordAuthenticationHandler" /> 
            <bean class="com.goldarmor.live800.cas.Live800CasAuthenticationHandler"> 
            <property name="dataSource" ref="casDataSource" /> 
            </bean> 
        </list> 
    </property> 

如上，我们定义了3个AuthenticationHandler，这正是CAS的一个，通过配置，我们可以实现针对不同的应用提供不同的认证方式，这样可以实现任意的中心认证。再来看看AuthenticationHandler的代码
  
    /**  
    * Method to determine if the credentials supplied are valid.  
    *   
    * @param credentials The credentials to validate.  
    * @return true if valid, return false otherwise.  
    * @throws AuthenticationException An AuthenticationException can contain  
    * details about why a particular authentication request failed.  
    */  
      
    boolean authenticate(Credentials credentials)   
        throws AuthenticationException;   
      
    /**  
    * Method to check if the handler knows how to handle the credentials  
    * provided. It may be a simple check of the Credentials class or something  
    * more complicated such as scanning the information contained in the  
    * Credentials object.  
    *   
    * @param credentials The credentials to check.  
    * @return true if the handler supports the Credentials, false othewrise.  
    */  
    boolean supports(Credentials credentials); 
    /** 
     * Method to determine if the credentials supplied are valid. 
     * 
     * @param credentials The credentials to validate. 
     * @return true if valid, return false otherwise. 
     * @throws AuthenticationException An AuthenticationException can contain 
     * details about why a particular authentication request failed. 
     */
    boolean authenticate(Credentials credentials) 
        throws AuthenticationException;
    /** 
     * Method to check if the handler knows how to handle the credentials 
     * provided. It may be a simple check of the Credentials class or something 
     * more complicated such as scanning the information contained in the 
     * Credentials object. 
     * 
     * @param credentials The credentials to check. 
     * @return true if the handler supports the Credentials, false othewrise. 
     */ 
    boolean supports(Credentials credentials); 

我们要做的就是实现这俩个方法而已，特别提醒：可以在cas-servlet.xml中设置你所使用的Credentials，如下：（其中的p:formObjectClass值，如果不指定默认使用UsernamePasswordCredentials）
  
    <bean id="authenticationViaFormAction" class="org.jasig.cas.web.flow.AuthenticationViaFormAction"  
        p:formObjectClass="com.goldarmor.live800.cas.Live800CasCredentials"  
        p:centralAuthenticationService-ref="centralAuthenticationService"  
        p:warnCookieGenerator-ref="warnCookieGenerator" /> 
    <bean id="authenticationViaFormAction" class="org.jasig.cas.web.flow.AuthenticationViaFormAction" 
        p:formObjectClass="com.goldarmor.live800.cas.Live800CasCredentials" 
        p:centralAuthenticationService-ref="centralAuthenticationService" 
        p:warnCookieGenerator-ref="warnCookieGenerator" />

## 定义自己的credentialsToPrincipalResolvers
通过AuthenticationHandler的认证后，会调用在deployerConfigContext.xml中配置的credentialsToPrincipalResolvers来处理Credentials，生成Principal对象：
  
    <property name="credentialsToPrincipalResolvers">   
        <list>   
            <!--   
                UsernamePasswordCredentialsToPrincipalResolver supports the UsernamePasswordCredentials that we use for /login    
                by default and produces SimplePrincipal instances conveying the username from the credentials.   
                If you've changed your LoginFormAction to use credentials other than UsernamePasswordCredentials then you will also   
                need to change this bean declaration (or add additional declarations) to declare a CredentialsToPrincipalResolver that supports the   
                Credentials you are using.   
            -->              
            <bean   
                class="org.jasig.cas.authentication.principal.UsernamePasswordCredentialsToPrincipalResolver" />   
            <!--   
                HttpBasedServiceCredentialsToPrincipalResolver supports HttpBasedCredentials.  It supports the CAS 2.0 approach of   
                authenticating services by SSL callback, extracting the callback URL from the Credentials and representing it as a   
                SimpleService identified by that callback URL.   
                If you are representing services by something more or other than an HTTPS URL whereat they are able to   
                receive a proxy callback, you will need to change this bean declaration (or add additional declarations).   
                -->   
            <bean   
                class="org.jasig.cas.authentication.principal.HttpBasedServiceCredentialsToPrincipalResolver" />   
            <bean class="com.goldarmor.live800.cas.Live800CasCredentialsToPrincipalResolver"/>   
        </list>   
    </property> 
    <property name="credentialsToPrincipalResolvers"> 
        <list> 
            <!-- 
            UsernamePasswordCredentialsToPrincipalResolver supports the UsernamePasswordCredentials that we use for /login 
            by default and produces SimplePrincipal instances conveying the username from the credentials. 
            If you've changed your LoginFormAction to use credentials other than UsernamePasswordCredentials then you will also 
            need to change this bean declaration (or add additional declarations) to declare a CredentialsToPrincipalResolver that supports the 
            Credentials you are using. 
            --> 
            <bean class="org.jasig.cas.authentication.principal.UsernamePasswordCredentialsToPrincipalResolver" /> 
            <!-- 
            HttpBasedServiceCredentialsToPrincipalResolver supports HttpBasedCredentials.  It supports the CAS 2.0 approach of 
            authenticating services by SSL callback, extracting the callback URL from the Credentials and representing it as a 
            SimpleService identified by that callback URL. 
            If you are representing services by something more or other than an HTTPS URL whereat they are able to 
            receive a proxy callback, you will need to change this bean declaration (or add additional declarations). 
            --> 
            <bean class="org.jasig.cas.authentication.principal.HttpBasedServiceCredentialsToPrincipalResolver" /> 
            <bean class="com.goldarmor.live800.cas.Live800CasCredentialsToPrincipalResolver"/> 
        </list> 
    </property> 

如上：我们也可以像定义AuthenticationHandler一样，可以定义多个credentialsToPrincipalResolvers来处理Credentials，返回你所需要的Principal对象，下面来看看credentialsToPrincipalResolvers的方法：

    /**  
    * Turn Credentials into a Principal object by analyzing the information  
    * provided in the Credentials and constructing a Principal object based on  
    * that information or information derived from the Credentials object.  
    *   
    * @param credentials from which to resolve Principal  
    * @return resolved Principal, or null if the principal could not be resolved.  
    */  
    Principal resolvePrincipal(Credentials credentials);   
      
    /**  
    * Determine if a credentials type is supported by this resolver. This is  
    * checked before calling resolve principal.  
    *   
    * @param credentials The credentials to check if we support.  
    * @return true if we support these credentials, false otherwise.  
    */  
  
  
    boolean supports(Credentials credentials); 
    /** 
     * Turn Credentials into a Principal object by analyzing the information 
     * provided in the Credentials and constructing a Principal object based on 
     * that information or information derived from the Credentials object. 
     * 
     * @param credentials from which to resolve Principal 
     * @return resolved Principal, or null if the principal could not be resolved. 
     */ 
    Principal resolvePrincipal(Credentials credentials);
    /** 
     * Determine if a credentials type is supported by this resolver. This is 
     * checked before calling resolve principal. 
     * 
     * @param credentials The credentials to check if we support. 
     * @return true if we support these credentials, false otherwise. 
     */

    boolean supports(Credentials credentials);  

在CAS验证的时候，通过访问/serviceValidate可知：验证成功之后返回的casServiceValidationSuccess.jsp中的数据来源于Assertion，下面来看看它的代码：
  
    List<Authentication> getChainedAuthentications();   
  
    /**  
    * True if the validated ticket was granted in the same transaction as that  
    * in which its grantor GrantingTicket was originally issued.  
    *   
    * @return true if validated ticket was granted simultaneous with its  
    * grantor's issuance  
    */  
  
    boolean isFromNewLogin();   
  
  
    /**  
    * Method to obtain the service for which we are asserting this ticket is  
    * valid for.  
    *   
    * @return the service for which we are asserting this ticket is valid for.  
    */  
    
    Service getService(); 
    List<Authentication> getChainedAuthentications();
    /** 
     * True if the validated ticket was granted in the same transaction as that 
     * in which its grantor GrantingTicket was originally issued. 
     * 
     * @return true if validated ticket was granted simultaneous with its 
     * grantor's issuance 
     */
    boolean isFromNewLogin();

    /** 
     * Method to obtain the service for which we are asserting this ticket is 
     * valid for. 
     * 
     * @return the service for which we are asserting this ticket is valid for. 
     */
    Service getService(); 

通过getChainedAuthentications()方法，我们可以得到Authentication对象列表，再看看Authentication的代码：
 
    /**  
    * Method to obtain the Principal.  
    *   
    * @return a Principal implementation  
    */  
      
    Principal getPrincipal();   
      
    /**  
    * Method to retrieve the timestamp of when this Authentication object was  
    * created.  
    *   
    * @return the date/time the authentication occurred.  
    */  
    
    Date getAuthenticatedDate();   
    
    /**  
    * Attributes of the authentication (not the Principal).  
    * @return the map of attributes.  
    */  
    Map<String, Object> getAttributes(); 
    /** 
     * Method to obtain the Principal. 
     * 
     * @return a Principal implementation 
     */
    Principal getPrincipal();
    /** 
     * Method to retrieve the timestamp of when this Authentication object was 
     * created. 
     * 
     * @return the date/time the authentication occurred. 
     */
    Date getAuthenticatedDate();
    /** 
     * Attributes of the authentication (not the Principal). 
     * @return the map of attributes. 
     */ 
    Map<String, Object> getAttributes(); 

而这其中的Principal就来源于上面提到的由credentialsToPrincipalResolvers处理得到的Principal对象，最后看一下Principal的代码，我们只要再做一个实现他的代码，整个CAS Server就可以信手拈来了，呵呵
  
    /**  
    * Returns the unique id for the Principal  
    * @return the unique id for the Principal.  
    */  
      
    String getId();   
      
      
    /**  
     *   
     * @return  
     */  
      
    Map<String, Object> getAttributes(); 
    /** 
     * Returns the unique id for the Principal 
     * @return the unique id for the Principal. 
     */
    String getId();

     /** 
     * 
     * @return 
     */
    Map<String, Object> getAttributes();

我们还可以自定义自己的casServiceValidationSuccess.jsp和casLoginView.jsp页面等，具体的操作办法也是最简单的办法就是备份以前的页面之后修改成自己需要的页面。 