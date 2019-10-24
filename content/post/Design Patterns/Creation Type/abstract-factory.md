---
title: "[设计模式 | 创建模式 | 抽象工厂模式]"
date: 2019-10-15T15:43:56+08:00
#lastmod: 2019-08-30T01:37:56+08:00
toc: true
draft: false
tags: ["设计模式"]
categories: ["设计模式/创建模式"]
author: "java-my-life"
---

> 转载自https://www.cnblogs.com/java-my-life/

--------------------------------------------

> 概述

&emsp;&emsp;举个生活中常见的例子--组装电脑。

&emsp;&emsp;我们在组装电脑的时候，通常需要一系列的配件，比如CPU、硬盘、内存、主板、电源、机箱等。为讨论简单一些，只考虑CPU和主板的问题。

&emsp;&emsp;事实上，在选择CPU的时候，面临一系列的问题，比如品牌、型号、针脚数据、主频等，只有把这些问题都确定下来，才能确定具体的CPU。

&emsp;&emsp;同样，在选择主板的时候，也有一系列问题，比如品牌、芯片组、集成芯片、总线频率等问题，也只有这些都确定了，才能确定具体的主板。

&emsp;&emsp;选择不同的CPU和主板，是每个客户在组装电脑的时候，向装机公司提出的要求，也就是我们每个人拟定的装机方案。

&emsp;&emsp;在最终确定这个装机方案之前，还需要整体考虑各个配件之间的兼容性。比如：CPU和主板，如果使用Interl的CPU和AMD的主板是根本无法组装的，因为Intel的CPU针脚数与AMD主板提供的CPU插口不兼容，就是说如果使用intel的CPU根本就插不到AMD的主板中，所以装机方案是整体性的，里面选择的各个配件之间是有关联的。

&emsp;&emsp;对于装机工程师而言，他只知道组装一台电脑，需要相应的配件，但是具体使用什么样的配件，还得由客户端说了算。也就是说装机工程师只负责组装，而客户负责选择装配所需要的具体配件。因此，当装机工程师为不同的客户组黄电脑时，只需要根据客户的装机方案，去获取相应的配件，然后组装即可。

![avatar](https://raw.githubusercontent.com/facedamon/MarkDownPhotos/master/Design-Patterns/Creation-Type/abstract-factory/抽象工厂组装图.png)

- 缺点：没有维护配件之间的兼容性

- 引进抽象工厂模式

- 每一个模式都是针对移动问题的解决方案。抽象工厂模式与工厂方法模式的最大区别就在于，工厂方法模式针对的是一个产品等级结构；而抽象工厂则需要面对多个产品等级结构。

![avatar](https://raw.githubusercontent.com/facedamon/MarkDownPhotos/master/Design-Patterns/Creation-Type/abstract-factory/产品等级结构图.png)

- 产品族：指位于不同产品等级结构中，功能相关联的产品组成的，功能相关联的组成家族。

&emsp;&emsp;显然，每个产品族中含有产品的数目，与产品等级结构的数目是相等的。产品的等级结构与产品族将产品按照不同方向划分，形成一个二维的坐标系。横轴表示产品的等级结构，纵轴表示族，上图共有两个产品族，分布于三个不同的产品等级结构中。只要指明一个产品所处的产品族以及它所属的等级结构，就可以唯一的确定这个产品。
&emsp;&emsp;上面所给出的三个不同的等级结构具有平行的结构。因此，如果采用工厂方法模式，就势必要使用三个独立的工厂等级结构来对付这三个产品等级结构。由于这三个等级结构的相似性，会导致三个平行的工厂等级结构。随着产品等级结构的数目的增加，工厂方法模式所给出的工厂等级结构的数目也会随之增加。如下图：

![avatar](https://raw.githubusercontent.com/facedamon/MarkDownPhotos/master/Design-Patterns/Creation-Type/abstract-factory/产品族.png)

- 那么是否可以使用同一个工厂等级结构来对付这写相同或者极为相似的产品等级结构，这就是抽象工厂模式的好处，同一个工厂等级负责多个不同产品等级结构中产品的创建。

![avatar](https://raw.githubusercontent.com/facedamon/MarkDownPhotos/master/Design-Patterns/Creation-Type/abstract-factory/产品等级-产品族.png)

- 抽象工厂模式是对象的创建模式，它是工厂方法模式的进一步推广。假设一个子系统需要一些产品对象，而这些产品又属于一个以上的产品等级结构。那么为了将消费这些产品对象的责任和创建这些产品对象的责任分割开来，可以引进抽象工厂模式。这样的话，消费产品的一方不需要直接参与产品的创建工作，而只需要向一个公用的工厂接口请求所需要的产品。

![avatar](https://raw.githubusercontent.com/facedamon/MarkDownPhotos/master/Design-Patterns/Creation-Type/abstract-factory/组合图.png)

- 由于这两个产品族的等级结构相同，因此使用同一个工厂族也可以处理这两个产品族的创建问题，这就是抽象工厂模式。

![avatar](https://raw.githubusercontent.com/facedamon/MarkDownPhotos/master/Design-Patterns/Creation-Type/abstract-factory/架构图.png)

- 可以看出，每一个工厂角色都有两个工厂方法，分别负责创建分属不同产品等级结构的产品对象。

![avatar](https://raw.githubusercontent.com/facedamon/MarkDownPhotos/master/Design-Patterns/Creation-Type/abstract-factory/详细结构图.png)

- 前面实现的CPU接口和主板接口都不需要变化。前面示例中创建CPU的简单工厂和创建主板的简单工厂，都不需要，需要加入新的抽象工厂类和实现。

```
public interface AbstractFactory{
    public Cpu createCpu();
    public Mainboard createMainboard();
}
```

```
public class IntelFactory implements AbstractFactory{
    public Cpu createCpu(){
        return Interl(755);
    }
    
    public Mainboard createMainboard(){
        return new IntelMainboard(755);
    }
}
```
```
public class AmdFactory implements AbstractFactory{
    public Cpu createCpu(){
        return new AmdCpu(938);
    }
    
    public Mainboard createMainboard(){
        return new IntelMainboard()938;
    }
}
```

- 装机工程师类跟之前的实现相比，主要变化是：从客户端不再传入选择CPU和主板的参数，而是直接传入客户已经选择好的产品。这样就避免了单独去选择CPU和主板带来的兼容性问题，客户要选就选一套，就是一个系列。

```
public class ComputerEngineer{
    private Cpu cpu;
    private Mainboard mainboard;
    
    public void makeComputer(AbstractFactory af){
        prepareHardwares(af);
    }
    
    private void prepareHardwares(AbstractFactory af){
        this.cpu = af.createCpu();
        this.mainboard = af.createMainboard();
        
        this.cpu.calculate();
        this.mainboard.installCPU();
    }
}
```

```
public static void main(String[] args){
    ComputerEngineer cf = new ComputerEngineer();
    
    AbstractFacatory af = new InterlFactory();
    
    cf.makeComputer(af);
}
```

- 抽象工厂的功能是为一系列相关对象或相互依赖的对象创建一个接口。一定要注意，这个接口内的方法不是任意堆砌的，而是一系列相关或相互依赖的方法。
- 由于抽象工厂定义的一系列对象通常是相关或相互依赖的，这些产品对象就构成了一个产品族，也就是抽象工厂定义了一个产品族。这样就带来了非常大的灵活性，切换产品族的时候，只要提供不同的抽象工厂实现就可以了，也就是说现在是以一个产品族作为一个整体被切换。

> 适用场景
- 一个系统不应该依赖于产品类实例如何被创建，组合和表达的细节，这对于所有形态的工厂模式都是重要的。
- 这个系统的产品有多于一个的产品族，而系统只消费其中某一族的产品。
- 同属于同一个产品族的产品是在一起使用的，这一约束必须在系统的设计中体现出来。
- 系统提供一个产品类的库，所有的产品以同样的接口出现，从而使客户端不依赖于实现。

- 抽象工厂模式的优点
    - `分离接口和实现`：客户端使用抽象工厂来创建需要的对象，而客户端根本就不知道具体的实现是谁，客户端只是面向产品的接口编程而已。也就是说，客户端从具体的产品实现中解耦。
    - `切换产品族变得容易`：因为一个具体的工厂实现代表的是一个产品族。

- 抽象工厂模式的缺点
    - 不太容易扩展新的产品：如果需要给整个产品添加一个新的产品，那么就需要修改抽象工厂，这样就会导致修改所有的工厂实现类

> 案例

- `CPU.Interface`
```
public interface Cpu{
    public void calculate();
}
```
- `Mainboard.Interface`
```
public interface Mainboard{
    public void installCPU();
}
```
- `AmdCpu`
```
public class AmdCpu implements Cpu{
    public int pins;
    public AmdCpu(int pins){
        this.pins = pins;
    }
    
    @Override
    public void calculate(){
        //TODO
        System.out.println("AMD CPU的阵脚数:"+pins);
    }
}
```
- `IntelCpu`
```
public class IntelCpu implements Cpu{
    private int pins = 0;//针脚数
    public InterCpu(int pins){
        this.pins = pins;
    }
    
    @Override
    public void calculate(){
        //TODO
        System.out.println("Intel CPU 的阵脚数："+pins);
    }
}
```
- `AmdMainboard`
```
public class AmdMainboard implements Mainboard{
    private int cpuHoles = 0;
    
    public AmdMainboard(int cpuHoles){
        this.cpuHoles = cpuHoles;
    }
    @Override
    public void installCPU(){
        //TODO
        System.out.println("AMD主板的CPU插槽孔数是："+cpuHoles);
    }
}
```
- `InterMainBoard`
```
public class InterMainBoard implements Mainboard{
    private int cpuHoles = 0;//CPU插槽孔数
    public InterlMainboard(int cpuHoles){
        this.cpuHoles = cpuHoles;
    }
    
    @Override
    public void installCPU(){
        //TODO
        System.out.ptintln("Interl主板的CPU插槽孔数是："+cpuHoles);
    }
}
```
- `CpuFactory`
```
public class CpuFactory{
    public static Cpu createCpu(int type){
        Cpu cpu = null;
        if(type == 1){
            cpu = new IntelCpu(755);
        }else if(type == 2){
            cpu = new AmdCpu(938);
        }
        return cpu;
    }
}
```
- `MainboardFactory`
```
public class MainboardFactory{
    public static Mainboard createMainboard(int type){
        Mainboard mainboard = null;
        if(type == 1){
            mainboard = new IntelMainboard(755);
        }else if(type == 2){
            mainboard = new AmdMainboard(938);
        }
        return mainboard;
    }
}
```
- **`ComputerEngineer`**

```
public class ComputerEngineer{
    private Cpu cpu;
    private Mainboard mainboard;
    
    public void makeComputer(int cpuType,int mainboard){
        prepareHardwares(cpuType,mainboard);
    }
    
    private void prepareHardwares(int cpuType,int mainboard){
        this.cpu = CpuFactory.createCpu(cpuType);
        this.mainboard = MainboardFactory.createMainboard(mainboard);
        
        //测试
        this.cpu.calculate();
        this.mainboard.installCPU();
    }
}
```