---
title: "[设计模式 | 结构模式 | 享元模式]"
date: 2019-10-24T14:34:56+08:00
#lastmod: 2019-08-30T01:37:56+08:00
toc: true
draft: false
tags: ["设计模式"]
categories: ["设计模式/结构模式"]
author: "java-my-life"
---

> 转载自https://www.cnblogs.com/java-my-life/

- 主要用于减少创建对象的数量,以减少内存占用和提高性能,它提供了减少对象数量从而改善应用所需要的对象结构的方式
- 关键代码：**HashMap存储**
- 应用实例：
    - Java中的String,如果有则返回,如果没有则创建一个字符串保存在字符串缓存池里面
    - 数据库连接池

- 优点：大大减少对象的创建,降低系统的内存,使效率提高
- 缺点：增加了系统的复杂性,需要分离出外部状态和内部状态,,而且外部状态具有固化的性质,不应该随着内部状态的变化而变化,否则会造成系统的混乱.

- 享元对象能做到共享的关键是区分内蕴状态(Internal State)和外蕴状态(External State)

![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Structural-Type/flyweight/架构图.png)

- `Flyweight(**抽象享元**)`：给出一个抽象接口，以规定出所有具体享元角色需要实现的方法
- `ConcreteFlyweight(**具体享元**)`：实现抽象享元角色所规定的接口,如果有内蕴状态的话，必须负责为内蕴状态提供存储空间
- `FlyweightFactory(**享元工厂**)`：负责创建和管理享元角色。当一个客户端对象调用一个享元对象的时候，享元工厂会检查系统中是否有一个符合要求的享元对象。如果已经有了，享元工厂角色就应当提供这个已有的享元对象；如果系统中没有一个适当的享元对象的话，享元工厂角色就应当创建一个合适的享元对象

> 案例

```
public interface Flyweight{
    public void operation(String state);
}
```

```
public class ConcreteFlyweight impmelemts Flyweight{
    private final Character internal = null;
    
    public ConcreteFlyweight(Character internal){
        this.internal = internal;
    }
    
    /**
    *外蕴状态作为参数传入方法中，改变方法的行为
    */
    @Override
    public void operation(String state){
        sout("internal state=" + this.internal);
        sout("external state=" + state);
    }
}
```
```
public class FlyweightFactory{
    private final Map<Character,Flyweight> files = new HashMap();
    
    public Flyweight factory(Character state){
        Flyweight fly = files.get(state);
        if (null == fly){
            fly = new ConcreteFlyweight(state);
            files.put(state,fly);
        }
        return fly;
    }
}
```







