---
layout: post
title:  "【Shiro】重要概念总结"
categories: 学习教程
tags: 基础 shiro 
author: senyer
---

* content
{:toc}



## 前言
shiro的重要知识点概述


## 第一部分：简介

Shiro的作用：
		认证、授权、加密、会话管理、缓存、Web集成等
		
基本功能点：
		1、Authentication    身份认证/登陆 
		2、Authorization     授权，即权限认证
		3、Session Manager   会话管理，即用户登陆后，就是一次会话，没有退出之前，信息都保存在会话中。
		3、Cryptography 		加密， 保护数据的安全性，例如将密码加密存储到数据库中，而不是明文存储
		4、Web Support		Web支持， 可以非常容易的集成到Web环境
		5、Caching 			缓存，用户登陆后，不必每次去查询数据库，提高效率
		6、Concurrency 		Shiro支持多线程应用的并发验证。
		7、Testing 			提供测试支持
		8、Run As			允许一个用户假装为另一个用户（如果被授权）的身份 访问
		9、Remember Me		记住我，即以此登陆后，下次再来就不用登陆了
		
注意：Shiro不会去维护用户、维护权限，需要我们自己设计，提供；然后通过相应接口注入给Shiro

流程：
	Application Code =====> Subject ======> Shiro SecurityManager =====> Realm

API的含义
		Subject：主题，代表了当前用户，可以是具体的人，机器人，网络爬虫等。所有Subject都绑定到SecurityManager，Subject是门面，SecurityManager是实际的执行者。
		
		SecurityManager：安全管理器；Shiro的核心，类似于SpringMVC的DispatherServlet，负责与其他组件进行交互。
		
		Realm：域，Shrio从Realm获得安全数据（如用户、角色、权限），即SecurityManager的授权，验证要从Realm里获取相应用户比较，确认身份合法性，授权，验证用户的操作。可以将Realm类比成DataSource，即安全数据源
		
基于spring引用的jar：shiro-core、shiro-web、shiro-spring
		
## 第二部分：身份认证


principals：		身份，主题的标识属性，用户名，邮箱等唯一性数据。一个主体可以有多个

credentials：	证明/凭证  如密码/数字证书。

异常类：
		身份验证：AuthenticationException
				子类：
				1、DisabledAccountException（禁用的账号）
				2、LockedAccountException（锁定的账号）
				3、UnkonwAccountException（错误的账号）
				4、ExcessiveAttemptsException(登录失败次数过多)
				5、IncorrectCredentialsException（错误的凭证）
				6、ExpiredCredentialsException（过期的凭证）
				

身份认证基本流程：
				流程如下：

				1、首先调用Subject.login(token)进行登录，其会自动委托给Security Manager，调用之前必须通过SecurityUtils. setSecurityManager()设置；
				2、SecurityManager负责真正的身份验证逻辑；它会委托给Authenticator进行身份验证；
				3、Authenticator才是真正的身份验证者，Shiro API中核心的身份认证入口点，此处可以自定义插入自己的实现；
				4、Authenticator可能会委托给相应的AuthenticationStrategy进行多Realm身份验证，默认ModularRealmAuthenticator会调用AuthenticationStrategy进行多Realm身份验证；
				5、Authenticator会把相应的token传入Realm，从Realm获取身份验证信息，如果没有返回/抛出异常表示身份验证失败了。此处可以配置多个Realm，将按照相应的顺序及策略进行访问。
						
## 第三部分： 授权

				Shiro提供了hasRole/hasAllRoles/hasRoles用于判断用户是否拥有某个角色/某些权限；
		
				Shiro提供了isPermitted和isPermittedAll用于判断用户是否拥有某个权限或所有权限，
		
		
## 第四部分：加密
				编码/解码加密 ：  Shiro提供了base64和16进制字符串编码/解码的API支持，
						String base64Encoded = Base64.encodeToString(str.getBytes());  
						String str2 = Base64.decodeToString(base64Encoded);  
						
						String base64Encoded = Hex.encodeToString(str.getBytes());  
						String str2 = new String(Hex.decode(base64Encoded.getBytes()));  
						
						
				散列算法加密：MD5    、   SHA等
				
					String md5 = new Md5Hash(str, salt).toString();//还可以转换为 toBase64()/toHex()   
					String sha1 = new Sha256Hash(str, salt).toString();   
					String simpleHash = new SimpleHash("SHA-1", str, salt).toString();   
					
					
				 加密/解密 ：Shiro还提供对称式加密/解密算法的支持，如AES、Blowfish等；
					
				
				支持密码输入次数超过n次，报异常！	
				
## 第五部分：Realm及相关对象
				doGetAuthenticationInfo获取身份验证相关信息：
				doGetAuthorizationInfo获取授权信息：
				
				
				AuthenticationToken
							AuthenticationToken用于收集用户提交的身份（如用户名）及凭据（如密码）：
							扩展接口RememberMeAuthenticationToken：提供了“boolean isRememberMe()”现“记住我”的功能；
							扩展接口是HostAuthenticationToken：提供了“String getHost()”方法用于获取用户“主机”的功能。
							UsernamePasswordToken，用于实现用户名/密码Token组，
					
				AuthorizationInfo 用于聚合授权信息
							 Collection<String> getRoles(); //获取角色字符串信息  
    						 Collection<String> getStringPermissions(); //获取权限字符串信息  
    						 Collection<Permission> getObjectPermissions(); //获取Permission对象信息	
    						 
				Subject Shiro的核心对象-----基本所有身份验证、授权都是通过Subject完成。	
							 1、身份信息获取
							 				Object getPrincipal(); //Primary Principal  
											PrincipalCollection getPrincipals(); // PrincipalCollection   
							 2、身份验证
							 				void login(AuthenticationToken token) throws AuthenticationException;  
											boolean isAuthenticated();  
											boolean isRemembered();  
											通过login登录，如果登录失败将抛出相应的AuthenticationException，
											如果登录成功调用isAuthenticated就会返回true，即已经通过身份验证；
											如果isRemembered返回true，表示是通过记住我功能登录的而不是调用login方法登录的。
											isAuthenticated/isRemembered是互斥的，即如果其中一个返回true，另一个返回false。				
							 3、角色授权验证
							 				boolean hasRole(String roleIdentifier);  
							 				boolean[] hasRoles(List<String> roleIdentifiers);  
							 				boolean hasAllRoles(Collection<String> roleIdentifiers);  
							 				void checkRole(String roleIdentifier) throws AuthorizationException;  
							 				void checkRoles(Collection<String> roleIdentifiers) throws AuthorizationException;  
							 4、权限授权验证
							 				boolean isPermitted(String permission);  
							 				boolean[] isPermitted(List<Permission> permissions);  
							 				boolean isPermittedAll(Collection<Permission> permissions);  
							 				void checkPermission(Permission permission) throws AuthorizationException;  
							 				void checkPermissions(Collection<Permission> permissions) throws AuthorizationException;  
							 5、会话
							 				Session getSession(); //相当于getSession(true)  
							 				Session getSession(boolean create);    
							 6、退出
							 				void logout();  
							 7、RunAs
							 				void runAs(PrincipalCollection principals) throws NullPointerException, IllegalStateException;  
							 				boolean isRunAs();  
							 				PrincipalCollection getPreviousPrincipals();  
							 				PrincipalCollection releaseRunAs();   
							 				RunAs即实现“允许A假设为B身份进行访问”；
							 				通过调用subject.runAs(b)进行访问；接着调用subject.getPrincipals将获取到B的身份；
							 				此时调用isRunAs将返回true；而a的身份需要通过subject. getPreviousPrincipals获取；
							 				如果不需要RunAs了调用subject. releaseRunAs即可。
							 8、多线程
							 				void execute(Runnable runnable);  
							 				<V> V execute(Callable<V> callable) throws ExecutionException; 
						
							 
							 
				 

				对于Subject我们一般这么使用：

				1、身份验证（login）

				2、授权（hasRole*/isPermitted*或checkRole*/checkPermission*）

				3、将相应的数据存储到会话（Session）

				4、切换身份（RunAs）/多线程身份传播

				5、退出
							
## 第六部分：配置文件之url的配置说明					
				url模式使用Ant风格模式
				
							Ant路径通配符支持?、*、**，注意通配符匹配不包括目录分隔符“/”：
							
							?：匹配一个字符，如”/admin?”将匹配/admin1，但不匹配/admin或/admin2；
							
							*：匹配零个或多个字符串，如/admin*将匹配/admin、/admin123，但不匹配/admin/1；
							
							**：匹配路径中的零个或多个路径，如/admin/**将匹配/admin/a或/admin/a/b。

 				 url模式匹配顺序

							url模式匹配顺序是按照在配置中的声明顺序匹配，即从头开始使用第一个匹配的url模式对应的拦截器链
							 
							 
							 
					
## 第七部分：Shiro的默认过滤器
			说明：用户可以i顶起过滤器，来按需扩展。一般默认的足够了
			
			身份验证相关的Filter
						authc		如“/**=authc”，如果没有登录会跳到相应的登录页面登录；
						authcBasic	Basic HTTP身份验证拦截器，
						logout		退出拦截器，主要属性：redirectUrl：退出成功后重定向的地址（/）;示例“/logout=logout”
						user		用户拦截器，用户已经身份验证/记住我登录的都可；示例“/**=user”
						anon		匿名拦截器，即不需要登录即可访问；一般用于静态资源过滤；示例“/static/**=anon”					
					
			授权相关的Filter
						roles		角色授权拦截器，验证用户是否拥有所有角色；主要属性： loginUrl：登录页面地址（/login.jsp）；unauthorizedUrl：未授权后重定向的地址；示例“/admin/**=roles[admin]”
						perms		权限授权拦截器，验证用户是否拥有所有权限；属性和roles一样；示例“/user/**=perms["user:create"]”
						port		端口拦截器，主要属性：port（80）：可以通过的端口；示例“/test= port[80]”，如果用户访问该页面是非80，将自动将请求端口改为80并重定向到该80端口，
						rest		rest风格拦截器，自动根据请求方法构建权限字符串（GET=read, POST=create,PUT=update,DELETE=delete,HEAD=read,TRACE=read,OPTIONS=read, MKCOL=create）构建权限字符串；
									示例“/users=rest[user]”，会自动拼出“user:read,user:create,user:update,user:delete”权限字符串进行权限匹配（所有都得匹配，isPermittedAll）；
						ssl			SSL拦截器，只有请求协议是https才能通过；否则自动跳转会https端口（443）；其他和port拦截器一样；
								
								
## 第八部分：JSP标签
				
			说明：Shiro提供了JSTL标签用于在JSP/GSP页面进行权限控制，如根据登录用户显示相应的页面按钮。
			
			导入标签库：
			<%@taglib prefix="shiro" uri="http://shiro.apache.org/tags" %> 
						
						
						guest标签 			用户没有身份验证时显示相应信息，即游客访问信息。
						
												<shiro:guest>  
												欢迎游客访问，<a href="${pageContext.request.contextPath}/login.jsp">登录</a>  
												</shiro:guest>  
			
					 	user标签 				用户已经身份验证/记住我登录后显示相应的信息。
					 	
												<shiro:user>  
												欢迎[<shiro:principal/>]登录，<a href="${pageContext.request.contextPath}/logout">退出</a>  
												</shiro:user>  					
					
						authenticated标签 	用户已经身份验证通过，即Subject.login登录成功，不是记住我登录的。
												<shiro:authenticated>  
												    用户[<shiro:principal/>]已身份验证通过  
												</shiro:authenticated>   						
																		
						notAuthenticated标签 	用户已经身份验证通过，即没有调用Subject.login进行登录，包括记住我自动登录的也属于未进行身份验证。
											<shiro:notAuthenticated>
											    未身份验证（包括记住我）
											</shiro:notAuthenticated> 
																	
						principal标签 		显示用户身份信息，默认调用Subject.getPrincipal()获取，即Primary Principal。
											<shiro: principal/>
											<shiro:principal type="java.lang.String"/>  相当于Subject.getPrincipals().oneByType(String.class)。
											<shiro:principal property="username"/>  	相当于((User)Subject.getPrincipals()).getUsername()。   
						
						hasRole标签 			如果当前Subject有角色将显示body体内容。
											<shiro:hasRole name="admin">  
											    用户[<shiro:principal/>]拥有角色admin<br/>  
											</shiro:hasRole> 	
											
						hasAnyRoles标签 		如果当前Subject有任意一个角色（或的关系）将显示body体内容。 								
											<shiro:hasAnyRoles name="admin,user">  
    										   用户[<shiro:principal/>]拥有角色admin或user<br/>  
											</shiro:hasAnyRoles>
											
						lacksRole标签 		如果当前Subject没有角色将显示body体内容。 
											<shiro:lacksRole name="abc">  
											    用户[<shiro:principal/>]没有角色abc<br/>  
											</shiro:lacksRole>  						
						
						
						hasPermission标签		如果当前Subject有权限将显示body体内容。 
											<shiro:hasPermission name="user:create">  
											    用户[<shiro:principal/>]拥有权限user:create<br/>  
											</shiro:hasPermission>						
						
						lacksPermission标签	如果当前Subject没有权限将显示body体内容。
											<shiro:lacksPermission name="org:create">  
											    用户[<shiro:principal/>]没有权限org:create<br/>  
											</shiro:lacksPermission> 					
						
						导入自定义标签库 			
											<%@taglib prefix="zhang" tagdir="/WEB-INF/tags" %>  
											
											<zhang:hasAllRoles name="admin,user">  
											    用户[<shiro:principal/>]拥有角色admin和user<br/>  
											</zhang:hasAllRoles>  
											<zhang:hasAllPermissions name="user:create,user:update">  
											    用户[<shiro:principal/>]拥有权限user:create和user:update<br/>  
											</zhang:hasAllPermissions>  
											<zhang:hasAnyPermissions name="user:create,abc:update">  
											    用户[<shiro:principal/>]拥有权限user:create或abc:update<br/>  
											</zhang:hasAnyPermissions>   						
						
						
## 第八部分：会话
						Subject subject = SecurityUtils.getSubject();  
						Session session = subject.getSession();  		获取会话
						session.getId();								获取当前会话的唯一标识。						
						session.getHost(); 								获取当前Subject的主机地址，该地址是通过HostAuthenticationToken.getHost()提供的。
						session.getTimeout();  							获取当前Session的过期时间；如果不设置默认是会话管理器的全局过期时间。
						session.setTimeout(毫秒);							设置当前Session的过期时间；如果不设置默认是会话管理器的全局过期时间。
						
						session.setAttribute("key", "123");  			设置会话属性；在整个会话范围内都可以对这些属性进行操作。 
						session.getAttribute("key") 					获取会话属性；在整个会话范围内都可以对这些属性进行操作。 
						session.removeAttribute("key"); 				删除会话属性；在整个会话范围内都可以对这些属性进行操作。 
						
						
						会话管理器：
								Shiro提供了三个默认实现：

													DefaultSessionManager：DefaultSecurityManager使用的默认实现，用于JavaSE环境；
													
													ServletContainerSessionManager：DefaultWebSecurityManager使用的默认实现，用于Web环境，其直接使用Servlet容器的会话；
													
													DefaultWebSessionManager：用于Web环境的实现，可以替代ServletContainerSessionManager，自己维护着会话，直接废弃了Servlet容器的会话管理。
																		
						会话存储/持久化	
					           Shiro提供SessionDAO用于会话的CRUD，即DAO（Data Access Object）模式实现：	
					   	缓存	Cache	
					
## 第九部分：与spring集成
									spring-shiro.xml相关配置：
																		<!-- 缓存管理器 使用Ehcache实现 -->  
																		<bean id="cacheManager" class="org.apache.shiro.cache.ehcache.EhCacheManager">  
																		    <property name="cacheManagerConfigFile" value="classpath:ehcache.xml"/>  
																		</bean>  
																		  
																		<!-- 凭证匹配器 -->  
																		<bean id="credentialsMatcher" class="  
																		com.github.zhangkaitao.shiro.chapter12.credentials.RetryLimitHashedCredentialsMatcher">  
																		    <constructor-arg ref="cacheManager"/>  
																		    <property name="hashAlgorithmName" value="md5"/>  
																		    <property name="hashIterations" value="2"/>  
																		    <property name="storedCredentialsHexEncoded" value="true"/>  
																		</bean>  
																		  
																		<!-- Realm实现 -->  
																		<bean id="userRealm" class="com.github.zhangkaitao.shiro.chapter12.realm.UserRealm">  
																		    <property name="userService" ref="userService"/>  
																		    <property name="credentialsMatcher" ref="credentialsMatcher"/>  
																		    <property name="cachingEnabled" value="true"/>  
																		    <property name="authenticationCachingEnabled" value="true"/>  
																		    <property name="authenticationCacheName" value="authenticationCache"/>  
																		    <property name="authorizationCachingEnabled" value="true"/>  
																		    <property name="authorizationCacheName" value="authorizationCache"/>  
																		</bean>  
																		<!-- 会话ID生成器 -->  
																		<bean id="sessionIdGenerator"   
																		class="org.apache.shiro.session.mgt.eis.JavaUuidSessionIdGenerator"/>  
																		<!-- 会话DAO -->  
																		<bean id="sessionDAO"   
																		class="org.apache.shiro.session.mgt.eis.EnterpriseCacheSessionDAO">  
																		    <property name="activeSessionsCacheName" value="shiro-activeSessionCache"/>  
																		    <property name="sessionIdGenerator" ref="sessionIdGenerator"/>  
																		</bean>  
																		<!-- 会话验证调度器 -->  
																		<bean id="sessionValidationScheduler"   
																		class="org.apache.shiro.session.mgt.quartz.QuartzSessionValidationScheduler">  
																		    <property name="sessionValidationInterval" value="1800000"/>  
																		    <property name="sessionManager" ref="sessionManager"/>  
																		</bean>  
																		<!-- 会话管理器 -->  
																		<bean id="sessionManager" class="org.apache.shiro.session.mgt.DefaultSessionManager">  
																		    <property name="globalSessionTimeout" value="1800000"/>  
																		    <property name="deleteInvalidSessions" value="true"/>  
																		    <property name="sessionValidationSchedulerEnabled" value="true"/>  
																		   <property name="sessionValidationScheduler" ref="sessionValidationScheduler"/>  
																		    <property name="sessionDAO" ref="sessionDAO"/>  
																		</bean>  
																		<!-- 安全管理器 -->  
																		<bean id="securityManager" class="org.apache.shiro.mgt.DefaultSecurityManager">  
																		    <property name="realms">  
																		        <list><ref bean="userRealm"/></list>  
																		    </property>  
																		    <property name="sessionManager" ref="sessionManager"/>  
																		    <property name="cacheManager" ref="cacheManager"/>  
																		</bean>  
																		<!-- 相当于调用SecurityUtils.setSecurityManager(securityManager) -->  
																		<bean class="org.springframework.beans.factory.config.MethodInvokingFactoryBean">  
																		<property name="staticMethod"   
																		value="org.apache.shiro.SecurityUtils.setSecurityManager"/>  
																		    <property name="arguments" ref="securityManager"/>  
																		</bean>  
																		<!-- Shiro生命周期处理器-->  
																		<bean id="lifecycleBeanPostProcessor"   
																		class="org.apache.shiro.spring.LifecycleBeanPostProcessor"/>  
																		
																		
																		
																		
																		<!-- 基于Form表单的身份验证过滤器 -->  
																		<bean id="formAuthenticationFilter"   
																		class="org.apache.shiro.web.filter.authc.FormAuthenticationFilter">  
																		    <property name="usernameParam" value="username"/>  
																		    <property name="passwordParam" value="password"/>  
																		    <property name="loginUrl" value="/login.jsp"/>  
																		</bean>  
																		<!-- Shiro的Web过滤器 -->  
																		<bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">  
																		    <property name="securityManager" ref="securityManager"/>  
																		    <property name="loginUrl" value="/login.jsp"/>  
																		    <property name="unauthorizedUrl" value="/unauthorized.jsp"/>  
																		    <property name="filters">  
																		        <util:map>  
																		            <entry key="authc" value-ref="formAuthenticationFilter"/>  
																		        </util:map>  
																		    </property>  
																		    <property name="filterChainDefinitions">  
																		        <value>  
																		            /index.jsp = anon  
																		            /unauthorized.jsp = anon  
																		            /login.jsp = authc  
																		            /logout = logout  
																		            /** = user  
																		        </value>  
																		    </property>  
																		</bean>  
																		1、formAuthenticationFilter为基于Form表单的身份验证过滤器；此处可以再添加自己的Filter bean定义；
											
																		2、shiroFilter：此处使用ShiroFilterFactoryBean来创建ShiroFilter过滤器；filters属性用于定义自己的过滤器，
																		       即ini配置中的[filters]部分；filterChainDefinitions用于声明url和filter的关系，即ini配置中的[urls]部分。
					
					接着需要在web.xml中进行如下配置： 
								
																		<filter>  
																		    <filter-name>shiroFilter</filter-name>  
																		    <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>  
																		    <init-param>  
																		        <param-name>targetFilterLifecycle</param-name>  
																		        <param-value>true</param-value>  
																		    </init-param>  
																		</filter>  
																		<filter-mapping>  
																		    <filter-name>shiroFilter</filter-name>  
																		    <url-pattern>/*</url-pattern>  
																		</filter-mapping> 
																		  
																		说明：DelegatingFilterProxy会自动到Spring容器中查找名字为shiroFilter的bean并把filter请求交给它处理。
					
## 第十部份：Shiro权限注解
								Shiro提供了相应的注解用于权限控制，如果使用这些注解就需要使用AOP的功能来进行判断，如Spring AOP；Shiro提供了Spring AOP集成用于权限注解的解析和验证。
					
								1、在spring-mvc.xml配置文件添加Shiro Spring AOP权限注解的支持： 
										<aop:config proxy-target-class="true"></aop:config>  
										<bean class="  
										org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor">  
										    <property name="securityManager" ref="securityManager"/>  
										</bean>   		
										说明：如上配置用于开启Shiro Spring AOP权限注解的支持；<aop:config proxy-target-class="true">表示代理类。		
										说明：接着就可以在相应的控制器（AnnotationController）中使用如下方式进行注解： 	
											@RequiresRoles("admin")  
											@RequestMapping("/hello2")  
											public String hello2() {  
											    return "success";  
											}   
										说明：访问hello2方法的前提是当前用户有admin角色。
										说明：当验证失败，其会抛出UnauthorizedException异常，此时可以使用Spring的ExceptionHandler（DefaultExceptionHandler）来进行拦截处理：
											@ExceptionHandler({UnauthorizedException.class})  
											@ResponseStatus(HttpStatus.UNAUTHORIZED)  
											public ModelAndView processUnauthenticatedException(NativeWebRequest request, UnauthorizedException e) {  
											    ModelAndView mv = new ModelAndView();  
											    mv.addObject("exception", e);  
											    mv.setViewName("unauthorized");  
											    return mv;  
											}   		
											
								
								权限注解  			
								
										@RequiresAuthentication 			表示当前Subject已经通过login进行了身份验证；即Subject. isAuthenticated()返回true。 
							
										@RequiresUser  		表示当前Subject已经身份验证或者通过记住我登录的。
										
										@RequiresGuest  	表示当前Subject没有身份验证或通过记住我登录过，即是游客身份。
										
										@RequiresRoles(value={“admin”, “user”}, logical= Logical.AND)  表示当前Subject需要角色admin和user。
										
										@RequiresPermissions (value={“user:a”, “user:b”}, logical= Logical.OR)  表示当前Subject需要权限user:a或user:b。
								
								
								
## 第十一部分：RememberMe配置
							spring-shiro-web.xml配置：								
										<!-- 会话Cookie模板 -->  
										<bean id="sessionIdCookie" class="org.apache.shiro.web.servlet.SimpleCookie">  
										    <constructor-arg value="sid"/>  
										    <property name="httpOnly" value="true"/>  
										    <property name="maxAge" value="-1"/>  
										</bean>  
										<bean id="rememberMeCookie" class="org.apache.shiro.web.servlet.SimpleCookie">  
										    <constructor-arg value="rememberMe"/>  
										    <property name="httpOnly" value="true"/>  
										    <property name="maxAge" value="2592000"/><!-- 30天 -->  
										</bean>   					
										说明：sessionIdCookie：maxAge=-1表示浏览器关闭时失效此Cookie；
										说明：rememberMeCookie：即记住我的Cookie，保存时长30天；
										说明：略......详情访问：http://jinnianshilongnian.iteye.com/blog/2031823
										
										
## 第十二部份：SSL的支持		
					略......	详情访问：		http://jinnianshilongnian.iteye.com/blog/2036420
					
					
						
## 第十三部份：单点登录的支持，		
					说明：单点登录主要用于多系统集成，即在多个系统中，用户只需要到一个中央服务器登录一次即可访问这些系统中的任何一个，无须多次登录。
					Jasig CAS单点登录系统分为服务器端和客户端，服务器端提供单点登录，多个客户端（子系统）将跳转到该服务器进行登录验证，大体流程如下：
					
							1、访问客户端需要登录的页面http://localhost:9080/ client/，此时会跳到单点登录服务器https://localhost:8443/ server/login?service=https://localhost:9443/ client/cas；
							
							2、如果此时单点登录服务器也没有登录的话，会显示登录表单页面，输入用户名/密码进行登录；
							
							3、登录成功后服务器端会回调客户端传入的地址：https://localhost:9443/client/cas?ticket=ST-1-eh2cIo92F9syvoMs5DOg-cas01.example.org，且带着一个ticket；
							
							4、客户端会把ticket提交给服务器来验证ticket是否有效；如果有效服务器端将返回用户身份；
							
							5、客户端可以再根据这个用户身份获取如当前系统用户/角色/权限信息。
					略......	详情访问：		http://jinnianshilongnian.iteye.com/blog/2036730
					
											
										
## 第十三部分：综合实例		************>>>>>>  重要     <<<<<<<<<<<*********
	
					详情访问：http://jinnianshilongnian.iteye.com/blog/2037222											
										
					简单数据字典	用户、角色、资源 及其映射		
					
					说明：此综合实例由较高的参考意义，提供了比较合理的数据库设计，spring继承实现等。			
						
										
## 第十四部份： 并发登录人数控制						
					详情访问：http://jinnianshilongnian.iteye.com/blog/2039760					
								在某些项目中可能会遇到如每个账户同时只能有一个人登录或几个人同时登录，如果同时有多人登录：要么不让后者登录；要么踢出前者登录（强制退出）。
								比如spring security就直接提供了相应的功能；Shiro的话没有提供默认实现，不过可以很容易的在Shiro中加入这个功能。		
								
								文件配置：
								<bean id="kickoutSessionControlFilter"   
								class="com.github.zhangkaitao.shiro.chapter18.web.shiro.filter.KickoutSessionControlFilter">  
								    <property name="cacheManager" ref="cacheManager"/>  
								    <property name="sessionManager" ref="sessionManager"/>  
								  
								    <property name="kickoutAfter" value="false"/>  
								    <property name="maxSession" value="2"/>  
								    <property name="kickoutUrl" value="/login?kickout=1"/>  
								</bean>  								
								说明：		
								cacheManager：使用cacheManager获取相应的cache来缓存用户登录的会话；用于保存用户—会话之间的关系的；
								
								sessionManager：用于根据会话ID，获取会话进行踢出操作的；
								
								kickoutAfter：是否踢出后来登录的，默认是false；即后者登录的用户踢出前者登录的用户；
								
								maxSession：同一个用户最大的会话数，默认1；比如2的意思是同一个用户允许最多同时两个人登录；
								
								kickoutUrl：被踢出后重定向到的地址；							
										
										
## 第十五部分：动态URL权限控制	
详情访问：http://jinnianshilongnian.iteye.com/blog/2040929					
略：	。。。。。。												
					
					
## 第十六部份：在线会话管理	
详情访问：http://jinnianshilongnian.iteye.com/blog/2047643
说明：有时候需要显示当前在线人数、当前在线用户，有
时候可能需要强制某个用户下线等；此时就需要获取相应的在线用户并进行一些操作。
``` java
@RequiresPermissions("session:*")  
@Controller  
@RequestMapping("/sessions")  
public class SessionController {  
		@Autowired  
		private SessionDAO sessionDAO;  
		@RequestMapping()  
		public String list(Model model) {  
				Collection<Session> sessions =  sessionDAO.getActiveSessions();  
				model.addAttribute("sessions", sessions);  
				model.addAttribute("sesessionCount", sessions.size());  
				return "sessions/list";  
		}  
		@RequestMapping("/{sessionId}/forceLogout")  
		public String forceLogout(@PathVariable("sessionId") String sessionId,   
				RedirectAttributes redirectAttributes) {  
				try {  
					Session session = sessionDAO.readSession(sessionId);  
					if(session != null) {  
						session.setAttribute(  
						Constants.SESSION_FORCE_LOGOUT_KEY, Boolean.TRUE);  
					}  
					} catch (Exception e) {/*ignore*/}  
						redirectAttributes.addFlashAttribute("msg", "强制退出成功！");  
						return "redirect:/sessions";  
					}  
					}   
 ```
					
## 第十七部份：其他	
					详情访问：http://jinnianshilongnian.iteye.com/blog/2049092
					第十七章  OAuth2集成
					
					第十八章 并发登录人数控制
					
					第二十章 无状态Web应用集成
					
					第二十一章 授予身份及切换身份
					
					第二十二章 集成验证码
					
					第二十三章 多项目集中权限管理及分布式会话
										
										
										
										
										
										
										
										
										
										
					
