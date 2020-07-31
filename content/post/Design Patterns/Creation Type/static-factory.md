---
title: "[设计模式 | 创建模式 | 静态工厂模式]"
date: 2019-10-15T15:36:56+08:00
#lastmod: 2019-08-30T01:37:56+08:00
toc: true
draft: false
tags: ["设计模式"]
categories: ["设计模式/创建模式"]
author: "java-my-life"
---

> 转载自https://www.cnblogs.com/java-my-life/

### 简单工厂模式是类的创建型模式，又叫静态工厂方法模式。简单工厂模式是由一个工厂对象决定创建出哪一中产品类的实例
- 优点
模式的核心是工厂类。这个类含有必要的逻辑判断，额可以决定在什么时候创建哪一个实例，而调用者则可以避免直接创建对象的责任，简单工厂通过这种做法实现了对责任的分割，当系统引入新的功能的时候无需修改**调用者**
- 缺点
工厂类集中了所有的创建逻辑，当有复杂的多层次等级结构时，所有业务逻辑都在这个工厂类中实现。耦合度高。

> 案例

- `login.interface`

        public interface Login {
            boolean verify(String name,String password);
        }

- `DomainLogin`

        public class DomainLogin implements Login {
            public boolean verify(String name, String password) {
                return true;
            }
        }

- `PasswordLogin`

        public class PasswordLogin implements Login {
            public boolean verify(String name, String password) {
                return true;
            }
        }

- `LoginManager`

        public class LoginManager{
            public static Login factory(String type){
                if(type.equals("password")){
                    return new PasswordLogin();
                }else if(type.equals("passcode")){
                    return new DomainLogin();
                }else{
                    throw new RuntimeException();
                }
            }
        }

