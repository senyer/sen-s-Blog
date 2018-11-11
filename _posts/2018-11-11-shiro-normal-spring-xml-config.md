---
layout: post
title:  "【Shrio】一般性的spring-xml文件配置"
categories: 学习教程
tags: spring shiro xml
author: senyer
---

* content
{:toc}



## 前言
一般性的spring-xml文件配置：记住我、集成验证码、自定义过滤器、会话管理器等。
## spring-xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:util="http://www.springframework.org/schema/util"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="
       http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
       http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
       http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

	<description>Shiro Configuration</description>
	
	
	<!--################# 缓存管理器  使用Ehcache实现 ################-->
	<bean id="cacheManager" class="org.apache.shiro.cache.ehcache.EhCacheManager">
    	<property name="cacheManager" ref="ehCacheManager"/>
	</bean>
	<bean id="ehCacheManager" class ="org.springframework.cache.ehcache.EhCacheManagerFactoryBean">
	<property name="configLocation" value="classpath:spring/ehcache-shiro.xml" />
	<property name="shared" value="true"></property>
	</bean>
	
	<!--############   自定义Realm ##############-->
	<bean id="myShiroRealm" class="com.senyer.core.shiro.realm.UserRealm" >
		<property name="cacheManager" ref="cacheManager" />
	</bean>
	

	<!-- ############   用户在线信息 ############   -->
	<!-- ############   同步在线信息 ############   -->
	<!-- ############   退出过滤器	############   -->
	<!-- ############   验证码过滤器############   -->
	<bean id="onlineSessionFilter" class="com.senyer.core.shiro.web.filter.online.OnlineSessionFilter" >
		<property name="loginUrl" value="/login" />										<!-- 登录地址 -->
	</bean>	
	<bean id="syncOnlineSessionFilter" class="com.senyer.core.shiro.web.filter.sync.SyncOnlineSessionFilter" />
	<bean id="logoutFilter" class="com.senyer.core.shiro.web.filter.LogoutFilter" >
		<property name="loginUrl" value="/login" />										<!-- 登录地址 -->
	</bean>	
	<bean id="captchaValidateFilter" class="com.senyer.core.shiro.web.filter.captcha.CaptchaValidateFilter" >
		<property name="captchaEnabled" value="false" />								<!-- 验证码开关 -->
		<property name="captchaType" value="char" />									<!-- 验证码类型 math: 数组计算, char: 字符 -->
	</bean>	
	
	<!-- ############   自定义sessionDAO会话    ############   -->
	<!-- ############   自定义sessionFactory会话  ############   -->
	<bean id="sessionDAO" class="com.senyer.core.shiro.session.OnlineSessionDAO" />	
	<bean id="sessionFactory" class="com.senyer.core.shiro.session.OnlineSessionFactory" />	
	
	
	<!-- ############   会话验证调度器 (自定义重写) ############   -->  
	<bean id="sessionValidationScheduler"   class="com.senyer.core.shiro.web.session.SpringSessionValidationScheduler">  
	    <property name="sessionValidationInterval" value="600000"/>  					<!-- 相隔多久检查一次session的有效性，单位毫秒，默认就是10分钟  -->
	    <property name="sessionManager" ref="sessionValidationManager"/>  				<!-- 设置会话验证调度器进行会话验证时的会话管理器 -->
	</bean> 
	
	<!-- ############   会话管理器(自定义重写)    ############-->
	<bean id="sessionValidationManager" class="com.senyer.core.shiro.web.session.OnlineWebSessionManager">
        <property name="cacheManager" ref="cacheManager"/>  							<!-- 加入缓存管理器 -->
        <property name="globalSessionTimeout" value="1800000"/>  						<!-- 设置全局session超时时间 -->
	    <property name="deleteInvalidSessions" value="true"/>  							<!-- 删除过期的session -->
	    <property name="sessionValidationSchedulerEnabled" value="true"/>  				<!-- 是否定时检查session  -->
	   	<property name="sessionIdUrlRewritingEnabled" value="true"/>  					<!-- 去掉 JSESSIONID -->
	    <property name="sessionDAO" ref="sessionDAO"/>  								<!-- 自定义SessionDao -->
	    <property name="sessionFactory" ref="sessionFactory"/> 							<!-- 自定义sessionFactory -->
    </bean>
    
	<!-- ############   会话管理器(自定义重写,与上面有区别)    ############-->
	<bean id="sessionManager" class="com.senyer.core.shiro.web.session.OnlineWebSessionManager">
        <property name="cacheManager" ref="cacheManager"/>  							<!-- 加入缓存管理器 -->
        <property name="globalSessionTimeout" value="1800000"/>  						<!-- 设置全局session超时时间 -->
	    <property name="deleteInvalidSessions" value="true"/>  							<!-- 删除过期的session -->
	    <property name="sessionValidationSchedulerEnabled" value="true"/>  				<!-- 是否定时检查session  -->
	    <property name="sessionValidationScheduler" ref="sessionValidationScheduler"/> 	<!-- 定义要使用的无效的Session定时调度器 -->
	   	<property name="sessionIdUrlRewritingEnabled" value="true"/>  					<!-- 去掉 JSESSIONID -->
	    <property name="sessionDAO" ref="sessionDAO"/>  								<!-- 自定义SessionDao -->
	    <property name="sessionFactory" ref="sessionFactory"/> 							<!-- 自定义sessionFactory -->
    </bean>
	
	<!-- ############ 	安全管理器(核心管理器 )		############ -->
	<bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
		<property name="cacheManager" ref="cacheManager"/> 
		<property name="rememberMeManager" ref="rememberMeManager" />
		<property name="realm" ref="myShiroRealm" />
		<property name="sessionManager" ref="sessionManager" />
	</bean>
	
	
	<!-- ############  rememberMe管理器   ############-->
	<bean id="rememberMeManager" class="org.apache.shiro.web.mgt.CookieRememberMeManager">
		<property name="cipherKey" value="#{T(org.apache.shiro.codec.Base64).decode('fCq+/xW488hMTCD+cmJ3aQ==')}" />
		<property name="cookie" ref="rememberMeCookie" />
	</bean>
	<bean id="rememberMeCookie" class="org.apache.shiro.web.servlet.SimpleCookie">			<!-- rememberMeCookie  -->
		<constructor-arg value="rememberMe" />												<!-- 构造函数传参：rememberMe -->
		<property name="httpOnly" value="true" />											<!-- 设置HttpOnly属性  -->
		<property name="maxAge" value="2592000" />											<!-- 30天 -->
	</bean>
	
	
					
	<!-- ############  Shiro Filter 	############  -->
	<bean id="shiroFilter"
		class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
		<property name="securityManager" ref="securityManager" />
		<property name="loginUrl" value="/unLogin" />
		<property name="unauthorizedUrl" value="/unauthorized" />
		<property name="filters">
			<util:map>
				<entry key="onlineSession" value-ref="onlineSessionFilter" />
				<entry key="syncOnlineSession" value-ref="syncOnlineSessionFilter" />
				<entry key="captchaValidate" value-ref="captchaValidateFilter" />
				<entry key="logout" value-ref="logoutFilter" />
			</util:map>
		</property>
		
		<property name="filterChainDefinitions">
			<value>
				<!-- 静态资源过滤 -->
				/static/** = anon
				/favicon.ico** = anon
				/css/** = anon
				/docs/** = anon
				/fonts/** = anon
				/images/** = anon
				/js/** = anon
				/druid/** = anon
				/fonts/** = anon
				/swagger-ui.html = anon
				/captcha/captchaImage** = anon
				
				<!-- 进入到logoutFilter  -->
				/logout = logout
				<!-- 匿名、再进入到验证码过滤器 -->
				/login = anon,captchaValidate
				<!-- 每一个请求都要进到下面的两个过滤器里面 -->
				/** = anon,onlineSession,syncOnlineSession
				<!-- 所有请求匿名访问、再进入到 会话Filter、同步会话Filter  -->
				/** = anon,onlineSession,syncOnlineSession
				<!-- /** = user,onlineSession,syncOnlineSession -->
			</value>
		</property>
	</bean>
	
	<!-- ############  保证实现了Shiro内部lifecycle函数的bean执行 		############  -->
	<bean id="lifecycleBeanPostProcessor" class="org.apache.shiro.spring.LifecycleBeanPostProcessor" />
	
	<!-- ############  开启Shiro注解通知器 ，实现权限控制 	############  -->
	<bean class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator" depends-on="lifecycleBeanPostProcessor" />
	<bean class="org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor">
		<property name="securityManager" ref="securityManager" />
	</bean>
	

</beans>  
```