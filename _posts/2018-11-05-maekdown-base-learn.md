---
layout: post
title:  "【MarkDown】基础语法学习"
categories: 学习教程
tags:  语法 基础 markdown
author: senyer
---

* content
{:toc}



## 前言
markdown语法的基础性学习，以便更好的进行我的jekyll+github博客网站的书写。
## markdown介绍

Markdown是一种轻量级的标记语言，它允许人们使用易读易写的纯文本格式编写文档，借助可实现快速排版且转换成格式丰富的HTML页面。语法十分简单，常用标记符号少，学习时间少，一旦掌握这种标记语言，将极大提高效率。

Markdown用简洁的语法代替排版，而不像常用文字处理软件Word或Pages等进行排版、字体、插入等设置。标记语言可以通过键盘即实现字体大小、插入表格，图片，超链接，脚注等。

## Markdown的工具
### Mac平台：

Mou工具：免费

UlyssesⅢ：收费

其余软件还有 iA Writer，Writer Pro等。

### Windows端：

MarkdownPad，
MarkPad，
马可飞象（专为印象笔记打造的编辑器），
Miu，Atom，Typora，Rstudio等。

## 基本语法
#### 1. 标题设置

- 第一种方法（常用）：在标题文字前加#，一级标题为#，二级标题为##，三级标题为###，以此类推，最多六级，其中一级标题文字最大。

- 第二种方法：在标题文字下行输入--（符号前不可有空格），可表示二级标题。在标题文字下行输入==，可表示一级标题。

#### 2. 引用块注释

">"小于号的符号，在一段文字前表示引用
#### 3. 文字

*文字*斜体（一对星号框起来），**文字**加粗（一对**框起来），“*”可用“_”代替，也能实现斜体和加粗。


删除划线内容：~~要划除的行内内容~~（一对~~框起来）

文字间换行：在文字前输入

文字下有分割线：---或者***，使用时候注意要在文字下和---之间有空行。

文字底纹：`文字内容`

#### 4. 列表

无序列表：文字前添加*或+或-和一个空格

有序列表：罗马数字和英文句号和一个空格

#### 5. 超链接

内联方式：[www.baidu.com](网址链接)`[www.baidu.com](网址链接)`

#### 6. 图片

![文字内容]()，括号内为图片名称，图片需要和.md文件放在一起，或者为网络位置。

不建议使用代码及文件路径方式，可以使用编辑器功能图标上传网络位置图片或者本地图片。
####7. 表格

注：  :---代表左对齐，---:代表右对齐，:---:代表居中对齐，-数目至少一个，:没有默认左对齐，第二行必须有，否则不是表格形式。以上格式代表是三行三列的表格。

#### 8. 代码（黑色底纹）

第一种：简单文字出现一个代码框。使用```代码区```。```要单独一行（`不是单引号而是左上角的ESC下面~中的`）。

第二种：大片文字需要实现代码框，文字前按键盘Tab键。


> 注意：选择语言，代码会有相应的高亮显示：java、js、xml等

示例代码块：

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello World");
    }
}
```

### 9. 脚注

文字内容[^1]

在文章末尾[^1]:脚注说明文字

注：1可以为任意数字或文字或单词，但文章末尾[^]中内容与文中应保持一致。

### 10. 定义字体颜色

浅红色文字：<font color="#dd0000">浅红色文字：</font><br /> 
深红色文字：<font color="#660000">深红色文字</font><br /> 
浅绿色文字：<font color="#00dd00">浅绿色文字</font><br /> 
深绿色文字：<font color="#006600">深绿色文字</font><br /> 
浅蓝色文字：<font color="#0000dd">浅蓝色文字</font><br /> 
深蓝色文字：<font color="#000066">深蓝色文字</font><br /> 
浅黄色文字：<font color="#dddd00">浅黄色文字</font><br /> 
深黄色文字：<font color="#666600">深黄色文字</font><br /> 
浅青色文字：<font color="#00dddd">浅青色文字</font><br /> 
深青色文字：<font color="#006666">深青色文字</font><br /> 
浅紫色文字：<font color="#dd00dd">浅紫色文字</font><br /> 
深紫色文字：<font color="#660066">深紫色文字</font><br />
    
    浅红色文字：<font color="#dd0000">浅红色文字：</font><br /> 
    深红色文字：<font color="#660000">深红色文字</font><br /> 
    浅绿色文字：<font color="#00dd00">浅绿色文字</font><br /> 
    深绿色文字：<font color="#006600">深绿色文字</font><br /> 
    浅蓝色文字：<font color="#0000dd">浅蓝色文字</font><br /> 
    深蓝色文字：<font color="#000066">深蓝色文字</font><br /> 
    浅黄色文字：<font color="#dddd00">浅黄色文字</font><br /> 
    深黄色文字：<font color="#666600">深黄色文字</font><br /> 
    浅青色文字：<font color="#00dddd">浅青色文字</font><br /> 
    深青色文字：<font color="#006666">深青色文字</font><br /> 
    浅紫色文字：<font color="#dd00dd">浅紫色文字</font><br /> 
    深紫色文字：<font color="#660066">深紫色文字</font><br />

### 11. 其他补充

Markdown中的转义字符为\，若不想使符号变成文字的格式等，在符号前加\。

选择笔记，添加标签（显示为灰色底纹）：@(笔记本)[标签A|标签B]。

自动生成目录（三个字母应全大写或全小写）：[TOC]。

复选框（注意有空格）：使用- [ ]和- [x]语法可以创建复选框。

常见功能已经列出，其余如公式流程图时序图等见示例，更多说明见参考链接。但复杂的公式流程图等还是建议用专门软件更为便捷。


### markdown支持的语法高亮语言
*使用方式*

     ``` key
      代码段 
     ```

>语法集合
>

![](https://i.imgur.com/iMi9Ugu.png)
![](https://i.imgur.com/a2bJU1c.png)
![](https://i.imgur.com/V6HeDwb.png)
![](https://i.imgur.com/rDV8MOs.png)
![](https://i.imgur.com/f1utjk4.png)
![](https://i.imgur.com/f2WBLe0.png)
![](https://i.imgur.com/idU2WRG.png)