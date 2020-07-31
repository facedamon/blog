---
title: "[设计模式 | 结构模式 | 装饰模式]"
date: 2019-10-24T14:44:56+08:00
#lastmod: 2019-08-30T01:37:56+08:00
toc: true
draft: false
tags: ["设计模式"]
categories: ["设计模式/结构模式"]
author: "java-my-life"
---

> 转载自https://www.cnblogs.com/java-my-life/

&emsp;&emsp;装饰模式又名包装(Wrapper)模式.装饰模式以对客户端透明的方式扩展对象的功能,是继承关系的一个替代方案.

- 结构: 装饰模式以对客户透明的方式动态地给一个对象附加更多的责任.换言之,客户端并不会觉得在装饰前和装饰后有什么不同.装饰模式可以在不使用创造更多子类的情况下,将对象的功能加以扩展

![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Structural-Type/wrapper/架构图.png)


- `Component(抽象构件)`:给出一个抽象接口,以规范准备接收附加责任对象
- `ConcreteComponent(具体构件)`:定义一个将要接收附加责任的类
- `Decorator(装饰角色)`:持有一个构件对象的实例,并定义一个与抽象构件接口一致的接口
- `ConcreteDecorator(具体装饰)`:负责给构件对象贴上附加的责任


        public interface Component{
            public void sampleOperation();
        }



        public class ConcreteComponent implements Component{
            @Override
            public void sampleOperation(){
                // TODO
            }
        }


        public class Decorator implements Component{
            private Component component;
            
            public Decorator (Component component){
                this.component = component;
            }
            
            @Override
            public void sampleOperation(){
                //委派给构件
                component.sampleOperation();
            }
        }


        public class ConcreteDecoratorA extends Decorator{
            public ConcreteDecoratorA(Component component){
                super(component);
            }
            
            @Override
            public void sampleOperation(){
                super.sampleOperation();
                // Extract TODO
            }
        }


- 装饰模式的简化
    - 如果只有一个ConcreteComponent类,那么可以考虑去掉抽象的Component接口,把Decorator作为一个ConcreteComponent子类

    - 如果只有一个ConcreteDecorator类,那么久没有必要建立一个单独的Decorator类,而可以把Decorator和ConcreteDecorator的责任合并成一个类.甚至在只有两个ConcreteDecorator类的情况下,都可以这样做.

- 透明性的要求
    - 装饰模式对客户端的透明性要求程序不要声明一个ConcreteComponent类型的变量,而应当声明一个Component类型的变量
    - 半透明的装饰模式
    - 然而,纯粹的装饰模式很难找到.装饰模式的用意是在不改变接口的前提下,增强所考虑的类的性能.在增强性能的时候,往往需要建立新的公开的方法.这就导致了大多数的装饰模式的实现是半透明的,而不是完全透明.换言之,允许装饰模式改变接口,增加新的方法.这意味着客户端可以声明ConcreteDecorator类型的变量,从而可以调用ConcreteDecorator类中才有的方法

# 半透明的装饰模式是介于装饰模式和适配器模式之间的.
