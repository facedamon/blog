---
title: "[设计模式 | 结构模式 | 门面模式]"
date: 2019-10-24T10:59:56+08:00
#lastmod: 2019-08-30T01:37:56+08:00
toc: true
draft: false
tags: ["设计模式"]
categories: ["设计模式/结构模式"]
author: "java-my-life"
---

> 转载自https://www.cnblogs.com/java-my-life/

- 外观模式,隐藏系统的复杂性,并向客户端提供了一个客户端可以访问系统的接口.这种类型的设计模式属于结构型模式,它向现有的系统添加一个接口,来隐藏系统的复杂性
- 这种模式设计到一个单一的类,该类提供了客户端请求的假话方法和现有系统类方法的委托调用

![avatar](https://raw.githubusercontent.com/facedamon/MarkDownPhotos/master/Design-Patterns/Structural-Type/facade/架构图.png)

- `Facade(门面角色)`:客户端可以调用这个角色，此角色知晓相关子系统的功能和责任.
- `SubSystem(子系统)`:可以同时有一个或者多个子系统。每个子系统都不是一个单独的类，而是一个类的集合（如上面的子系统就是由ModuleA、ModuleB、ModuleC三个类组合而成）。每个子系统都可以被客户端直接调用，或者被门面角色调用。子系统并不知道门面的存在，对于子系统而言，门面仅仅是另外一个客户端而已
- 使用门面模式还有一个附带的好处，就是能够有选择性地暴露方法。一个模块中定义的方法可以分成两部分，一部分是给子系统外部使用的，一部分是子系统内部模块之间相互调用时使用的。有了Facade类，那么用于子系统内部模块之间相互调用的方法就不用暴露给子系统外部了

```
public class Module {
    /**
     * 提供给子系统外部使用的方法
     */
    public void a1(){};
    
    /**
     * 子系统内部模块之间相互调用时使用的方法
     */
    public void a2(){};
    public void a3(){};
}

public class ModuleB {
    /**
     * 提供给子系统外部使用的方法
     */
    public void b1(){};
    
    /**
     * 子系统内部模块之间相互调用时使用的方法
     */
    public void b2(){};
    public void b3(){};
}

public class ModuleC {
    /**
     * 提供给子系统外部使用的方法
     */
    public void c1(){};
    
    /**
     * 子系统内部模块之间相互调用时使用的方法
     */
    public void c2(){};
    public void c3(){};
}

public class ModuleFacade {
    
    ModuleA a = new ModuleA();
    ModuleB b = new ModuleB();
    ModuleC c = new ModuleC();
    /**
     * 下面这些是A、B、C模块对子系统外部提供的方法
     */
    public void a1(){
        a.a1();
    }
    public void b1(){
        b.b1();
    }
    public void c1(){
        c.c1();
    }
}
```
- 这样定义一个ModuleFacade类可以有效地屏蔽内部的细节，免得客户端去调用Module类时，发现一些不需要它知道的方法。比如a2()和a3()方法就不需要让客户端知道，否则既暴露了内部的细节，又让客户端迷惑。对客户端来说，他可能还要去思考a2()、a3()方法用来干什么呢？其实a2()和a3()方法是内部模块之间交互的，原本就不是对子系统外部的，所以干脆就不要让客户端知道
- 优点：
    1. `松散耦合`。门面模式松散了客户端与子系统的耦合关系，让子系统内部的模块能更容易扩展和维护。
    2. `简单易用`。门面模式让子系统更加易用，客户端不再需要了解子系统内部的实现，也不需要跟众多子系统内部的模块进行交互，只需要跟门面类交互就可以了。
    3. `更好的划分访问层次`。通过合理使用Facade，可以帮助我们更好地划分访问的层次。有些方法是对系统外的，有些方法是系统内部使用的。把需要暴露给外部的功能集中到门面中，这样既方便客户端使用，也很好地隐藏了内部的细节

> Servlet 中的门面模式


![avatar](https://raw.githubusercontent.com/facedamon/MarkDownPhotos/master/Design-Patterns/Structural-Type/facade/servlet.png)


```
public class TestServlet extends HttpServlet {

    public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        this.doPost(request, response);
            
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
            
        
    }

}
```

![avatar](https://raw.githubusercontent.com/facedamon/MarkDownPhotos/master/Design-Patterns/Structural-Type/facade/servlet-facade.png)

- 注意红色方框圈中的类，StandardWrapperValue类中的invoke方法225行代码如下：

```
filterChain.doFilter(request.getRequest(), response.getResponse());
```

- 在StandardWrapperValue类中并没有直接将Request对象与Response对象传递给ApplicationFilterChain类的doFilter方法，传递的是RequestFacade与ResponseFacade对象，为什么这么说呢，看一下request.getRequest()与response.getResponse()方法就真相大白了。

```
public HttpServletRequest getRequest() {
        if (facade == null) {
            facade = new RequestFacade(this);
        }
        return facade;
    }
    
public HttpServletResponse getResponse() {
        if (facade == null) {
            facade = new ResponseFacade(this);
        }
        return (facade);
    }
```