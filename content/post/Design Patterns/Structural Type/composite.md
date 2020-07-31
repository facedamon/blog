---
title: "[设计模式 | 结构模式 | 合成模式]"
date: 2019-10-16T09:46:56+08:00
#lastmod: 2019-08-30T01:37:56+08:00
toc: true
draft: false
tags: ["设计模式"]
categories: ["设计模式/结构模式"]
author: "java-my-life"
---

> 转载自https://www.cnblogs.com/java-my-life/

--------------------------------------------

- 合成模式属于对象的结构模式,有时又叫做'部分-整体'模式.合成模式将对象组织到数结构中,可以用来描述整体与部分的关系.合成模式可以使客户端将单纯元素与复合元素同等看待.
- 文件系统是一个树形结构,树上长有节点.树的节点有两种,一种是树枝节点,有内部树结构;另一种是文件,即树叶节点,没有内部树结构.
- 合成模式可以不提供父对象的管理方法,但是合成模式必须在合适的地方提供子对象的管理方法,add,remove,getChild 等.
- 合成模式的实现根据所实现接口的区别分为两种形式,分别称为安全式和透明式.
- 安全式合成模式:要求管理聚集的方法只出现在树枝构件类中,而不出现在树叶构件类中.

![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Structural-Type/composite/架构图.png)

- Component(抽象构件):它给参加组合的对象定义出公共的接口及其默认行为,可以用来管理所有的子对象.合成对象通常把它所包含的子对象当作类型为Component的对象.在安全的合成模式,构件角色并不定义出管理子对象的方法,这一定义由树枝构件对象给出.
- Left(树叶构件):树叶对象是没有下级子对象,定义出参加组合的原始对象的行为.
- Composite(树枝构件):代表参加组合的有下级子对象.树枝构件类给出所有的管理子对象的方法.


        public interface Component{
            public void printStruct(String preStr);
        }



        public class Composite implements Component{
            private List<Component> childComponents = new ArrayList<Component>();
            
            private String name;
            
            public Composite(String name){
                this.name = name;
            }
            
            public void addChild(Component child){
                childComponents.add(child);
            }
            
            public void removeChild(int index){
                childComponents.remove(index);
            }
            
            public List<Component> getChild(){
                return childComponents;
            }
            
            @Override
            public void printStruct(String preStr){
                log.info(preStr + "+" + this.name);
                if (this.childComponents != null){
                    preStr += " ";
                    for (Component c : childComponents){
                        //递归输出每个子对象
                        c.printStruct(preStr);
                    }
                }
            }
        }



        public class Leaf implements Component{
            private String name;
            
            public Leaf(String name){
                this.name = name;
            }
            
            @Override
            public void printStruct(String preStr) {
                System.out.println(preStr + "-" + name);
            }
        }


- 可以看出,Composite给出了add,remove,get等方法的声明和实现,而Leaf构件类则没有给出这些方法或实现.这样的做法是安全的做法,由于这个特点,客户端应用程序不可能错误地调用树叶构件的聚集方法,因为树叶构件没有这些方法,调用会导致编译错误

- 透明式合成模式(与安全式的合成模式不同的是,透明式的合成模式要求所有具体构件类,不论树枝构件还是树叶构件,均符合一个固定接口)




        public abstract class Component{
            public abstract void printStruct(String preStr);
        }

        public void addChild(Component child){
            throw new UnsupportedOperationException("对象不支持此操作");
        }

        public void removeChild(int index){
            throw new UnsupportedOperationException("对象不支持此操作");
        }

        public List<Component> getChild(){
            throw new UnsupportedOperationException("对象不支持此操作");
        }


        public class Composite extends Component{
            private List<Component> childComponents = new ArraysList<Component>();
            
            private String name;
            public Composite(String name){
                this.name = name;
            }
            
            public void addChild(Component child){
                childComponents.add(child);
            }
            
            public void removeChild(int index){
                childComponents.remove(index);
            }
            
            public List<Component> getChild(){
                return childComponents;
            }
            
            @Override
            public void printStruct(String preStr){
                log.info(preStr + "+" + this.name);
                if (this.childComponents != null){
                    preStr += " ";
                    for (Component c : childComponents){
                        c.printStruct(preStr);
                    }
                }
            }
        }


        public class Leaf extends Component{
            private String name;
            public Leaf(String name){
                this.name = name;
            }
            
            @Override
            public void printStruct(String preStr){
                log.info(preStr + "-" + name);
            }
        }

> 区别

- 这里所说的安全性合成模式是指：从客户端使用合成模式上看是不再安全,如果是安全的,那么就不会发生误操作的可能,能访问的方法都是被支持的.
- 这里所说的透明性合成模式是指:从客户端使用合成模式上,是否需要区分到底是"树枝对象"还是"树叶对象".如果是透明的,那就不用区分对于客户而言,都是Component对象,具体的类型对于客户端而言是透明的,是无需关心的
- 对于合成模式而言,在安全性和透明性上,回更看重透明性,毕竟合成模式的目的是:让客户端不再区分操作的是树枝还是树叶对象,而是一个统一的方法来操作.
- 而且对于安全性的实现,需要区分是树枝对象还是树叶对象.有时候需要将对象进行类型转换,却发现类型信息丢失,只好强制转换,这种类型转换必然是不安全的.
- 因此在使用合成模式的时候,建议多采用透明性的实现方式
