
> 转载自https://www.cnblogs.com/java-my-life/

---------------------------------------------

> 概述

给定一个语言后,解释器模式可以定义出其文法的一种表示,并同时提供一种解释,客户端可以使用这个解释器来解释这个语言的句子.

- 结构

![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Behavior-Type/interpreter/架构图.png)

1. `Expression(抽象表达式)`:声明一个所有的具体表达式角色都需要实现的抽象接口.这个接口主要时一个Interpret()方法,乘坐解释操作.
2. `Terminal Expression(终结符表达式)`:实现了抽象表达式角色所要求的接口,主要时一个interpret()方法;文法中的每一个终结符都具有一个具体中介表达式与之对应.比如有一个简单的公式R=R1+R2,再里面R1和R2就是终结符,对应的解析R1和R2的解释器就是终结符表达式.
3. `Nonterminal Expression(非终结符表达式)`:文法中的每一条规则都需要一个具体的非终结符表达式,非终结符表达式一般是文法中的运算符或者其它关键字,比如公式R=R1+R2中,"+"就是非终结符,解析"+"的解释器就是一个非终结符表达式.
4. `Context`:这个角色的任务一般是用来存放文法中各个终结符所对应的具体值,比如R=R1+R2,我们给R1赋值100,给R2赋值200.这些信息需要存放到环境角色中,很多情况下我们使用Map来充当环境角色就够了.
- 为了说明解释器模式的实现方法,这里给出一个最简单的文法和对应的解释器模式的实现,这就是模拟Java语言中对布尔表达式进行操作和求值.在这个语言中终结符是布尔变量.非终结符表达式包含运算符and、or、not等布尔表达式.文法如下:


        Expression ::=Constant|Variable|Or|And|Not
        And ::=Expression'AND'Expression
        Or ::=Expression'OR'Expression
        Not ::=Expression'NOT'Expression
        Variable ::=任何标识符
        Constant ::='true'|'false'



![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Behavior-Type/interpreter/案例图.png)

- 源代码
- 抽象表达式角色

        public abstract class Expression{
            //以context为准,context包含基本boolean的变量
            public abstract boolean interpret(Context ctx);
            //检验两个表达式在结构上是否相同
            public abstract boolean equals(Object obj);
            
            public abstract int hashCode();
            
            public abstract String toString();
        }

- Constant对象代表布尔常量

        public class Constant extends Expression{
            private boolean value;

            public Constant(boolean value){
                this.value = value;
            }
            
            @Override
            public boolean equals(Object obj){
                if (obj != null && obj instanceof Constant){
                    return this.value = ((Constant)obj).value;
                }
                return false;
            }
            
            @Override
            public int hashCode(){
                return this.toString().hashCode();
            }
            
            @Override
            public boolean interpret(Context ctx){
                return value;
            }
            
            @Override
            public String toString(){
                return new Boolean(value).toString();
            }
        }
		
- Variable代表一个有名变量
		
        public class Variable extends Expression{
            private String name;
            
            public Variable(String name){
                this.name = name;
            }
            
            @Override
            public boolean equals(Object obj){
                if (null != obj && obj instanceof Variable){
                    return this.name.equals(((Variable)obj).name);
                }
                return false;
            }
            
            @Override
            public int hashCode(){
                reutrn this.toString().hashCode();
            }
            
            @Override
            public String toString(){
                return name;
            }
            
            @Override
            public boolean interpret(Context ctx){
                return ctx.lookup(this);
            }
        }
		
- 表示由两个布尔表达式通过逻辑and操作给出一个新的布尔表达式
		
        /**
        * @Description: 表示由两个布尔表达式通过逻辑and操作给出一个新的布尔表达式
        * @Author: facedamon
        * @CreateDate: 2019/1/18 17:08
        * @UpdateUser: facedamon
        * @UpdateDate: 2019/1/18 17:08
        * @UpdateRemark: 修改内容
        * @Version: 1.0
        */
        public class And extends Expression {

            private Expression left,right;

            public And(Expression left, Expression right) {
                this.left = left;
                this.right = right;
            }

            @Override
            public boolean interpret(Context ctx) {
                return left.interpret(ctx) && right.interpret(ctx);
            }

            @Override
            public boolean equals(Object obj) {
                if (null != obj && obj instanceof And){
                    return left.equals(((And) obj).left)
                            && right.equals(((And) obj).right);
                }
                return false;
            }

            @Override
            public int hashCode() {
                return this.toString().hashCode();
            }

            @Override
            public String toString() {
                return "(" + left.toString() + " AND " + right.toString() + ")";
            }
        }

		
- 由一个布尔表达式通过逻辑“非”操作给出一个新的布尔表达式的操作
		
        /**
        * @Description: 由一个布尔表达式通过逻辑“非”操作给出一个新的布尔表达式的操作
        * @Author: facedamon
        * @CreateDate: 2019/1/18 17:22
        * @UpdateUser: facedamon
        * @UpdateDate: 2019/1/18 17:22
        * @UpdateRemark: 修改内容
        * @Version: 1.0
        */
        public class Not extends Expression {

            private Expression exp;

            public Not(Expression exp) {
                this.exp = exp;
            }

            @Override
            public boolean interpret(Context ctx) {
                return !exp.interpret(ctx);
            }

            @Override
            public boolean equals(Object obj) {
                if (null != obj && obj instanceof Not){
                    return exp.equals(((Not) obj).exp);
                }
                return false;
            }

            @Override
            public int hashCode() {
                return this.toString().hashCode();
            }

            @Override
            public String toString() {
                return "(Not " + exp.toString() + ")";
            }
        }

		
- 两个布尔表达式通过逻辑或给出一个新的布尔表达式
		
        /**
        * @Description: 两个布尔表达式通过逻辑或给出一个新的布尔表达式
        * @Author: facedamon
        * @CreateDate: 2019/1/18 17:19
        * @UpdateUser: facedamon
        * @UpdateDate: 2019/1/18 17:19
        * @UpdateRemark: 修改内容
        * @Version: 1.0
        */
        public class Or extends Expression {

            private Expression left,right;

            public Or(Expression left, Expression right) {
                this.left = left;
                this.right = right;
            }

            @Override
            public boolean interpret(Context ctx) {
                return left.interpret(ctx) || right.interpret(ctx);
            }

            @Override
            public boolean equals(Object obj) {
                if (null != obj && obj instanceof Or){
                    return left.equals(((Or) obj).left)
                            && right.equals(((Or) obj).right);
                }
                return false;
            }

            @Override
            public int hashCode() {
                return this.toString().hashCode();
            }

            @Override
            public String toString() {
                return "(" + left.toString() + " OR " + right.toString() + ")";
            }
        }
		
- client
		
        public class Client {
            public static void main(String[] args) {
                Context ctx = new Context();
                Variable x = new Variable("x");
                Variable y = new Variable("y");
                Constant c = new Constant(true);
                ctx.assign(x,false);
                ctx.assign(y,true);

                Expression exp = new Or(new And(c,x),new And(y,new Not(x)));

                System.out.println("x=" + x.interpret(ctx));
                System.out.println("y=" + y.interpret(ctx));
                System.out.println(exp.toString() + "=" + exp.interpret(ctx));
            }
        }
		
