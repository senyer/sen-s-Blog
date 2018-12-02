---
layout: post
title:  "【spring boot】基本功能总结与入门"
categories: springboot
tags: springboot 
author: senyer
---

* content
{:toc}





## Spring Boot入门

### 前言

>初次使用spring boot，记录spring boot的一些基本功能。很多功能，基于约定大于配置的理念，spring boot都给我们默认配置好了。我们按照他的约定来，就可以省区很多工作。很方便。

### 内容
#### **项目结构**

``` xml
|--project
	|--src/main/java
		|--com.senyer
			|--controller
			|--entity
			|--mapper
			|--service
			|--Application.java（启动类）
	|--src/main/resources	
		|--static
			|--favicon.ico
			|--img
			|--css
			|--js
		|--templates
			|--index.html
			|--user
			|--dept
		|--application.yml(application.properties)
		|--application-druid.yml
		|--application-dev.yml
		|--application-prod.yml
	|--src/test/java
```


#### **banner.txt**
	丢到resources文件下就行，好用的生成自定义banner的网站：[http://www.bootschool.net/ascii](http://www.bootschool.net/ascii)
	
#### **favicon.ico**
	 丢到默认的static文件夹下面就可以了
	 
#### **多环境配置**
	格式：application-{profile}.properties
	
	可以编写多个yml文件，但是默认的application不能变，然后可以在默认里面加上spring.profiles.active=xxx，来选择使用哪个环境。application.yml、application-druid.yml、application-dev.yml、application-prod.yml

*yml多文档快*
		
	yml文件中支持使用三个短横线分割文档块的方式。其中default表示未指定时默认使用的配置。
	
		server:
	  	  port: 8082
		spring:
		  profiles:
		    active: dev
		---
		spring:
		  profiles: dev
		server:
		  port: 8083
		---
		spring:
		  profiles: prod
		server:
		  port: 8084
		---
		spring:
		  profiles: default
		server:
		  port: 80
		---
#### **静态资源过滤**

``` xml
spring:
    resources:
        static-locations: classpath:/static/,classpath:/templates/
```

#### **集成Thymeleaf**

``` yml
#配置访问静态资源，static默认可以访问，templates默认不可以访问，如果此处配置，则默认配置失效
spring:
    resources:
        static-locations: classpath:/static/,classpath:/templates/
```
#### **配置端口与访问路径**

``` yml
server:
    port: 8080
    servlet:
        context-path: /
```

#### **集成Druid**

>可以利用druid的数据监控，实时掌握sql使用情况。  访问地址：  ip:port/project_name/druid

``` yml
spring:
    datasource:
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://localhost:3306/mybatis_plus_learn_demo1?useUnicode=true&characterEncoding=utf8&allowMultiQueries=true&serverTimezone=GMT%2B8
        username: root
        password: root
#druid配置        
        type: com.alibaba.druid.pool.DruidDataSource
        druid:
            filters: stat,wall
            max-active: 20
            initial-size: 5
            max-wait: 60000
            min-idle: 1
            time-between-eviction-runs-millis: 60000
            min-evictable-idle-time-millis: 300000
            validation-query: select 'x'
            test-while-idle: true
            test-on-borrow: false
            test-on-return: false
            pool-prepared-statements: true
            max-open-prepared-statements: 20
            filter:
                stat:
                    log-slow-sql: true
            #druid WebStatFilter监控配置
            web-stat-filter:
                enabled: true
                url-pattern: /*
                #忽略资源
                exclusions: /druid/*,*.js,*.gif,*.jpg,*.bmp,*.png,*.css,*.ico
                session-stat-enable: true
                session-stat-max-count: 10
                principal-session-name: 
                principal-cookie-name: 
                profile-enable: 
            #druid StatViewServlet监控配置
            stat-view-servlet:
                enabled: true
                url-pattern: /druid/*
                reset-enable: false
                login-username: admin
                login-password: admin
                #配置白名单，一般都是允许内网访问，保证安全性
                allow: 192.168.1.110,127.0.0.1
                #黑名单
                deny: 192.168.16.111
```

#### **自定义属性配置**

``` yml
#server:port=9090
server:
	port: ${random.int[1024,9999]}#随机端口
gwf:
	num: ${random.int}#随机数
	hello: hello#自定义
	name:  gwf  ${gwf.hello}  ${gwf.num}#配置变量的引用
```

``` java
@RestController
public class HelloController {
    @Value("${gwf.name}")
    private String msg;
    @RequestMapping("/hello")
    public String hello() {
        return this.msg;
    }
}
```

#### **SpringBoot日志配置**

> Spring Boot 配置方式一般有两种，1、命令模式配置（java -jar app.jar --debug=true  优先级最高）  2、资源文件配置    （application.yml）

一般可以编写logback-spring.xml日志文件（实现日志分级别存储、按天存档，保存天数），yml里面自定义文件路径

``` yml
#logback自定义路径配置
logback:
    logdir: D:/sen/log
    appname: sen
```

#### **集成Swagger**

> 最好导入springfox-swagger以及springfox-swagger-ui来实现swagger，版本2.8、2.7的最好.


* 导包
* 编写配置文件
* 编写注解：@Api、@ApiModel等。

``` xml
		<!-- springfox-swagger2 -->
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger2</artifactId>
			<version>2.8.0</version>
		</dependency>
		<!-- springfox-swagger-ui -->
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger-ui</artifactId>
			<version>2.8.0</version>
		</dependency>
```

配置类：

``` java
@Configuration
@EnableSwagger2
public class Swagger2 {
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.senyer.controller"))
                .paths(PathSelectors.any())
                .build();
    }
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("森的测试swagger2")
                .description("森德博客：https://senyer.github.io")
                .termsOfServiceUrl("https://senyer.github.io")
                .contact("森")
                .version("1.0.0")
                .build();
    }
}
```

#### **实现单\多文件的上传**
*

``` java
    @PostMapping("/upload1")
    @ResponseBody
    public Map<String, String> upload1(@RequestParam("file") MultipartFile file) throws IOException {
        log.info("[文件类型] - [{}]", file.getContentType());
        log.info("[文件名称] - [{}]", file.getOriginalFilename());
        log.info("[文件大小] - [{}]", file.getSize());
        // TODO 将文件写入到指定目录（具体开发中有可能是将文件写入到云存储/或者指定目录通过 Nginx 进行 gzip 压缩和反向代理，此处只是为了演示故将地址写成本地电脑指定目录）
        file.transferTo(new File("D:/sen/file/" + file.getOriginalFilename()));
        Map<String, String> result = new HashMap<>(16);
        result.put("contentType", file.getContentType());
        result.put("fileName", file.getOriginalFilename());
        result.put("fileSize", file.getSize() + "");
        return result;
    }

    @PostMapping("/upload2")
    @ResponseBody
    public List<Map<String, String>> upload2(@RequestParam("file") MultipartFile[] files) throws IOException {
        if (files == null || files.length == 0) {
            return null;
        }
        List<Map<String, String>> results = new ArrayList<>();
        for (MultipartFile file : files) {
            // TODO Spring Mvc 提供的写入方式
            file.transferTo(new File("D:/sen/file/" + file.getOriginalFilename()));
            Map<String, String> map = new HashMap<>(16);
            map.put("contentType", file.getContentType());
            map.put("fileName", file.getOriginalFilename());
            map.put("fileSize", file.getSize() + "");
            results.add(map);
        }
        return results;
    }
```

#### **开启定时任务**

* TimeTask方式（不推荐）

``` java
/**
 * 基于Timer实现的定时调度（不推荐，用该方式不如用 ScheduledExecutorService ）
 *
 * @author Levin
 * @since 2018/5/29 0029
 */
public class TimerDemo {

    public static void main(String[] args) {
        TimerTask timerTask = new TimerTask() {
            @Override
            public void run() {
                System.out.println("执行任务:" + LocalDateTime.now());
            }
        };
        Timer timer = new Timer();
        // timerTask：需要执行的任务
        // delay：延迟时间（以毫秒为单位）
        // period：间隔时间（以毫秒为单位）
        timer.schedule(timerTask, 1000, 3000);
    }
}
```
* ScheduledExecutorService 方式（推荐）

``` java 
/**
 * 基于 ScheduledExecutorService 方式,相对的比 Timer 要好
 *
 * @author Levin
 * @since 2018/5/29 0029
 */
public class ScheduledExecutorServiceDemo {
//Timer运行多个TimeTask时，只要其中有一个因任务报错没有捕获抛出的异常，其它任务便会自动终止运行，使用ScheduledExecutorService则可以规避这个问题
    public static void main(String[] args) {
        ScheduledExecutorService service = Executors.newScheduledThreadPool(10);
        // 参数：1、具体执行的任务   2、首次执行的延时时间
        //      3、任务执行间隔     4、间隔时间单位
        service.scheduleAtFixedRate(() -> System.out.println("执行任务A:" + LocalDateTime.now()), 0, 3, TimeUnit.SECONDS);
    }
}
```
* Spring Boot的方式（推荐）

> 需要先编写config类，然后调用相应的注解即可。  cron表达式，可以在网上搜cron在线生成器，获取想要的表达式。

``` java
@EnableAsync
//@EnableScheduling //测试需要打开该注解
@Configuration
public class SchedulerConfig {
    /**
     * 	很关键：默认情况下 TaskScheduler 的 poolSize = 1
     *
     * @return 线程池
     */
    @Bean
    public TaskScheduler taskScheduler() {
        ThreadPoolTaskScheduler taskScheduler = new ThreadPoolTaskScheduler();
        taskScheduler.setPoolSize(10);
        return taskScheduler;
    }
    /**
     * @EnableScheduling注解表示开启对@Scheduled注解的解析；同时new ThreadPoolTaskScheduler()也是相当的关键，
	 * 	通过阅读过源码可以发现默认情况下的private volatile int poolSize = 1;
	 * 	这就导致了多个任务的情况下容易出现竞争情况（多个任务的情况下，如果第一个任务没执行完毕，后续的任务将会进入等待状态）。
		@EnableAsync注解表示开启@Async注解的解析；作用就是将串行化的任务给并行化了。
		（@Scheduled(cron = "0/1 * * * * *")假设第一次工作时间为2018-05-29 17:30:55，工作周期为3秒；
		如果不加@Async那么下一次工作时间就是2018-05-29 17:30:59；如果加了@Async下一次工作时间就是2018-05-29 17:30:56）
     */
}
```

``` java
@Component
public class SpringTaskDemo {

    private static final Logger log = LoggerFactory.getLogger(SpringTaskDemo.class);

    @Async
    @Scheduled(cron = "0/1 * * * * *")
    public void scheduled1() throws InterruptedException {
        Thread.sleep(3000);
        log.info("scheduled1 每1秒执行一次：{}", LocalDateTime.now());
    }

    @Scheduled(fixedRate = 1000)
    public void scheduled2() throws InterruptedException {
        Thread.sleep(3000);
        log.info("scheduled2 每1秒执行一次：{}", LocalDateTime.now());
    }

    @Scheduled(fixedDelay = 3000)
    public void scheduled3() throws InterruptedException {
        Thread.sleep(5000);
        log.info("scheduled3 上次执行完毕后隔3秒继续执行：{}", LocalDateTime.now());
    }
}
```

#### 统一异常处理

``` java
@ControllerAdvice					捕获Controller层抛出的异常，如果添加@ResponseBody返回信息则为JSON格式。
@RestControllerAdvice				相当于@ControllerAdvice与@ResponseBody的结合体。
@ExceptionHandler					统一处理一种类的异常，减少代码重复率，降低复杂度。
```

``` java
/**
 * 全局异常处理
 * Created by Donghua.Chen on 2018/7/24.
 */
@RestControllerAdvice
public class GlobalExceptionHandler extends ResponseEntityExceptionHandler {

    /**
     * 定义要捕获的异常 可以多个 @ExceptionHandler({})
     *
     * @param request  request
     * @param e        exception
     * @param response response
     * @return 响应结果
     */
    @ExceptionHandler(CustomException.class)
    public ErrorResponseEntity customExceptionHandler(HttpServletRequest request, final Exception e, HttpServletResponse response) {
        response.setStatus(HttpStatus.BAD_REQUEST.value());
        CustomException exception = (CustomException) e;
        return new ErrorResponseEntity(exception.getCode(), exception.getMessage());
    }

    /**
     * 捕获  RuntimeException 异常
     * TODO  如果你觉得在一个 exceptionHandler 通过  if (e instanceof xxxException) 太麻烦
     * TODO  那么你还可以自己写多个不同的 exceptionHandler 处理不同异常
     *
     * @param request  request
     * @param e        exception
     * @param response response
     * @return 响应结果
     */
    @ExceptionHandler(RuntimeException.class)
    public ErrorResponseEntity runtimeExceptionHandler(HttpServletRequest request, final Exception e, HttpServletResponse response) {
        response.setStatus(HttpStatus.BAD_REQUEST.value());
        RuntimeException exception = (RuntimeException) e;
        return new ErrorResponseEntity(400, exception.getMessage());
    }

    /**
     * 通用的接口映射异常处理方
     */
    @Override
    protected ResponseEntity<Object> handleExceptionInternal(Exception ex, Object body, HttpHeaders headers,
                                                             HttpStatus status, WebRequest request) {
        if (ex instanceof MethodArgumentNotValidException) {
            MethodArgumentNotValidException exception = (MethodArgumentNotValidException) ex;
            return new ResponseEntity<>(new ErrorResponseEntity(status.value(), 
            exception.getBindingResult().getAllErrors().get(0).getDefaultMessage()), status);
        }
        if (ex instanceof MethodArgumentTypeMismatchException) {
            MethodArgumentTypeMismatchException exception = (MethodArgumentTypeMismatchException) ex;
            logger.error("参数转换失败，方法：" + exception.getParameter().getMethod().getName() + "，参数：" + exception.getName()
                    + ",信息：" + exception.getLocalizedMessage());
            return new ResponseEntity<>(new ErrorResponseEntity(status.value(), "参数转换失败"), status);
        }
        return new ResponseEntity<>(new ErrorResponseEntity(status.value(), "参数转换失败"), status);
    }
}
```


#### 数据验证

+ @NotNull							
+ @NotEmpty							验证注解的元素值不为 null 且不为空（字符串长度不为0、集合大小不为0）
+ @NotBlank							验证注解的元素值不为空（不为null、去除首位空格后长度为0），不同于@NotEmpty，@NotBlank只应用于字符串且在比较时会去除字符串的空格
+ @Pattern(value)					限制必须符合指定的正则表达式
+ @Size(max,min)					限制字符长度必须在 min 到 max 之间（也可以用在集合上）
+ @Email	
+ @Max(value)						限制必须为一个不大于指定值的数字
+ @Min(value)						
+ @DecimalMax(value)				限制必须为一个不大于指定值的数字
+ @DecimalMin(value)
+ @Past								限制必须是一个过去的日期
+ @Future							限制必须是一个将来的日期


----
+ @Validated：开启数据有效性校验，添加在类上即为验证方法，添加在方法参数中即为验证参数对象。（添加在方法上无效）
+ @NotBlank：被注释的字符串不允许为空（value.trim() > 0 ? true : false）
+ @Length：被注释的字符串的大小必须在指定的范围内
+ @NotNull：被注释的字段不允许为空(value != null ? true : false)
+ @DecimalMin：被注释的字段必须大于或等于指定的数值

> 以上注解基本够用，但是可以实现`自定义注解 `，自己编写验证器。参考博客：
[https://blog.battcn.com/2018/06/07/springboot/v2-other-validate3/](https://blog.battcn.com/2018/06/07/springboot/v2-other-validate3/)

> 可以实现分组验证：@NotBlank(message = "name 不允许为空", groups = Groups.Default.class) Groups.Default.class需要自己编写
``` java
public class Book {

    private Integer id;
    @NotBlank(message = "name 不允许为空")
    @Length(min = 2, max = 10, message = "name 长度必须在 {min} - {max} 之间")
    private String name;
    @NotNull(message = "price 不允许为空")
    @DecimalMin(value = "0.1", message = "价格不能低于 {value}")
    private BigDecimal price;

	getter......
	setter......
}
```


#### pom文件配置阿里镜像仓库

>  `插播`：一般的maven项目，在pom里面加上如下配置，可以将远程仓库改为国内的阿里镜像，这样下载速度提高。  并且省去了eclipse的相关maven的setting文件的麻烦。

``` xml
	<!--配置maven阿里云仓库开始,不用去改maven的setting -->
	<repositories>
		<repository>
			<id>public</id>
			<name>local private nexus</name>
			<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
			<releases>
				<enabled>true</enabled>
			</releases>
		</repository>
	</repositories>
	<pluginRepositories>
		<pluginRepository>
			<id>public</id>
			<name>local private nexus</name>
			<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
			<releases>
				<enabled>true</enabled>
			</releases>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</pluginRepository>
	</pluginRepositories>
	<!--配置maven阿里云结束 -->
```

#### 解决跨域问题

> 说明：  这里值提供一种方式，具体可以参考其他博文：[https://senyer.github.io/2018/11/20/cros-learn/](https://senyer.github.io/2018/11/20/cros-learn/)

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

#### @Async实现异步调用

> @Async所修饰的函数不要定义为static类型，这样异步调用不会生效

为了让@Async注解能够生效，还需要在Spring Boot的主程序中配置@EnableAsync，如下所示：

``` java
@SpringBootApplication
@EnableAsync
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```
通过使用@Async注解就能简单的将原来的同步函数变为异步函数，

``` java
@Component
public class Task {
    @Async
    public void doTaskOne() throws Exception {
        // 省略
    }
    @Async
    public void doTaskTwo() throws Exception {
        // 省略
    }
    @Async
    public void doTaskThree() throws Exception {
        // 省略
    }
}
```

#### 异步回调

> 如何判断上述异步调用是否已经执行完成呢？我们需要使用Future<T>来返回异步调用的结果

```  java
@Async
public Future<String> doTaskOne() throws Exception {
    System.out.println("开始做任务一");
    long start = System.currentTimeMillis();
    Thread.sleep(random.nextInt(10000));
    long end = System.currentTimeMillis();
    System.out.println("完成任务一，耗时：" + (end - start) + "毫秒");
    return new AsyncResult<>("任务一完成");
}
```

#### 工程结构推荐

``` java
com
  +- senyer
    +- myproject
      +- Application.java
      |
      +- domain
      |  +- User.java
      |
      +- mapper
      |  +- UserMapper.java
      |
      +- service
      |  +- UserService.java
      |  +- impl
      		|  +- UserServiceImpl.java
      |
      +- web
      |  +- UserController.java
      |
```



#### 工程部署与打包

* 方式1：通过jar包发布

	步骤1：在pom.xml文件点击右键，Run as 每次编译，最好先“Maven Clean”一下。再选择“Maven install”,然后刷新target文件夹，在target中会产生xxx.jar包，步骤1完毕。
> 注意1： 编译失败有可能是你的test路径下的测试类有问题
> 注意2： Eclipse的java环境需要配置成jdk，不能是jre，  查看：Eclipse-->Window-->preferences-->Java-->Installed JREs  例如： 需要改成jdk1.8.0.11 不能是 jre1.8.0.11

	步骤2：然后在cmd终端输入代码：  java -jar xxx.jar
	步骤3：在浏览器输入localhost等url进行访问
	

* 方式2：通过war包发布
	
	步骤1：在pom.xml文件中将jar修改为war
	步骤2：在pom.xml文件中将配置文件添加tomcat模块
	步骤3：在main方法处的启动类基继承SpringBootServletInitializer
	步骤4：在pom.xml点击右键选择Maven install，会在target文件夹中生成xxx.war包
	步骤5：将target中的xxx.war包复制到tomcat的webapps文件中，然后再启动tomcat服务器
	步骤6：通过浏览器url访问，注意url地址的写法
	
``` java
package com.xxx;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

@SpringBootApplication
public class SmallsystemApplication extends SpringBootServletInitializer{

    public static void main(String[] args) {
        SpringApplication.run(SmallsystemApplication.class, args);
    }

    @Override//为了打包springboot项目
    protected SpringApplicationBuilder configure(
            SpringApplicationBuilder builder) {
        return builder.sources(this.getClass());
    }
}
```


