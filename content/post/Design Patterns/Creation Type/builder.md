---
title: "[设计模式 | 创建模式 | 构建模式]"
date: 2019-10-15T17:06:56+08:00
#lastmod: 2019-08-30T01:37:56+08:00
toc: true
draft: false
tags: ["设计模式"]
categories: ["设计模式/创建模式"]
author: "java-my-life"
---

> 转载自https://www.cnblogs.com/java-my-life/

--------------------------------------------

- 建造模式是对象的创建模式。建造模式可以将一个产品的内部表象(internal representation)与产品的生产过程分割开来，从而可以使一个建造过程生成具有不同的内部表象的产品对象

- 产品的内部表象:一个产品常有不同组成成分作为产品零件，这些零件有可能是对象，也可能不是对象，它们通常又叫做产品的内部表象。

- 作用:使客户端不需要知道所生产的产品有哪些零件,每个产品的对应零件彼此有何不同，是如何建造出来以及怎么组装。

- 结构

![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Creation-Type/builder/架构图.png)

- `Builder(抽象建造者)`:抽象接口，规范产品对象的各个组成成分的建造。此接口独立于应用程序的商业逻辑。模式中直接创建产品对象的是具体建造者(ConcreteBuilder)角色.具体建造者类必须实现这个接口所要求的两种方法:一种是**建造者方法**，一种是**返还结构方法**。有多少零件就有多少构造方法
- `ConCreteBuilder(具体构造者)`:担任这个角色的是应用程序紧密相关的一些类，它们在应用程序调用下创建产品实例.该角色要完成的任务包括:1.实现Builder接口，给出完整的创建产品实例操作.2.在构造完成后，提供产品的实例.
- `Director(导演)`:担任这个角色的类调用具体建造者角色以创建产品对象.应当指出的是，导演者角色并没有产品类的具体知识，真正拥有产品类的具体知识的是具体建造着角色.
- `Product(产品)`:这些产品类并不一定有共同的接口，而完全可以是不相关联的.

- **在什么情况下使用建建造模式：**
1. 需要生成的产品对象有复杂的内部结构，每一个内部成分可以是对象，也可以仅仅是一个对象的一个组成部分
2. 需要生成的产品的属性相互依赖。建造模式可以强制一种分步骤进行的建造过程，因此，如果产品对象的一个属性必须在另一个属性被赋值之后可以被赋值，使用建造模式是一个很好的设计思想。
3. 在对象创建过程中会使用到系统中的其他一些对象，这些对象在产品对象的创建过程中不易得到

> 案例

- `Builder.Interface`

        public interface Builder{
            public void builderPartOne();
            public void builderPartTwo();
            public Product retrieveResult();
        }

- `Product`

        public class Product{
            private String partOne;
            private String partTwo;
            
            //setter/getter method
        }

- `ConcreteBuilder`

        public class ConcreteBuilder implements Builder{
            private Product product = new Product();
            
            public void builderPartOne(){
                product.setPartOne("uuid:9527");
            }
            
            public void builderPartTwo(){
                product.setPartTwo("name:iphoneX");
            }
            
            public Product retrieveResult(){
                return product;
            }
        }

- `Director`

        public class Director{
            @Setter
            private Builder builder;
            
            public void construct(){
                builder.builderPartOne();
                builder.builderPartTwo();
            }
        }


> 肯德基案例

- 应用实例: 去肯德基，汉堡、可乐、薯条、炸鸡翅等是不变的，而其组合是经常变化的，生成出所谓的"套餐"。Java中的StringBuilder

- 优点：建造者独立，易扩展。便于控制风险
- 缺点:产品必须有共同点，范围限制.如内部变化复杂,会有很多的建造类
- 使用场景:需要生成的对象具有复杂的内部结构,需要生成的对象内部属性本身相互依赖
- 与工厂模式区别:建造者模式更加关注与零件装配的顺序

- 我们假设一个快餐店的商业案例，其中，一个典型的套餐可以是一个汉堡（Burger）和一杯冷饮（Colddrink）。汉堡（Burger）可以是素食汉堡（Veg Burger）或鸡肉汉堡（Chicken Burger），它们是包在纸盒中。冷饮（Colddrink）可以是可口可乐（coke）或百事可乐（pepsi），它们是装在瓶子中。我们将创建一个表示食物条目（比如汉堡和冷饮）的 Item 接口和实现 Item 接口的实体类，以及一个表示食物包装的 Packing 接口和实现Packing接口的实体类，汉堡是包在纸盒中，冷饮是装在瓶子中。然后我们创建一个 Meal 类，带有 Item 的 ArrayList 和一个通过结合 Item 来创建不同类型的 Meal 对象的 MealBuilder。BuilderPatternDemo，我们的演示类使用 MealBuilder 来创建一个 Meal

![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Creation-Type/builder/快餐架构图.png)

> 肯德基案例程序

- `Item`

        // abstract top product
        public interface Item{
            public String name;
            public Packing packing();
            public float price();
        }

- `Packing 封装属性，分离行为`

        public interface Packing{
            public String pack();
        }

- `Wrapper`

        public class Wrapper implements Packing{
            public String pack(){
                retirn "Wrapper";
            }
        }

- `Bottle`

        public class Bottle implements Packing{
            public String pack(){
                return "Bottle";
            }
        }

- `Burger`

        public abstract class Burger implements Item{
            @Override
            public Packing packing(){
                return new Wrapper();
            }
            
            @Override
            public abstract float price();
        }

- `ColdDrink`

        public abstract class ColdDrink implements Itme{
            @Override
            public Packing packing(){
                return new Bottle();
            }
            
            @Override
            public abstract float price();
        }

- `VeBurger`

        public class VeBurger extends Burger{
            @Override
            public float price(){
                return 25.0f;
            }
            
            @Override
            public String name(){
                return "Veg Burger";
            }
        }

- `ChickenBurger`

        public class ChickenBurger extends Burger{
            @Override
            public float price(){
                return 50.5f;
            }
            
            @Override
            public String name(){
                return "Coke";
            }
        }

- `Pepsi`

        public class Pepsi extends ColdDrink{
            @Override
            public float price(){
                return 35.0f;
            }
            
            @Override
            public String name(){
                return "Pepsi";
            }
        }

------------------------------
- `Meal`

        public class Meal{
            private List<Item> items = new ArrayList();
            
            public void addItem(Item item){
                items.add(item);
            }
            
            public float getCost(){
                float cost = 0.0f;
                for (Item item : items){
                    cost += item.price();
                }
                return cost;
            }
            
            public void showItems(){
                for (Item item : items){
                    log.info("Item:{},Packing:{},Price",item.name,item.pack(),item.price());
                }
            }
        }

- `MealBuilder`

        public class MealBuilder(){
            public Meal prepareVegMeal (){
                Meal meal = new Meal();
                meal.addItem(new VegBurger());
                meal.addItem(new Coke());
                return meal;
            }
            
            public Meal prepareNonVegMeal(){
                Meal meal = new Meal();
                meal.addItem(new ChickenBurger());
                meal.addItem(new Pepsi());
                return meal;
            }
        }
