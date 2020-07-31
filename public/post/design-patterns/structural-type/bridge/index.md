
> 转载自https://www.cnblogs.com/java-my-life/

**&emsp;&emsp;桥梁模式是对象的结构模式。又称为柄体(Handle and Body)模式或接口(Interface)模式。桥梁模式的用意是“将抽象化(Abstraction)与实现化(Implementation)脱耦，使得二者可以独立地变化”**

- 桥梁模式虽然不是一个使用频率很高的模式，但是熟悉这个模式对于理解面向对象的设计原则，包括“开-闭”原则以及组合/聚合复用原则都很有帮助。理解好这两个原则，有助于形成正确的设计思想和培养良好的设计风格。

- 桥梁模式的用意是“将抽象化(Abstraction)与实现化(Implementation)脱耦，使得二者可以独立地变化”。这句话很短，但是第一次读到这句话的人很可能都会思考良久而不解其意。
这句话有三个关键词，也就是抽象化、实现化和脱耦。理解这三个词所代表的概念是理解桥梁模式用意的关键

- `抽象化`：从众多的事物中抽取出共同的、本质性的特征，而舍弃其非本质的特征，就是抽象化。例如苹果、香蕉、生梨、 桃子等，它们共同的特性就是水果。得出水果概念的过程，就是一个抽象化的过程。要抽象，就必须进行比较，没有比较就无法找到在本质上共同的部分。共同特征是指那些能把一类事物与他类事物区分开来的特征，这些具有区分作用的特征又称本质特征。因此抽取事物的共同特征就是抽取事物的本质特征，舍弃非本质的特征。 所以抽象化的过程也是一个裁剪的过程。在抽象时，同与不同，决定于从什么角度上来抽象。抽象的角度取决于分析问题的目的
- `实现化`：一个类的实例就是这个类的实例化，一个具体子类是它的抽象超类的实例化
- `脱耦`：所谓耦合，就是两个实体的行为的某种强关联。而将它们的强关联去掉，就是耦合的解脱，或称脱耦。在这里，脱耦是指将抽象化和实现化之间的耦合解脱开，或者说是将它们之间的强关联改换成弱关联。

&emsp;&emsp;所谓强关联，就是在编译时期已经确定的，无法在运行时期动态改变的关联；所谓弱关联，就是可以动态地确定并且可以在运行时期动态地改变的关联。显然，在Java语言中，继承关系是强关联，而聚合关系是弱关联。

&emsp;&emsp;将两个角色之间的继承关系改为聚合关系，就是将它们之间的强关联改换成为弱关联。因此，桥梁模式中的所谓脱耦，就是指在一个软件系统的抽象化和实现化之间使用聚合关系而不是继承关系，从而使两者可以相对独立地变化。这就是桥梁模式的用意

![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Structural-Type/bridge/架构图.png)


- 系统含有两个等级结构：
    1. 由抽象化角色和修正抽象化角色组成的抽象化等级结构
    2. 由实现化角色和两个具体实现化角色组成的实现化等级结构

- 桥梁模式设计的角色：
    1. `Abstraction(抽象化角色)`:抽象化给出的定义，并保存一个对实现化对象的引用。
    2. `RefinedAbstraction(修正抽象化角色)`:扩展抽象化角色，改变和修正父类对抽象化的定义
    3. `Implementor(实现化角色)`：这个角色给出实现化角色的接口，但不给出具体的实现。必须指出的是，这个接口不一定和抽象化角色的接口定义相同，实际上，这两个接口可以非常不一样。实现化角色应当只给出底层操作，而抽象化角色应当只给出基于底层操作的更高一层的操作
    4. `ConcreteImplementor(具体实现化角色)`:这个角色给出实现化角色接口的具体实现

- 优点：分离抽象和实现部分，让抽象部分和实现部分独立出来，分别定义接口，这有助于对系统进行分层，从而产生更好的结构化的系统

> 发送消息案例

&emsp;&emsp;考虑这样一个实际的业务功能：发送提示消息。基本上所有带业务流程处理的系统都会有这样的功能，比如OA上有尚未处理完毕的文件，需要发送一条消息提示他。

&emsp;&emsp;从业务上看，消息又分成普通消息、加急消息和特急消息多种，不同的消息类型，业务功能处理是不一样的，比如加急消息是在消息上添加加急，而特急消息除了添加特急外，还会做一条催促的记录，多久不完成会继续催促；从发送消息的手段上看，又有系统内短消息、手机短信息、邮件等

- 不使用模式的解决方案
    1. 实现发送普通消息    

![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Structural-Type/bridge/发送普通消息.png)

    2. 实现发送加急消息

![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Structural-Type/bridge/发送加急消息.png)

    3. 实现发送特急消息

![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Structural-Type/bridge/特急消息.png)


- 观察上面的系统结构图，会发现一个很明显的问题，那就是通过这种继承的方式来扩展消息处理，会非常不方便。实现加急消息处理的时候，必须实现系统内短消息和邮件两种处理方式，因为业务处理可能不同，在实现特急消息处理的时候，又必须实现系统内短信息和邮件两种处理方式。这意味着，以后每次扩展一下消息处理，都必须要实现这两种处理方式，这还不算完，如果要添加新的实现方式呢？

- 采用通过继承来扩展的实现方式，有个明显的缺点，扩展消息的种类不太容易。不同种类的消息具有不同的业务，也就是有不同的实现，在这种情况下，每一种类的消息，需要实现所有不同的消息发送方式。更可怕的是，如果要新加入一种消息的发送方式，那么会要求所有的消息种类都有加入这种新的发送方式的实现

> 采用桥梁模式

- 根据业务的功能要求，业务的变化具有两个维度，一个维度是抽象的消息，包括普通消息、加急消息和特急消息，这几个抽象的消息本身就具有一定的关系，加急消息和特急消息会扩展普通消息；另一个维度是在具体的消息发送方式上，包括系统内短消息、邮件和手机短消息，这几个方式是平等的，可被切换的方式


![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Structural-Type/bridge/桥梁模式消息.png)

- 要想解决这个问题，就必须把这两个纬度分开，也就是将抽象部分和实现部分分开，让它们相互独立，这样就可以实现独立的变化，使扩展变得简单。抽象部分就是各个消息的类型所对应的功能，而实现部分就是各种发送消息的方式。按照桥梁模式的结构，给抽象部分和实现部分分别定义接口，然后分别实现它们就可以了(**封装不变的，开放变化的**)


        public interface MessageImplementor{
            send (String message,String toUser);
        }


        public class MessageSMS implements MessageImplementor{
            @Override
            public void send(String message,String toUser){
                sout("使用系统内短消息的方法，发送消息'"+message+"'给"+toUser);
            }
        }


        public class MessageEmail implements MessageImplementor{
            @Override
            public void send(String message,String toUser){
                sout("使用邮件短消息的方法，发送消息'"+message+"'给"+toUser);
            }
        }


        public abstract class AbstractMessage{
            MessageImplementor impl;
            public AbstractMessage(MessageImplementor imple){
                this.impl = impl;
            }
            
            //委派
            public void sendMessage(String message,String toUser){
                this.impl.send(message,toUser);
            }
        }


        public class CommonMessage extends AbstractMessage {

            public CommonMessage(MessageImplementor impl) {
                super(impl);
            }
            @Override
            public void sendMessage(String message, String toUser) {
                // 对于普通消息，直接调用父类方法，发送消息即可
                super.sendMessage(message, toUser);
            }
        }


        public class UrgencyMessage extends AbstractMessage {

            public UrgencyMessage(MessageImplementor impl) {
                super(impl);
            }
            @Override
            public void sendMessage(String message, String toUser) {
                message = "加急：" + message;
                super.sendMessage(message, toUser);
            }
            /**
            * 扩展自己的新功能，监控某消息的处理状态
            * @param messageId 被监控的消息编号
            * @return 监控到的消息的处理状态
            */
            public Object watch(String messageId) {
                // 根据消息id获取消息的状态，组织成监控的数据对象，然后返回
                return null;
            }
        }

> JDBC桥梁模式

- 桥梁模式在Java应用中的一个非常典型的例子就是JDBC驱动器。JDBC为所有的关系型数据库提供一个通用的界面。一个应用系统动态地选择一个合适的驱动器，然后通过驱动器向数据库引擎发出指令。这个过程就是将抽象角色的行为委派给实现角色的过程。

- 抽象角色可以针对任何数据库引擎发出查询指令，因为抽象角色并不直接与数据库引擎打交道，JDBC驱动器负责这个底层的工作。由于JDBC驱动器的存在，应用系统可以不依赖于数据库引擎的细节而独立地演化；同时数据库引擎也可以独立于应用系统的细节而独立的演化。两个独立的等级结构如下图所示，左边是JDBC API的等级结构，右边是JDBC驱动器的等级结构。应用程序是建立在JDBC API的基础之上的

![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Structural-Type/bridge/jdbc.png)

- JDBC的这种架构，把抽象部分和具体部分分离开来，从而使得抽象部分和具体部分都可以独立地扩展。对于应用程序而言，只要选用不同的驱动，就可以让程序操作不同的数据库，而无需更改应用程序，从而实现在不同的数据库上移植；对于驱动程序而言，为数据库实现不同的驱动程序，并不会影响应用程序
