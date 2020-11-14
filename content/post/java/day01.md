---
title: "[Java (一) | Java基础认识]"
date: 2020-11-07T14:20:55+08:00
#lastmod: 2019-08-30T01:37:56+08:00
toc: true
draft: false
tags: ["Java"]
categories: ["Java/学习"]
author: "facedamon"
---

# Java语言概述

## Java语言发展史

&emsp;&emsp;1977年詹姆斯·高斯林（James Gosling）获得了加拿大卡尔加里大学计算机科学学士学位，1983年获得了美国卡内基梅隆大学计算机科学博士学位，毕业后到IBM工作，设计IBM第一代工作站NeWS系统，但不受重视。后来转至Sun公司，1990年，与Patrick，Naughton和Mike Sheridan等人合作“绿色计划”，后来发展一套语言叫做“Oak”，后改名为Java。

- Java Development Kit
  - 1.0.2
  - 1.1.1 -> 1.1.6 -> 1.1.7 -> 1.1.8
  - 1.2.1
- Java 2 SDK
  - 1.2.2
  - 1.3.0 -> 1.3.1
  - 1.4.0 -> 1.4.1 -> 1.4.2 -> 1.5.0
  - 1.6.0

- Java 3 SDK
  - Java7
  - Java8
  - Java9
  - Java10
  - Java11
  - Java12
  - Java13 beat

## Java语言平台特性

- J2SE(Java 2 Platform Standard Edition) 标准版
  - 是为开发普通桌面和商务应用程序提供的解决方案
  - 该技术体系是其它两者的基础，可以完成一些桌面应用程序的开发


- J2ME(Java 2 Platform Model Edition) 移动版
  - 是为开发电子消费品和嵌入式设备提供的解决方案


- J2EE(Java 2 Platform Enterprise Edition) 企业版
  - 是为开发企业环境下的应用程序提供的一套解决方案
  - 该技术体系中包含的技术主要针对于Web应用程序开发

## Java语言特点

- 简单性
- 面向对象
- 健壮性
- 结构中立
- 高性能
- 多线程
- 动态
- 安全性
- 跨平台

> 什么是跨平台？

&emsp;&emsp;通过Java语言便携的应用程序在不同的操作系统平台上都可以运行。只需要在运行java应用的操作系统上，安装一个Java虚拟机(JVM Java Virtual Machine)即可。由JVM来负责Java程序在该系统中的运行。


![avatar](https://cdn.jsdelivr.net/gh/facedamon/markdownps2@master/java/跨平台讲解.png)

&emsp;&emsp;因为有了JVM，所以同一个Java程序在三个不同的操作系统中可以执行。这样就实现了Java程序的跨平台性。

## JRE与JDK

|JRE(Java Runtime Environment)|JDK(Java Development Kit)|
|------|------|
|&emsp;&emsp;包括Java虚拟机(JVM Java Virtual Machine)和Java程序所需的核心类库等，，如果想要运行一个开发好的Java程序，计算机中只需要安装JRE即可。|&emsp;&emsp;JDK是提供给Java开发人员使用的，其中包含了java的开发工具，也包含了JRE。所以安装了JDK，就不用再单独安装JRE了|

# HelloWorld

    class HelloWorld {
        public static void main(String[] args) {
            System.out.println("Hello World");
        }
    }

- 首先编写java源代码程序，扩展名 .java
- 在命令行模式中，输入javac命令对源代码进行编译，生成字节码文件
  - javac 原文件名.java
- 编译完成后，如果没有报错信息，输入java命令对class字节码文件进行解释运行，执行时不需要添加.class扩展名
  - java HelloWorld

## 运行与工作原理

![avatar](https://cdn.jsdelivr.net/gh/facedamon/markdownps2@master/java/运行与工作原理.png)

## 常见错误

- 找不到文件
- 单词拼写错误
- 括号匹配问题
- 中英文问题

## path环境变量配置的作用

- 程序的执行需要使用外部指令javac，但是javac指令仅仅能在JDK安装目录下的bin目录下作用，因此程序只能写入bin目录
- 程序开发过程中，不能将源代码写入JDK的安装目录，因此需要将源程序保存到任意位置的指定目录，所以需要使用javac指令在任意目录下可以运行

## path和classpath的区别

|path|classpath|
|------|------|
|&emsp;&emsp;path环境变量里面记录的是可执行文件，如.exe文件，对可执行文件先在当前路径去找，如果没找到就去path环境变量中配置的路径去找|&emsp;&emsp;classpath环境变量里记录的是java类的运行文件所在的目录|

# 基础语法

- 关键字
- 标识符
- 注释
- 常量、进制和进制转换
- 变量
- 数据类型和类型转换
- 运算符
- 语句

## 关键字

|概述|特点|
|------|------|
|被Java语言赋予特殊含义的单词|组成关键字的字母全部小写|

> 目前的关键字

|定义数据类型|
|------|
|class,interface,byte,short,int,long,float,double,char,boolean,void|

|定义数据类型值|
|------|
|true, false, null|

|流程控制|
|------|
|if,else,switch,case,default,while,do,for,break,continue,return|

|访问权限修饰符|
|------|
|private, protected, public|

|定义类，方法，变量修饰符的关键字|
|------|
|abstract, final, static, synchronized|

|定义类与类之间关系|
|------|
|extends, implements|


|建立对象及引用对象，判断对象|
|------|
|new, this, super, instanceof|

|异常处理|
|------|
|try, catch, finally, throw, throws|

|包|
|------|
|package, import|


## 标识符

- 概述
  - 就是给类，接口，方法，变量等起名字时使用的字符序列
- 组成规则
  - 英文大小写字母
  - 数字字符
  - $和_
- 注意事项
  - 不能以数字开头
  - 不能是Java中的关键字
  - 区分大小写

## 注释

- 概述
  - 用于解释说明程序的文字

- Java中注释分类
  - 单行注释
    - 格式：//注释文字
  - 多行注释：
    - 格式：/* 注释文字 */
  - 文档注释
    - 格式：/** 注释文字 */

</br>

    /**
     * 需求：我要完成HelloWorld案例
     * 分析：
     *    A：java语言最基本的单位是类，所以我们首先要定义一个类
     *    B：java程序要想能够独立运行，必须有主方法
     *    C：如果想要程序有标准输出，必须有输出语句

     * 步骤：
     *    A：定义类的格式
     *         class 类名 {
     *             //类体
     *         }
     *    B：主方法的格式
     *         public static void main(String[] args) {
     *             //方法体
     *         }
     *    C：输出语句的格式
     *         System.out.println("这里的内容可以修改");

     *  最后把我们的思想用代码给体现出现
    */
    class HelloWorld {
        /*
            这是main方法
            main方法是程序的入口
            jvm会自动调用main方法
        */
        public static void main(String[] args) {
            // 这是输出语句
            System.out.println("HelloWorld");
        }
    }

- **注释是一个软件工程师必须要具有的良好编程习惯**
- **初学者编写程序可以养成习惯：先写注释再写代码**
- **将自己的思想通过注释先整理出来，再用代码去体现**
- **因为代码仅仅是思想的一种体现形式而已**