---
title: "[设计模式 | 创建模式 | 动态工厂模式]"
date: 2019-10-15T15:42:56+08:00
#lastmod: 2019-08-30T01:37:56+08:00
toc: true
draft: false
tags: ["设计模式"]
categories: ["设计模式/创建模式"]
author: "java-my-life"
---

> 转载自https://www.cnblogs.com/java-my-life/


- 工厂方法模式是类的创建模式，又叫多态性工厂模式，他的用意是定义一个创建产品对象的工厂接口，将实际创建工作推迟到子类中。
- `ExportFactory(抽象工厂)`:担任这个角色的是工厂方法模式的核心,任何在模式中创建对象的工厂类必须实现这个接口。在实际的系统中，这个角色也常常使用抽象类实现 。
- `ExportHtmlFactory,ExportPdFactory(具体工厂)`:担任这个角色实现了抽象接口的具体工厂类。具体工厂角色含有与业务密切相关的逻辑，并且受到使用者的调用以创建导出类。
- `ExportFile(抽象导出)`:工厂方法模式所创建的对象的超类，也就是所有导出类的共同父类或者共有的接口。在实际的系统中没这个角色也常常使用抽象类实现。
- `ExportStandardHtmlFile(具体导出)`:这个角色实现了抽象导出角色所声明的接口，工厂方法模式所创建每一个对象都是某个具体导出角色实例。

- `工厂方法模式和简单工厂模式`：**工厂方法模式和简单工厂模式在结构上的不同很明显。工厂方法模式的核心是一个抽象工厂类，而简单工厂模式把核心放在一个具体类上。**
- 如果系统需要加入一个新的导出类型，那么所需要的就是向系统中加入一个这个导出类以及所对应的工厂类。没有必要修改客户端，也没有必要修改抽象工厂角色或者其它已有的具体工厂角色。对于增加新的导出类型而言，这个系统完全支持开闭原则

> 案例

- `ExportFile`
```
public interface ExportFile {
    boolean export(String data);
}
```
- `ExportFactory`
```
public interface ExportFactory {
    ExportFile factory(String type);
}
```
- `ExportHtmlFactory`
```
public class ExportHtmlFactory implements ExportFactory {
    public ExportFile factory(String type) {
        if("standard".equals(type)){
            return new ExportStandardHtmlFile();
        }else if("financial".equals(type)){
            return new ExportFinancialHtmlFile();
        }else{
            throw new RuntimeException("没有找到对象");
        }
    }
}
```
