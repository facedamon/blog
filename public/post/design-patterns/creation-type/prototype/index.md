
> 转载自https://www.cnblogs.com/java-my-life/

--------------------------------------------

- 通过给出一个原型对象来知名所有创建的对象的类型，然后用复制这个原型对象的办法创建出更多同类型的对象
- 原型模式结构:原型模式要求对象实现一个可以"克隆"自身的接口,这样就可以通过复制一个实例对象本身来创建一个新的实例.这样一来，通过原型实例创建新的对象，就不再需要关心这个实例本身的类型，只要实现了克隆自身的方法，就可以通过这个方法获取新的对象，而无须再去通过new来创建。
- 两种表现形式:
    - 简单形式
    - cache形式

- 简单形式的原型模式
![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Creation-Type/prototype/架构图.png)

- Prototype(抽象原型):通常油一个接口或抽象类实现。此角色给出所有的具体原型类所需的接口
- Concrete Prototype(具体原型):被复制的对象。此角色需要实现抽象的原型角色所要求的接口
- 主要解决问题:**在运行期建立和删除原型**
- **何时使用:**
    - 当一个系统应该独立于它的产品创建时
    - 当要实例化的类是在运行时刻指定时
    - 为了避免创建一个与产品类层次平行的工厂类层次
    - 当一个类的实例只能有几个不同状态组合中的一种时，建立相应数目的原型并克隆它们可能比每次用合适的状态手工实例化更方便
- 关键代码:
    - **实现克隆操作，继承Cloneable,重写clone()**
    - **原型模式同样用于隔离类对象的使用者和具体类型之间的耦合关系，它同样要求这些易变类拥有稳定的接口**
- 优点:
    - 性能提高
    - 避免构造函数的约束
- 缺点:
    - 配备克隆方法需要对类的功能进行全面考虑，这对于全新的类不是很难，但对于已有的类不一定很容易，特别当一个类引用不支持串行化的间接对象，或者引用含有循环结构的时候
    - 必须实现Cloneable接口

- 使用场景
    - 资源优化场景
    - 类初始化需要消耗非常多的资源，这资源包括数据硬件资源等
    - 性能和安全的场景
    - 通过new产生一个对象需要非常繁琐的数据准备或访问权限
    - 一个对象多个修改者的场景
    - 一个对象需要提供给其它对象访问，而各个调用者都可能需要修改其值时，使用原型克隆出多个对象供调用者使用
    - 在实际项目中，原型模式很少单独出现，一般是和工厂方法模式一起出现，通过clone的方法创建一个对象，然后由工厂方法提供提供给调用者

> 案例

- `Prototype.Interface`

        public interface Prototype{
            public Object clone();
        }

- `ConcretePrototype`

        public class ConcretePrototype implements Prototype{
            public Prototype clone(){
                Prototype prototype = new ConcretePrototype();
                return prototype;
            }
        }

- `Client`

        public class Client{
            private Prototype prototype;
            
            public Client(Prototype prototype){
                this.prototype = prototype;
            }
            
            public void opertation(Prototype example){
                Prototype copy = prototype.clone();
            }
        }

> 浅克隆&深克隆

- Object类提供protected Object clone()方法对对象进行复制.当使用Object类的clone()方法来复制一个对象时，此对象对其他对象也同时会被复制一份
- 克隆满足的条件:clone()方法将对象复制了一份并返还给调用者.所谓"复制"的含义与clone()方法是怎么实现的，一般而言，clone方法满足以下的描述
1. 对任何的对象x,都有:x.clone != x.换言之，克隆对象与元对象不是同一个对象
2. 对任何的对象x,都有:x.clone().getClass() == x.getClass(),换言之,克隆对象与原对象的类型一样
3. 如果对象x的equals()方法定义其恰当的话，那么x.clone().equals(x)应当成立的.

- 浅克隆和深克隆
    - 浅克隆:只负责克隆按值传递数据(比如基本数据类型,String类型),而不复制它所引用的对象,换言之,所有的对其他对象的引用都仍然指向原来的对象
    - 深度克隆:那些引用其他对象的变量将指向被复制过的新对象,而不再是原有的那些被引用的对象.换言之,**深度克隆把要复制的对象所引用的对象都复制了一遍**.深度克隆要深入到多少层,是一个不易确定的问题.在决定以深度克隆的方式复制一个对象的时候,必须决定对间接复制的对象时采取浅度克隆还是继续采用深度克隆.因此,在采用深度克隆时,需要决定多深，此外在深度克隆时,很可能会出现循环引用的问题,必须小心处理.

- 利用序列化实现深度克隆:把对象写到流里的过程是序列化(Serialzation)过程;而把对象从流中读出来的过程则叫反序列化(Deserialzation)过程.应当指出的是,写到流里的是对象的一个拷贝,而原对象仍然存在于JVM里面


        public Object deepClone() throws IOException,ClassNotFoundException{
            ByteArrayOutputStream bos = new ByteArrayOutputStream();
            ObjectOutputStream oos = new ObjectOutputStram(bos);
            oos.writeObject(this);
            
            ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
            ObjectInputStream ois = new ObjectInputStream(bis);
            return ois.readObject();
        }


- 浅度克隆显然比深度克隆更容易实现,因为Java语言的所有类都会继承一个clone方法,而这个clone所做的正式浅度克隆.有一些对象,比如Thread或者Socket对象，是不能简单复制或共享的.不管是使用浅度克隆还是深度克隆,只要设计这样的间接对象,就必须把见解对象设置成transient而不予复制;或者由程序自行创建出相当的同种对象,权且当作复制使用

> 案例2

- 我们将创建一个抽象类Shape和扩展了Shape类的实体类
- 下一步定义ShapeCache,该类把shape对象存储在一个hashtable中，并在请求的时候返回它们的克隆
- PrototypePatternDemo ,演示类使用试用ShapeCache来获取Shape对象

![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Creation-Type/prototype/案例.png)

> 案例2代码

- `Abstract Shape`

        public abstract class Shape Implements Cloneable{
            @Setter
            @Getter
            private String id;
            
            @Getter
            @Construct
            private String type;
            
            @Override
            public Object clone(){
                Object clone = null;
                try{
                    clone = super.clone();
                }catch(CloneNotSupportedException e){
                    e.printStackTrace();
                }
                return clone;
            }
        }

- `Rectangle`

        pulic class Rectangle extends Shape{
            public Rectangle(){
                type = "Rectangle";
            }
            
            @Override
            public void draw(){
                log.info("Inside Rectangle:draw() method.");
            }
        }

- `Square`

        public class Square extenda Shape{
            public Square(){
                type = "Square";
            }
            
            @Override
            public void draw(){
                log.info("Inside Square::draw() method.");
            }
        }

- `Circle`

        public class Circle extends Shape{
            public Circle(){
                type = "Circle";
            }
            
            @Override
            public void draw(){
                log.info("Inside Circle::draw() method.");
            }
        }

- `ShapeCache`

        public class ShapeCache{
            private static Hashtable<String,Shape> shapeMap = new Hashtable();
            
            public static Shape getShape (String shapeId){
                Shape cachedShape = shapeMap.get(shapeId);
                return (Shape) cachedShape.clone();
            }
            
            public static void loadCache(){
                Circle circle = new Circle();
                circle.setId("1");
                shapeMap.put(circle.getId,circle);
                
                Square square = new Square();
                square.setId("2");
                shapeMap.put(square.getId(),square);
                
                Rectangle rectangle = new Rectangle();
                rectabgle.setId("3");
                shapeMap.put(rectangle.getId(),rectangle);
            }
            
        }
