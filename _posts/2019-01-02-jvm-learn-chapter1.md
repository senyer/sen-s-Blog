---
layout: post
title:  "【JVM】基本工具与命令行的使用"
categories: jvm
tags:  jvm tools
author: senyer
---

* content
{:toc}





## 前言
jvm监控工具的简单实用、基本命令行指令，问题排查基本流程、








## Java 性能分析器

VisualVM 、JConsole 

## Java 性能测试工具

LR、kylinPET

## Java 系统监控工具

nmon 或Linux（top sar）等自带命令

## 性能调优之系统

- 系统设置的调整：主要是对系统运行的基础平台设置进行调整，例如，根据应用需要调整UNIX系统的核心参数，调整数据库的内存池大小，调整应用服务器使用的内存大小，或是采用更高版本的JVM环境等；

- ulimit -a 用来显示当前的各种用户进程限制。
- Linux对于每个用户，系统限制其最大进程数。为提高性能，可以根据设备资源情况，设置各linux 用户的最大进程数，下面我把某linux用户的最大进程数设为10000个：

> ulimit -u 10000

- 对于需要做许多 socket 连接并使它们处于打开状态的 Java 应用程序而言，最好通过使用 ulimit -n xx 修改每个进程可打开的文件数，缺省值是 1024。
- ulimit -n 4096 将每个进程可以打开的文件数目加大到4096，缺省为1024
- 其他建议设置成无限制（unlimited）的一些重要设置是：

> 数据段长度：ulimit -d unlimited
> 最大内存大小：ulimit -m unlimited
> 堆栈大小：ulimit -s unlimited
> CPU 时间：ulimit -t unlimited
> 虚拟内存：ulimit -v unlimited


