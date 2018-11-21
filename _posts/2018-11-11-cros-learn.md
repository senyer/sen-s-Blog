---
layout: post
title:  "【跨域】基本概念与解决办法"
categories: 学习教程
tags: 跨域 
author: senyer
---

* content
{:toc}



## 前言
跨域的基本概念与解决办法


## 发生跨域的必要条件

1. **浏览器限制**
2. **跨域（域名不同、端口不通、ip不同等等）**
3. **XHR(XMLHttpRequest)请求**

## 解决思路
1、浏览器

*不现实，限制也比较大，不推荐*

比如cmd执行该命令来启动谷歌浏览器（cd到exe所在位置）：

chrome  --disable-web-security --user-data-dir=d:\temp3

2、XHR

*通过JSONP来实现，不好用，麻烦，也不推荐使用*

3、跨域

两种思路：

- 被调用方

1. 编写过滤器
2. 基于spring的xml配置
3. spring boot的注册bean
4. 或者spring 4.2以上直接在controller里面，直接加上@CrossOrigin

- 调用方
- 隐藏式跨域：略

### 过滤器解决跨域
A.编写跨域的Filter：

``` java
package com.senyer.crosServer;


import org.springframework.util.StringUtils;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * Created by TSY on:2018/11/21
 */
public class CrosFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletResponse response=(HttpServletResponse)servletResponse;
        HttpServletRequest request=(HttpServletRequest)servletRequest;
        String orgin=request.getHeader("origin");
        if (!StringUtils.isEmpty(orgin)){
            //带cookie的时候，origin必须是全匹配，不饿能使用*
            response.addHeader("Access-Control-Allow-Origin",orgin);
        }

        response.addHeader("Access-Control-Allow-Methods","*");
        /*response.addHeader("Access-Control-Allow-Headers","Content-Type");
        response.addHeader("Access-Control-Max-Age","3600");*/
        //enable cookie：可以解决sessionid不一致的问题
        response.addHeader("Access-Control-Allow-Credentials","true");
        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void destroy() {

    }
}
```
B.注册bean（或者xml配置）

``` java
package com.senyer.crosServer;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
@SpringBootApplication
@Configuration
public class CrosServerApplication {
	public static void main(String[] args) {
		SpringApplication.run(CrosServerApplication.class, args);
	}
	@Bean
	public FilterRegistrationBean registrationBean(){
		FilterRegistrationBean bean=new FilterRegistrationBean();
		bean.addUrlPatterns("/*");
		bean.setFilter(new CrosFilter());
		return bean;
	}
}
```
C.需要前端的ajax请求，配置允许带上cookie： xhrField{creDential:true}


### 局部配置注解

``` java
@CrossOrigin(origins = "*", maxAge = 3600)
@RestController
@RequestMapping("/controller")
public class Controlle{
}
```
### 全局配置注解
默认情况下所有的域名和GET、HEAD和POST方法都是允许的。
``` java
@Configuration
public class WebConfig extends WebMvcConfigurerAdapter {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**");
    }
}
```
可以单独更改任何属性，以及配置适用于特定的路径模式的CORS：
``` java
@Configuration
public class WebConfig extends WebMvcConfigurerAdapter {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
            .allowedOrigins("http://domain2.com")
            .allowedMethods("PUT", "DELETE")
            .allowedHeaders("header1", "header2", "header3")
            .exposedHeaders("header1", "header2")
            .allowCredentials(false).maxAge(3600);
    }
}
```
不限制任何请求：
``` java
@Configuration
public class CorsConfig extends WebMvcConfigurerAdapter {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOrigins("*")
                .allowedMethods("GET", "HEAD", "POST", "PUT", "PATCH", "DELETE", "OPTIONS", "TRACE");
    }
}
```
基于XML的配置：
``` xml
<mvc:cors>
    <mvc:mapping path="/**" />
</mvc:cors>
```
``` xml
<mvc:cors>
    <mvc:mapping path="/api/**"
        allowed-origins="http://domain1.com, http://domain2.com"
        allowed-methods="GET, PUT"
        allowed-headers="header1, header2, header3"
        exposed-headers="header1, header2" allow-credentials="false"
        max-age="123" />
    <mvc:mapping path="/resources/**"
        allowed-origins="http://domain1.com" />
</mvc:cors>
```
### Nginx配置的其中一种方式

![](https://i.imgur.com/TbgNe98.png)