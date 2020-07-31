---
title: "[设计模式 | 结构模式 | 代理模式]"
date: 2019-10-15T19:06:56+08:00
#lastmod: 2019-08-30T01:37:56+08:00
toc: true
draft: false
tags: ["设计模式"]
categories: ["设计模式/结构模式"]
author: "java-my-life"
---

> 转载自https://www.cnblogs.com/java-my-life/

--------------------------------------------

## 定义
创建具有现有对象的对象，以便向外界提供功能接口
## 目的
为其他对象提供一种代理以控制对这个对象的访问
## 时机
想在访问一个类时做一些控制
## 使用场景
按职责来划分，通常有以下使用场景： 1、远程代理。 2、虚拟代理。 3、Copy-on-Write 代理。 4、保护（Protect or Access）代理。 5、Cache代理。 6、防火墙（Firewall）代理。 7、同步化（Synchronization）代理。 8、智能引用（Smart Reference）代理


- `和Adapter的区别`:**适配器模式主要改变所考虑对象的接口,而代理模式不能改变所代理对象的接口**
- `和Decorator的区别`:**装饰器模式主要为了增强功能,而代理模式主要为了增强控制**
- 优点:职责清晰，高扩展性，智能化
- 缺点:由于在客户端和真是主题址间增加了代理对象,因此有些类型的代理模式可能会造成请求的处理速度缓慢。实现代理模式需要额外的工作

> 静态代理


        public interface IUserDao{
            void save();
        }


        //Readl Object
        public class UserDao implements IUserDao{
            public void save(){
                sout("has saved");
            }
        }


        public class UserDaoProxy implements IUserDao{
            private IUserDao target;
            
            public UserDaoProxy(IUserDao target){
                this.target = target;
            }
            
            public void save(){
                target.save();
            }
        }


        public class JunitTestCase{
            public void test(){
                UserDao target = new UserDao();
                UserDaoProxy proxy = new UserDapProxy(target);
                proxy.save();
            }
        }


- 缺点: 因为代理对象需要与目标对象实现一样的接口,所以会有很多代理类。很难维护

> 动态代理

- 特点：
    - 代理对象不需要实现接口
    - 代理对象的生成是利用JDK的API，动态的在内存中构件代理对象
    - 动态代理也叫做JDK代理

- JDK中生成代理对象的API
代理类所在包:java.lang.reflect.Proxy
JDK实现代理只需要使用newProxyInstance方法,但是该方法需要接受三个参数


        static Object newProxyInstance(ClassLoader loader,Class<?>[] interfaces,InvocationHandler h);


参数|说明
-------- | ---
ClassLoader loader|指定当前目标对象使用的类加载器
Class<?>[] interfaces|目标对象实现的接口的类型,使用泛型方式确认类型
InvocationHandler h|事件处理,执行目标对象的方法时,会触发事件处理器的方法,会把当前执行目标对象的方法作为参数传入


        public class ProxyFactory{
            public static Object getProxyInstance(IUser target){
                return Proxy.newProxyInstance(
                        target.getClass().getClassLoader(),
                        target.getClass().getInterfaces(),
                        (proxy,method,args)->{
                            System.out.println("first transaction");
                            return method.invoke(target,args);
                        }
                );
            }
        }


        public class App {
            private IUser user;
            @Before
            public void before(){
                user = new User();
            }

            @Test
            public void test(){
                IUser proxy = (IUser) ProxyFactory.getProxyInstance(user);
                proxy.save();
            }
        }

代理对象不需要实现接口，但是目标对象需要实现接口，否则无法动态代理。

> CGLIB

- 有时候目标对象只是一个单独的对象，并没有实现任何的接口，这个时候就可以使用以目标对象子类的方式实现代理
- Cglib代理也叫做子类代理，它是在内存中构件一个子类对象从而实现对目标对象功能的扩展
- JDK的动态代理有一个限制,就是使用动态代理的对象必须实现一个或多个接口,如果想代理没有实现接口的类,就可以使用Cglib
- Cglib是一个想打的高性能的代码生成包，它可以在运行期扩展Java类与实现Java接口，它广泛的被许多AOP框架使用，为它们提供方法的interception
- Cglib包的底层是通过使用一个小而块的字节码处理框架ASM来转换字节码生成新的类


        public class CglibProxyFactory implements MethodInterceptor {

            private UserDao target;

            public CglibProxyFactory(UserDao target){
                this.target = target;
            }

            /**
            * 获取实例
            * @return
            */
            public Object getTargetInstance(){
                /**
                * 增强器
                */
                Enhancer enhancer = new Enhancer();
                enhancer.setSuperclass(target.getClass());
                enhancer.setCallback(this);
                return enhancer.create();
            }

            @Override
            public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                System.out.println("begin");
                return method.invoke(target,objects);
            }
        }


        public class App{
        @Test
        public void test(){
            UserDao target = new UserDao();
            UserDao proxy = (UserDao) new CglibProxyFactory(target).getTargetInstance();
            proxy.save();
        }
        }


# 在Spring的AOP编程中:
如果加入容器的目标对象有实现接口,用JDK代理
如果目标对象没有实现接口,用Cglib代理
