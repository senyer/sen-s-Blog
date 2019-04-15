---
layout: post
title:  "【Linux】完整的Java项目部署"
categories: linux
tags:  linux
author: senyer
---

* content
{:toc}





## 前言
基于Linux服务器完成一个Java web项目的部署







## 服务器购买


> 购买阿里云服务器（用学生身份购买有很大优惠）
> 
> 按流程走下来时，**注意设置一下安全组**：入方向、出方向等，开放基本的端口出来，除了系统默认的22\80\3389等。还可以开放443\3306\3789等常用端口。


## 远程连接

1. 下载xshell和xftp
2. 远程连接访问，再usr/local/src下面创建自己的一系列文件夹：java、tomcat、redis、nginx、project等，便于管理
3. 利用xftp上传下载好的jdk和tomcat等（Linux版）
4. 在指定文件夹下解压缩    tar -zxvf xxxxx.tar.gz、 tar-zxvf jdk-版本编号-linux-x64.tar.gz-C/usr/java/jdk/
5. 配置jdk的环境变量,java -version  进行验证。

``` java

export JAVA_HOME=/usr/local/src/senyer/software/java/jdk1.8.0_144

export PATH=$JAVA_HOME/bin:$PATH

export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.ja

```
> **vi /etc/profile**  插入以上信息、然后 ** resource /etc/profile **

6. 启动tomcat进行验证，先在本地输入curl http:// 127.0.0.1:8080，确保服务已启动
7. 外网再进行访问测试。不通要么就是安全组端口没有开放、要么就是防火墙没有开放指定端口
8. 查看防火墙状态： **firewall-cmd --state**
9. 启动防火墙： **systemctl start firewalld**  新旧版本的linux命令有差异性

## 编辑文件的编辑与退出方式

1. vi xx/xx  然后按i或者a，进入编辑界面
2. ESC+最快捷的方法：按了ESC后，直接按shift+zz，或者切换到大写模式按ZZ，就可以保存退出了，即是按2下大写的Z
3. ESC+在最后输入命令时，直接输入"x"，也是一样的，即X=WQ。
4. ESC + :wq
5. w表示write、q表示quit


## CentOS7使用firewalld打开关闭防火墙与端口

**1、firewalld的基本使用**

> 启动： systemctl start firewalld
> 
> 关闭： systemctl stop firewalld
> 
> 查看状态： systemctl status firewalld 
> 
> 开机禁用  ： systemctl disable firewalld
> 
> 开机启用  ： systemctl enable firewalld


**2.systemctl是CentOS7的服务管理工具中主要的工具，它融合之前service和chkconfig的功能于一体。**

> 启动一个服务：systemctl start firewalld.service
> 
> 关闭一个服务：systemctl stop firewalld.service
> 
> 重启一个服务：systemctl restart firewalld.service
> 
> 显示一个服务的状态：systemctl status firewalld.service
> 
> 在开机时启用一个服务：systemctl enable firewalld.service
> 
> 在开机时禁用一个服务：systemctl disable firewalld.service
> 
> 查看服务是否开机启动：systemctl is-enabled firewalld.service
> 
> 查看已启动的服务列表：systemctl list-unit-files|grep enabled
> 
> 查看启动失败的服务列表：systemctl --failed

**3.配置firewalld-cmd**

> 查看版本： firewall-cmd --version
> 
> 查看帮助： firewall-cmd --help
> 
> 显示状态： firewall-cmd --state
> 
> 查看所有打开的端口： firewall-cmd --zone=public --list-ports
> 
> 更新防火墙规则： firewall-cmd --reload
> 
> 查看区域信息:  firewall-cmd --get-active-zones
> 
> 查看指定接口所属区域： firewall-cmd --get-zone-of-interface=eth0
> 
> 拒绝所有包：firewall-cmd --panic-on
> 
> 取消拒绝状态： firewall-cmd --panic-off
> 
> 查看是否拒绝： firewall-cmd --query-panic
 
**那怎么开启一个端口呢**

添加

**firewall-cmd --zone=public --add-port=80/tcp --permanent**    （--permanent永久生效，没有此参数重启后失效）

重新载入

**firewall-cmd --reload**

查看

**firewall-cmd --zone= public --query-port=80/tcp**

删除

**firewall-cmd --zone= public --remove-port=80/tcp --permanent**


## 在云服务器下部署 Java一共提供几种方式？不同方式优缺点是什么？

一共三种方式。这三种方式分别是Java镜像部署、一键安装包部署和手动部署。前两种方式优点是方便快捷，缺点是要花钱！而且价格个人觉得不便宜，Java镜像部署一个月要￥80，一键安装包部署虽然一次只要5块，但是本文作者没试过也就不好评价。而手动部署方式的优缺点正好与前两种方式相反，它优点是免费同时修炼了你的技术，缺点就是稍微费事了点。


## 总结

> 关键是需需要正确开启防火墙以及配置安全组策略，这样阿里云的服务，才能在外网进行访问
> 启动tomcat服务：进入tomcat的bin文件夹，执行startup.sh即可
> 
