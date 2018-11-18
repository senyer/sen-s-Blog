---
layout: post
title:  "【spring boot】spring boot项目的快速搭建"
categories: spring boot
tags: springboot 
author: senyer
---

* content
{:toc}



## 前言
我的第一个spring boot项目。快速搭建一个spring boot项目。不引入任何的框架。同时附带简单的注解说明。



##快速创建
创建一个spring boot项目，有很多方式。其中一种：
初始化spring boot网站：[https://start.spring.io/](https://start.spring.io/)
用户可以自行选择需要导入的框架，比如mybatis、shiro等等。
![](https://i.imgur.com/aBm4Vwl.png)

生成压缩包以后，在开发工具里面打开。resources里面有配置文件properties，可以指定请求的端口，如：server.port=8080。
main函数，用于启动springboot。
![](https://i.imgur.com/b8cPruF.png)
