---
title: "[Java (二) | 常量变量运算符]"
date: 2020-11-07T15:49:55+08:00
#lastmod: 2019-08-30T01:37:56+08:00
toc: true
draft: false
tags: ["Java"]
categories: ["Java/学习"]
author: "facedamon"
---

# 常量

- 概述
  - 在程序执行过程中其值不可以发生改变
- Java中常量分类
  - 字面值常量
  - 自定义常量

</br>

    /**
     * 常量：在程序执行的过程中气其值不可以发生改变
     * 
     * 分类：
     *    A：字面值常量
     *       1， 2.5
     *    B：自定义常量(面向对象部分讲解)
     * 
     * 字面值常量分类：
     *     A：字符串常量，用""括起来的内容
     *     B：整数常量，所有的整数数据
     *     C：小数常量，所有带小数的数据
     *     D：字符常量，用单引号括起来的内容
     *     E：布尔常量，只有两个值：true/false
     *     F: 空常量 null
    */
    class ConstantDemo {
        public static void main(String[] args) {
            //字符串常量
            System.out.println("HelloWorld");

            //整数常量
            System.out.println(100);

            //小数常量
            System.out.println(12.345);

            //字符常量
            System.out.println('A');

            //布尔常量
            System.out.println(false);
        }
    }

## 进制转换

- Java针对整数常量提供了4中表现形式
  - 二进制
  - 八进制
  - 十进制
  - 十六进制
- 不同进制的数据组成
  - 二进制
    - 由0，1组成，以0b开头
  - 八进制
    - 由0,1,...7组成。以0开头
  - 十进制
    - 由0,1,...9组成。整数默认是十进制的
  - 十六进制
    - 由0,1,...9,a,b,c,d,e,f。以0x开头

![avatar](https://cdn.jsdelivr.net/gh/facedamon/markdownps2@master/java/进制图解.png)

    class ScaleDemo {
        public static void main(String[] args)
        //二进制
        //1*2^2+0*2^1+0*2^0=4
        System.out.println(0b100);//4
        //八进制
        //1*8^2+0*8^1+0*8^0=64
        System.out.println(0100);//64
        //十进制
        //1*10^2+0*10^1+0*8^0=100
        System.out.println(100);//100
        //十六进制
        //1*16^2+0*16^1+0*16^0=256
        System.out.println(ox100);//256
    }

# 变量

- 概述
  - 在程序执行的过程中，在某个范围内其值可以发生改变的量
  - 如同数学中的未知数X
- 变量定义格式
  - 数据类型 变量名 = 初始化值;
  - 注意：格式是固定的，记住格式，以不变应万变

# 数据类型

&emsp;&emsp;Java语言是强类型语言，对于每一种数据都定义了明确的具体数据类型，在内存中分配了不同大小的空间。

![avatar](https://cdn.jsdelivr.net/gh/facedamon/markdownps2@master/java/数据类型.png)

    /**
     * 熟悉数组
    */
    class Array2Sum {
        public static void main(String[] args) {
            int[][] arr = {{22,66,44},{77,33,88},{25,45,65},{11,66,99}};
            int sum = 0;
            for (int i = 0; i < arr.length; i++) {
                for (int j = 0; j < arr[i].length; j++) {
                    sum += arr[i][j];
                }
            }
            System.out.println("总数为：" + sum);
        }
    }

> 学会计算数据类型占用存储空间大小

|类型|占用存储空间|数值范围|
|------|------|------|
|byte|1个字节(一个字节=8位)|-2^7~2^7-1(包含了最高位的符号位) -128～127|
|short|2个字节|-2^15~2^15-1|
|int|4个字节|-2^31~2^31-1|
|long|8个字节|-2^63~2^63-1|
|float|4个字节|-3.403E38~3.403E38|
|double|8个字节|-1.798E308~1.798E308|

- 使用变量注意事项
  - 作用域
    - 变量定义在哪一级大括号中，哪个打括号的范围就是这个变量的作用域。相同的作用域中不能定义两个同名变量。
  - 初始化值
    - 没有初始化值不能直接使用
  - 在一行上建议只定义一个变量
    - 可以定义多个，但是不建议
  - boolean类型不能转换为其他的数据类型
  - 默认转换
    - byte,short,char----int----log----float----double
    - byte,short,char相互之间不转换，它们参与运算首先转换为int类型
  - 强制转换
    - 目的类型 变量名=(目标类型)(被转换的数据);

</br>

    /**
     * 问题：
     *  Java中字符可以存储一个汉字吗？
     *  因为Java语言采用unicode编码，而unicode编码的每个字符是两个字节，那java中的字符足以存储一个汉字
     *  
     * 注意：
     *   整数默认是int类型
     *   浮点数默认是double
     *   long类型要加L
     *   float类型要加F
     *   
    */
    class DataType {
        public static void main(String[] args) {
            //定义byte类型的变量
            byte b = 1;
            System.out.println(1);
            System.out.println(b);

            //定义short类型的变量
            short s = 100;
            System.out.println(s);

            //定义int类型
            int i = 100000;
            System.out.println(i);

            //定义long类型
            long l = 2147483648L;
            System.out.println(l);

            //定义float类型
            float f = 12.34f;
            System.out.println(f);

            //定义double类型
            double d = 23.56;
            System.out.println(d);

            //定义char类型
            char ch = 'a';
            System.out.println(ch);

            //定义boolean类型
            boolean flag = true;
            System.out.println(flag);
        }
    }

# 类型转换

    /**
     * +是一个运算符，我们应该能够看懂，做数据的加法。
     * 参与运算的数据，要求类型一致。
     * boolean类型不能转换为其它的数据类型。
     * 隐式转换：
     *     A: byte, short, char-->int-->long-->float-->double
     *     B: byte, short, char相互之间不能转换，它们参与运算首先转换为int类型
    */
    class DataTypeDemo {
      public static void main(String[] args) {
        //类型一样的数据
        int a = 10;
        int b = 20;
        System.out.println(a + b);
        int c = a + b;
        System.out.println(c);
        System.out.println("---------");

        //定义变量
        byte by = 3;
        int i = 4;
        System.out.println(by + i);
        int j = by + i;
        System.out.println(j);
      }
    }

> 思考?

- byte b1=3, b2=4, b;
- b=b1+b2;
- b=3+4;
- 哪一句会编译失败？为什么？

</br>

    /**
     * 强制转换： 从大到小，不建议使用，因为可能有精度的丢失
    */
    class DataTypeDemo2 {
      public static void main(String[] args) {
        byte by = 3;
        int i = 4;
        byte bb = (byte)(by + i);
        System.out.println(bb);
      }
    }

# 运算符

- 算术运算符
- 赋值运算符
- 比较运算符逻辑运算符
- 位运算符
- 三目运算符

## 算术运算符

|运算符|运算|范例|结果|
|------|------|------|------|
|+|正号|+3|3|
|-,+|负号,加号|b=4;-b;5+5|-4;10|
|-,*|减,乘|6-4,3*4|2,12|
|/,%|除,取模|5/5,5%5|1,0|
|++,++|自增前/后|a=2;b=++a;a=2;b=a++|a=3;b=3;a=3;b=2|
|--,--|自减前/后|a=2;b=--a;a=2;b=a--|a=1;b=1;a=1;b=2|
|+|字符串相加|"He"+"llo"|"Hello"|

## 赋值运算符

- 符号
  - =，+=，-=，*=，/=，%=
  - =为基本的赋值运算符，其它的为扩展的赋值运算符
- 如下操作会怎么样?
  - int a,b; a = b = 10;
  - sout(a);sout(b);
  - int a = 10; a += 20; sout(a);
- 下面代码有没有问题?
  - short s = 1; s = s+1; short s = 1; s += 1;

## 比较运算符

|运算符|范例|结果|
|---|---|---|
|==|4==3|false|
|!=|4!=3|true|
|<|4<3|false|
|>|4>3|true|
|<=|4<=3|false|
|>=|4>=3|false|
|instanceof|"Hello" instanceof String|true|

## 逻辑运算符

|运算符|运算|范例|结果|
|---|---|---|---|
|&|and(短路与)|false&true|false|
|\||or(短路或)|false\|true|true|
|^|异或|true^false|true|
|!|not|!true|false|
|&&|and(逻辑与)|false&&true|false|
|\|\||or(逻辑或)|false\|\|true|true|

- 逻辑运算符用于连接布尔型表达式
- '&' 和 '&&'的区别
  - 单'&'时，属于'短路与'，左边无论真假，右边都进行运算
  - 双'&&'时，属于'逻辑与'，如果左边为真，右边参与运算，如果左边为假，那么右边不参与运算

## 位运算

|运算符|运算|范例|逻辑|
|---|---|---|---|
|<<|左移|3<<2 -> 3*2*2=12|空位补0，被移除的高位丢弃｜
|>>|右移|-5>>1=-3(牵扯原码补码计算)|被移位的二进制最高位是0，右移后，空缺位补0；最高位是1，最高位补1|
|>>>|无符号右移|-5>>>1=2147483645|被移位二进制最高位无论是0或者是1，空缺位都用0补|
|&|与运算|6&3=2|任何二进制位和0进行&运算，结果是0；和1进行&运算结果是原值|
|\||或运算|6|3=7|任何二进制位和0或运算，结果是原值，和1进行或运算结果是1|
|^|异或运算|6^3=5|相同为假，相异为真|
|~|反码|～6=-7|

## 三目运算符

- 格式
  - (关系表达式)?表达式1:表达式2
  - 如果条件为true, 运算后的结果是表达式1
  - 如果条件为false，运算后的结果是表达式2
- 示例
  - 获取两个数中最大的数
  - z=(x>y)?x:y

# 键盘录入数据

    /**
     * 为了程序的数据更加的灵活，我们决定加入键盘录入数据
     * 如何使用键盘录入数据呢？
     * A:导包
     *      import java.util.Scanner;
     * B:创建对象
     *      Scanner sc = new Scanner(System.in);
     * C:获取数据
     *      int i = sc.nextInt();
    */

    import iava.util.Scanner;

    class OperatorDemo {
        public static void main(String[] args) {
          //创建键盘录入对象
          Scanner sc = new Scanner(System.in);
          System.out.println("请输入一个整数：");
          //获取数据
          int i = sc.nextInt();
          System.out.println("i:"+i);
        }
    }

</br>


    /**
     * 需求：键盘录入两个数据，获取这两个数据中的最大值
    */
    import java.util.Scanner;

    class OperatorTest {
      public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入第一个数据: ");
        int a = sc.nextInt();
        System.out.println("请输入第二个数据: ");
        int b = sc.nextInt();

        // 获取数据最大值
        int max = (a>b)?a:b;

        System.out.println("最大值是："+max);
      }
    }