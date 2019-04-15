---
layout: post
title:  "【Linux】linux基本操作指令"
categories: linux
tags:  linux,xshell
author: senyer
---

* content
{:toc}





## 前言
很多都会选择linux作为一个项目的服务器。linux服务器更加稳定。linux对于一个java开发者而言，是很有必要掌握的。 
本文主要是介绍xshell的基本用法和linux的基本指令。帮我我们可以进行web项目的部署，以及简单维护的目的。





## Linux基本目录说明

> /usr：系统级的目录，可以理解为C:/Windows/，/usr/lib理解为C:/Windows/System32。
> /usr/local：用户级的程序目录，可以理解为C:/Progrem Files/。用户自己编译的软件默认会安装到这个目录下。
> /opt：用户级的程序目录，可以理解为D:/Software，opt有可选的意思，这里可以用于放置第三方大型软件（或游戏），当你不需要时，直接rm -rf掉即可。在硬盘容量不够时，也可将/opt单独挂载到其他磁盘上使用。
> 
> 源码放哪里？
> /usr/src：系统级的源码目录。
> /usr/local/src：用户级的源码目录
> 
> usr 指 Unix System Resource

## Linux基本指令

### **操作指令**

> pwd：显示工作路径 。
> 
> ls：查看目录中的文件 。
> 
> ls -F：查看目录中的文件 。
> 
> ls -l：显示文件和目录的详细资料 。
> 
> ls -a：显示隐藏文件 。
> 
> ls *[0-9]*：显示包含数字的文件名和目录名 。
> 
> tree：显示文件和目录由根目录开始的树形结构(1) 。
> 
> lstree：显示文件和目录由根目录开始的树形结构(2) 。
> 
> mkdir dir1：创建一个叫做 'dir1' 的目录' 。
> 
> mkdir dir1 dir2：同时创建两个目录 。



### **系统**

>  uname –a                           # 查看内核/操作系统/CPU信息
>  head -n 1 /etc/issue                # 查看操作系统版本
>  cat /proc/cpuinfo                   # 查看CPU信息
>  hostname                            # 查看计算机名
>  ifconfig –a                        # 查看网络配置
>  lspci –tv                          # 列出所有PCI设备
>  lsusb –tv                          # 列出所有USB设备
>  lsmod                               # 列出加载的内核模块
>  env                                 # 查看环境变量

 

### **资源**

>  free –m                            # 查看内存使用量和交换区使用量 
> 
>  df –h                              # 查看各分区使用情况 
> 
>  du -sh <目录名                    # 查看指定目录的大小 
> 
>  grep MemTotal /proc/meminfo         # 查看内存总量
> 
>  grep MemFree /proc/meminfo          # 查看空闲内存量
> 
>  uptime                              # 查看系统运行时间、用户数、负载 
> 
>  cat /proc/loadavg                   # 查看系统负载

 

### 磁盘和分区

Sfdisk -s

>  mount | column –t  # 查看挂接的分区状态 
> 
>  fdisk –l   # 查看所有分区 
> 
>  swapon –s  # 查看所有交换分区
> 
>  hdparm -i /dev/had  # 查看磁盘参数(仅适用于IDE设备) 
> 
>  dmesg | grep IDE# 查看启动时IDE设备检测状况

 

### 网络

>  ifconfig             　　　　　　 　# 查看所有网络接口的属性
> 
>  iptables -L             　　　　　　# 查看防火墙设置 
> 
>  route -n               　　　　　　 # 查看路由表 
> 
>  netstat -lntp             　　　　  # 查看所有监听端口 
> 
>  netstat -antp             　　　　　# 查看所有已经建立的连接
> 
>  netstat -s             　　　　　　 # 查看网络统计信息

 

### 进程

>  ps -ef              　　　　　　　　 # 查看所有进程 

 

### 用户

>  w                  　　　　　　　　# 查看活动用户 
> 
>  id <用户名             　　　　　# 查看指定用户信息 
> 
>  last                　　　　　　　 # 查看用户登录日志 
> 
>  cut -d: -f1 /etc/passwd            # 查看系统所有用户 
> 
>  cut -d: -f1 /etc/group             # 查看系统所有组 
> 
>  crontab -l                         # 查看当前用户的计划任务

 

### 服务

>  chkconfig --list                   # 列出所有系统服务 
> 
>  chkconfig --list | grep on         # 列出所有启动的系统服务

 

### 程序

>  rpm -qa                            # 查看所有安装的软件包

### 卷组

> Vgdisplay
> 
> Vgdisplay –v
> 
> 硬件信息
> 
> cat /etc/sysconfig/hwconfig



 