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



## 快速创建
创建一个spring boot项目，有很多方式。其中一种：
初始化spring boot网站：[https://start.spring.io/](https://start.spring.io/)
用户可以自行选择需要导入的框架，比如mybatis、shiro等等。
![](https://i.imgur.com/aBm4Vwl.png)

生成压缩包以后，在开发工具里面打开。resources里面有配置文件properties，可以指定请求的端口，如：server.port=8080。
main函数，用于启动springboot。
![](https://i.imgur.com/b8cPruF.png)

## 注解总结

----------

1. @SpringBootApplication：包含了@ComponentScan、@Configuration和@EnableAutoConfiguration注解。其中@ComponentScan让spring Boot扫描到Configuration类并把它加入到程序上下文。
1. 
1. @Configuration 等同于spring的XML配置文件；使用Java代码可以检查类型安全。
1. 
1. @EnableAutoConfiguration 自动配置。
1. 
1. @ComponentScan 组件扫描，可自动发现和装配一些Bean。
1. 
1. @Component可配合CommandLineRunner使用，在程序启动后执行一些基础任务。
1. 
1. @RestController注解是@Controller和@ResponseBody的合集,表示这是个控制器bean,并且是将函数的返回值直 接填入HTTP响应体中,是REST风格的控制器。
1. 
1. @Autowired自动导入。
1. 
1. @PathVariable获取参数。
1. 
1. @JsonBackReference解决嵌套外链问题。
1. 
1. @RepositoryRestResourcepublic配合spring-boot-starter-data-rest使用。

----------
## spring boot注解详解
**@SpringBootApplication：**申明让spring boot自动给程序进行必要的配置，这个配置等同于：@Configuration ，@EnableAutoConfiguration 和 @ComponentScan 三个配置。

``` java
package com.example.myproject; 
import org.springframework.boot.SpringApplication; 
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication // same as @Configuration @EnableAutoConfiguration @ComponentScan 
public class Application { 
	public static void main(String[] args) { 
		SpringApplication.run(Application.class, args); 
	} 
}
```

**@EnableAutoConfiguration：**Spring Boot自动配置（auto-configuration）：尝试根据你添加的jar依赖自动配置你的Spring应用。例如，如果你的classpath下存在HSQLDB，并且你没有手动配置任何数据库连接beans，那么我们将自动配置一个内存型（in-memory）数据库”。你可以将@EnableAutoConfiguration或者@SpringBootApplication注解添加到一个@Configuration类上来选择自动配置。如果发现应用了你不想要的特定自动配置类，你可以使用@EnableAutoConfiguration注解的排除属性来禁用它们。

**@ComponentScan：**表示将该类自动发现扫描组件。个人理解相当于，如果扫描到有@Component、@Controller、@Service等这些注解的类，并注册为Bean，可以自动收集所有的Spring组件，包括@Configuration类。我们经常使用@ComponentScan注解搜索beans，并结合@Autowired注解导入。可以自动收集所有的Spring组件，包括@Configuration类。我们经常使用@ComponentScan注解搜索beans，并结合@Autowired注解导入。如果没有配置的话，Spring Boot会扫描启动类所在包下以及子包下的使用了@Service,@Repository等注解的类。

**@Configuration：**相当于传统的xml配置文件，如果有些第三方库需要用到xml文件，建议仍然通过@Configuration类作为项目的配置主类——可以使用@ImportResource注解加载xml配置文件。

**@Import：**用来导入其他配置类。

**@ImportResource：**用来加载xml配置文件。

@Autowired：自动导入依赖的bean

@Service：一般用于修饰service层的组件

**@Repository：**使用@Repository注解可以确保DAO或者repositories提供异常转译，这个注解修饰的DAO或者repositories类会被ComponetScan发现并配置，同时也不需要为它们提供XML配置项。

**@Bean：**用@Bean标注方法等价于XML中配置的bean。相当于XML中的,放在方法的上面，而不是类，意思是产生一个bean,并交给spring管理。

**@Value：**注入Spring boot application.properties配置的属性的值。示例代码：

    @Value(value = “#{message}”) 
    private String message;
 

**@Inject：**等价于默认的@Autowired，只是没有required属性；

@Component：泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。

**@Qualifier：**当有多个同一类型的Bean时，可以用@Qualifier(“name”)来指定。与@Autowired配合使用。@Qualifier限定描述符除了能根据名字进行注入，但能进行更细粒度的控制如何选择候选者，具体使用方式如下：

    @Autowired 
    @Qualifier(value = “demoInfoService”) 
    private DemoInfoService demoInfoService;
    
## springMVC相关注解
**@RequestMapping：**@RequestMapping(“/path”)表示该控制器处理所有“/path”的UR L请求。RequestMapping是一个用来处理请求地址映射的注解，可用于类或方法上。 
用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。该注解有六个属性： 
params:指定request中必须包含某些参数值是，才让该方法处理。 
headers:指定request中必须包含某些指定的header值，才能让该方法处理请求。 
value:指定请求的实际地址，指定的地址可以是URI Template 模式 
method:指定请求的method类型， GET、POST、PUT、DELETE等 
consumes:指定处理请求的提交内容类型（Content-Type），如application/json,text/html; 
produces:指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回

**@RequestParam：**用在方法的参数前面。 

**@PathVariable:**路径变量。如

    RequestMapping(“user/get/mac/{macAddress}”) 
    public String getByMacAddress(@PathVariable String macAddress){ 
    //do something; 
    } 
参数与大括号里的名字一样要相同。

## 全局异常处理
**@ControllerAdvice：**包含@Component。可以被扫描到。统一处理异常。

**@ExceptionHandler**（Exception.class）：用在方法上面表示遇到这个异常就执行以下方法。