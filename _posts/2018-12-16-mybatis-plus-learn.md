---
layout: post
title:  "【MyBatis-Plus】知识学习与整理"
categories: mybatis-plus
tags:  mybatis-plus 
author: senyer
---

* content
{:toc}




## 前言
学习mybatis-plus的代码自动生成，通用的CRUD，强大的wrapper条件构造器。



## 基本配置

> 官方文档：[http://mp.baomidou.com/](http://mp.baomidou.com/ "http://mp.baomidou.com/")

> 系统源码+测试用例：[https://gitee.com/baomidou/mybatis-plus](https://gitee.com/baomidou/mybatis-plus "https://gitee.com/baomidou/mybatis-plus")

> 我的项目：

- jdk1.8
- spring boot2.1.0
- mysql5.7
- lombok
- mybatis-plus3.0.6


## pom文件

> **注意：** springboot2.0+集成mybatis3.0+ 需要由两个关键的jar包需要导入，不然会出错。
>      mybatis-plus-boot-starter     和     mybatis-plus 

``` xml
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		
		<dependency>
			<groupId>com.baomidou</groupId>
			<artifactId>mybatis-plus-boot-starter</artifactId>
			<version>3.0.6</version>
		</dependency>


		<dependency>
			<groupId>com.baomidou</groupId>
			<artifactId>mybatis-plus</artifactId>
			<version>3.0.6</version>
		</dependency>

		<dependency>
			<groupId>org.apache.velocity</groupId>
			<artifactId>velocity-engine-core</artifactId>
			<version>2.0</version>
		</dependency>

		<!-- lombok -->
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<version>1.18.4</version>
			<scope>provided</scope>
		</dependency>
```

## 代码自动生成

>  要求：mybatis-plus3.0.6.jar和：velocity-engine-core.jar


``` java
package com.senyer.gen;
 
import java.sql.SQLException;
 
import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.ConstVal;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;
 
public class MyBatisPlusGenerator {

	/**
	 * 具体参考官方源码：https://gitee.com/baomidou/mybatis-plus/tree/3.0/mybatis-plus-generator/src/main/java/com/baomidou/mybatisplus/generator/config
	 */

	public static void main(String[] args) throws SQLException {
		//1. 全局配置
				GlobalConfig config = new GlobalConfig();
				config.setActiveRecord(true) // 是否支持AR模式
					  .setAuthor("Senyer") // 作者
					  .setOutputDir("D:\\JavaDeveloper\\My_WorkSpace\\workspace_Eclipse\\mybatis-plus-chapter1\\src\\main\\java") // 生成路径 --项目右键Properties--Resource---可以看到项目的路径
					  .setFileOverride(true)  // 文件覆盖
					  .setIdType(IdType.AUTO) // 主键策略
					  .setServiceName("%sService")  // 设置生成的service接口的名字的首字母是否为I，%s 为占位符“I%sService”
					  .setSwagger2(false)//是否开启 swagger2 模式
		 			  .setBaseResultMap(true)//生成基本的resultMap
		 			  .setBaseColumnList(true);//生成基本的SQL片段 
					  
				
		//2. 数据源配置
				DataSourceConfig  dsConfig  = new DataSourceConfig();
				dsConfig.setDbType(DbType.MYSQL)  // 设置数据库类型
						.setDriverName("com.mysql.cj.jdbc.Driver")
						.setUrl("jdbc:mysql://localhost:3306/mybatis_plus_learn_demo1?useUnicode=true&characterEncoding=utf8&allowMultiQueries=true&serverTimezone=GMT%2B8")
						.setUsername("root")
						.setPassword("root");
				 
		//3. 策略配置globalConfiguration中
				StrategyConfig stConfig = new StrategyConfig();
				stConfig.setCapitalMode(true) //全局大写命名
						.setNaming(NamingStrategy.underline_to_camel) // 数据库表映射到实体的命名策略
						//.setTablePrefix("sys_")//表前缀,如果不给定，则会默认给实体类加上此前缀：即User可能就变成了：SysUser。
						.setTablePrefix(new String[]{"sys_","shop_"})// 此处提供多个表的前缀去除
						.setEntityLombokModel(true)//【实体】是否为lombok模型（默认 false）
						.setRestControllerStyle(true)//restControllerStyle 生成@RestController 控制器
						.entityTableFieldAnnotationEnable(true)//是否生成实体时，生成字段注解
						// .setInclude(new String[] {"table_A"}) // 需要生成的表
                        // .setExclude(new String[]{"table_B","table_C"}) // 排除生成的表
						;  // 需要包含的表名，允许正则表达式（与exclude二选一配置）
				
		//4. 包名策略配置 
				PackageConfig pkConfig = new PackageConfig();
				pkConfig.setParent("")//父包名。如果为空，将下面子包名必须写全部， 否则就只需写子包名,建议写空，这样，可以让xml文件写入到templates下面，更为规范。
						.setMapper("com.senyer.mapper")//dao
						.setService("com.senyer.service")//servcie
						.setServiceImpl("com.senyer.service.impl")
						.setController("com.senyer.controller")//controller
						.setEntity("com.senyer.entity")
						.setXml("com.senyer.mapper.xml");//mapper.xml   //即："/templates/mapper.xml"
				
		//5. 整合配置
				AutoGenerator  ag = new AutoGenerator();
				ag.setGlobalConfig(config)
				  .setDataSource(dsConfig)
				  .setStrategy(stConfig)
				  .setPackageInfo(pkConfig);
				
		//6. 执行
				ag.execute();
	}
 
}

```

**截图：**

![](https://i.imgur.com/2sH8F2w.png)


## 测试用法

> 增删改查、分页、条件构造器。

``` java
package com.senyer.service.impl;

import static org.junit.Assert.*;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collection;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.core.conditions.update.UpdateWrapper;
import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import com.senyer.entity.User;
import com.senyer.mapper.UserMapper;
import com.senyer.service.UserService;

import lombok.extern.slf4j.Slf4j;


@RunWith(SpringRunner.class)
@SpringBootTest
@Slf4j
public class UserServiceImplTest {
	@Autowired
	private UserService userService;
	@Autowired
	private UserMapper userMapper;

	@Test//添加用户
	public void testSave() {
		User user=new User();
		user.setAge(18);
		user.setEmail("hakjshdskja@qq.com");
		user.setName("香香ii");
		userService.save(user);
		//尽量使用{}占位符
		log.info("插入成功 :{}+++++++++++++++++++++++",user.getName());
	}

	@Test//批量添加用户
	public void testSaveBatch() {
		List<User> list=new ArrayList<>();
		list.add(new User(1,"tom",18,"sadas@qq.com"));
		list.add(new User(24,"tom2",17,"sadas@qq.com"));
		list.add(new User(42,"tom2",17,"sadas@qq.com"));
		boolean saveBatch = userService.saveBatch(list);
		log.info("批量添加 :{}+++++++++++++++++++++++",saveBatch);
	}

	@Test//插入或者修改
	public void testSaveOrUpdate() {
		boolean saveOrUpdate = userService.saveOrUpdate(new User(177,"tom",18,"sadas@qq.com"));
		log.info("插入或者修改 :{}+++++++++++++++++++++++",saveOrUpdate);
		
	}

	@Test//批量插入或者修改
	public void testSaveOrUpdateBatch() {
		List<User> list=new ArrayList<>();
		list.add(new User(1,"tomxx",18,"sadas@qq.com"));
		list.add(new User(24,"tomXX",17,"sadas@qq.com"));
		list.add(new User(42,"tomXX",17,"sadas@qq.com"));
		boolean saveOrUpdate = userService.saveOrUpdateBatch(list);
		log.info("批量插入或者修改 :{}+++++++++++++++++++++++",saveOrUpdate);
		/** SQL语句执行如下：
		 *  Time：20 ms - ID：com.senyer.mapper.UserMapper.selectById
			Execute SQL：SELECT id,name,age,email FROM sys_user WHERE id=1
			
			 Time：0 ms - ID：com.senyer.mapper.UserMapper.updateById
			Execute SQL：UPDATE sys_user SET name='tomxx', age=18, email='sadas@qq.com' WHERE id=1
			
			 Time：0 ms - ID：com.senyer.mapper.UserMapper.selectById
			Execute SQL：SELECT id,name,age,email FROM sys_user WHERE id=24
			
			 Time：0 ms - ID：com.senyer.mapper.UserMapper.updateById
			Execute SQL：UPDATE sys_user SET name='tomXX', age=17, email='sadas@qq.com' WHERE id=24
			
			 Time：2 ms - ID：com.senyer.mapper.UserMapper.selectById
			Execute SQL：SELECT id,name,age,email FROM sys_user WHERE id=42
			
			 Time：0 ms - ID：com.senyer.mapper.UserMapper.insert
			Execute SQL：INSERT INTO sys_user ( name, age, email ) VALUES ( 'tomXX', 17, 'sadas@qq.com' )
		 */
	}

	@Test//根据id删除
	public void testRemoveById() {
		boolean removeById = userService.removeById(29);
		log.info("根据id删除 :{}+++++++++++++++++++++++",removeById);
	}

	@Test//根据条件删除：        	#####################前面都是根据id删除的，这个可以不按照id来进行删除！！很有用的语句。######################
	public void testRemoveByMap() {
	     //删除条件
	    Map<String, Object> columnMap1 = new HashMap<>();
	    columnMap1.put("id", 28);
		boolean removeById1 = userService.removeByMap(columnMap1);
		
		
		 Map<String, Object> columnMap2 = new HashMap<>();
		 columnMap2.put("name", "香香3");
		 columnMap2.put("id", 18);
		 boolean removeById2 = userService.removeByMap(columnMap2);
		 //Execute SQL：DELETE FROM sys_user WHERE name = '香香3' AND id = 18
		log.info("根据map删除 :{}+++++++++++++++++++++++",removeById2);
	}

	@Test//利用wrapper实现删除
	public void testRemove() {
		//3.0+版本将EntityWrapper改为了QueryWrapper
       boolean remove = userService.remove(new QueryWrapper<User>().eq("id",1));
       log.info("利用wrapper实现删除 :{}+++++++++++++++++++++++",remove);
       
	}

	@Test//根据id批量删除：
	public void testRemoveByIds() {
		List<Integer> idList = new ArrayList<>();
		 idList.add(16);
		 idList.add(17);
		 boolean removeByIds = userService.removeByIds(idList);
		 log.info("根据id批量删除： :{}+++++++++++++++++++++++",removeByIds);
	}

	@Test//根据id更新
	public void testUpdateById() {
		
		boolean updateById = userService.updateById(new User(24,"tomXX",17,"xxxxxxx@qq.com"));
		log.info("根据id更新（传对象）： :{}+++++++++++++++++++++++",updateById);
	}

	@Test //更新--需要new一个UpdateWrapper对象，来加上where后面的条件。
	public void testUpdate() {
		boolean update = userService.update(new User(24,"tomXX",17,"xxxxxxx@qq.com"), new UpdateWrapper<User>().eq("id", 11).eq("age", 17));
		//Execute SQL：UPDATE sys_user SET name='tomXX', age=17, email='xxxxxxx@qq.com' WHERE id = 11
		//Execute SQL：UPDATE sys_user SET name='tomXX', age=17, email='xxxxxxx@qq.com' WHERE id = 11 AND age = 17
		log.info("更新 :{}+++++++++++++++++++++++",update);
	}

	@Test//根据id批量更新
	public void testUpdateBatchById() {
		List<User> list=new ArrayList<>();
		list.add(new User(1,"tomxx",18,"sadas@qq.com"));
		list.add(new User(24,"tomXX",17,"sadas@qq.com"));
		list.add(new User(42,"tomXX",17,"sadas@qq.com"));
		boolean updateBatchById = userService.updateBatchById(list);
		log.info("根据id批量更新（传对象）： :{}+++++++++++++++++++++++",updateBatchById);
	}

	@Test//根据id查询
	public void testGetById() {
		User user = userService.getById(2);
		log.info("查询成功：{}+++++++++++++++++++++++",user.getName());
	}

	@Test//根据ids批量查询
	public void testListByIds() {
		List<Integer> asList = Arrays.asList(1,2,3,4,5);
		Collection<User> listByIds = userService.listByIds(asList);
		log.info("查询成功：{}+++++++++++++++++++++++",listByIds.toString());
		//Execute SQL：SELECT id,name,age,email FROM sys_user WHERE id IN ( 1 , 2 , 3 , 4 , 5 )
	}

	@Test//条件查询
	public void testListByMap() {
		 Map<String, Object> columnMap2 = new HashMap<>();
		 columnMap2.put("name", "tomXX");
		 columnMap2.put("age", 17);
		 Collection<User> listByMap = userService.listByMap(columnMap2);
		 //Execute SQL：SELECT id,name,age,email FROM sys_user WHERE name = 'tomXX' AND age = 17
		 log.info("查询成功：{}+++++++++++++++++++++++",listByMap.toString());
	}

	@Test//按条件获取一个记录
	public void testGetOne() {
		User one = userService.getOne(new QueryWrapper<User>().eq("id", 17));
		log.info("查询成功：{}+++++++++++++++++++++++",one.toString());
		//Execute SQL：SELECT id,name,age,email FROM sys_user WHERE id = 2
	}

	@Test//按条件查询Map集合
	public void testGetMap() {
		Map<String, Object> map = userService.getMap(new QueryWrapper<User>().eq("id", 17));
		log.info("查询成功：{}+++++++++++++++++++++++",map.get("name"));
		//Execute SQL：SELECT id,name,age,email FROM sys_user WHERE id = 17
	}

	@Test//按条件查询记录数
	public void testCount() {
		int count = userService.count();
		int count2 = userService.count(new QueryWrapper<User>().eq("age", 17));
		log.info("查询成功：{}+++++++++++++++++++++++",count2);
	}

	@Test//按条件查询集合
	public void testList() {
		List<User> list = userService.list(new QueryWrapper<User>().eq("age", 17));
		log.info("查询成功：{}+++++++++++++++++++++++",list.toString());
	}

	@Test//分页查询
	public void testPage() {
		
        
		IPage<User> page = userService.page(new Page<User>(1,3));
		log.info("查询成功：{}+++++++++++++++++++++++",page.getTotal());
		log.info("查询成功：{}+++++++++++++++++++++++",page.getRecords().size());
		log.info("查询成功：{}+++++++++++++++++++++++",page.getRecords());
		//Execute SQL：SELECT id,name,age,email FROM sys_user LIMIT 0,3
	}

	@Test//获取map集合
	public void testListMaps() {
		List<Map<String, Object>> listMaps = userService.listMaps(new QueryWrapper<User>().eq("age", 17));
		log.info("查询成功：{}+++++++++++++++++++++++",listMaps.toString());
	}

	@Test//一般也用不到了，没必要试
	public void testListObjs() {
		fail("Not yet implemented");
	}

	@Test//略....
	public void testPageMaps() {
		fail("Not yet implemented");
	}

}


```


## 插件集成
> 分页插件、SQL执行效率插件(开发和测试环境下的利器！生产环境可以去掉，利用@Profile注解实现),

``` java
package com.senyer.config;

import java.util.Properties;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.transaction.annotation.EnableTransactionManagement;

import com.baomidou.mybatisplus.extension.plugins.PaginationInterceptor;
import com.baomidou.mybatisplus.extension.plugins.PerformanceInterceptor;

/**
 * 
 * @author kk
 * MybatisPlus配置类
 */
//@EnableTransactionManagement
@Configuration
@MapperScan("com.senyer.mapper")
public class MybatisPlusConfig {

	
	/**
	 * 分页
	 * @return
	 */
	@Bean
	public PaginationInterceptor paginationInterceptor(){
		return new PaginationInterceptor();
	}
   	
	
	/**
     * SQL执行效率插件,很好用！值得推荐，可以在控制台查看执行的sql时间以及执行的语句！！！！！！！
     */
    @Bean
    public PerformanceInterceptor performanceInterceptor() {
        return new PerformanceInterceptor();
    }
}



```
