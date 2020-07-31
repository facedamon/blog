---
title: "[设计模式 | 结构模式 | 适配器模式]"
date: 2019-10-16T15:03:56+08:00
#lastmod: 2019-08-30T01:37:56+08:00
toc: true
draft: false
tags: ["设计模式"]
categories: ["设计模式/结构模式"]
author: "java-my-life"
---

> 转载自https://www.cnblogs.com/java-my-life/

&emsp;&emsp;适配器模式把一个类的接口变换成客户端所期待的另一种接口,从而使原本因接口不匹配而无法工作在一起工作的两个类能够在一起工作。
结构:类适配器模式和对象的适配器模式
类适配器模式 

![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Structural-Type/adapter/架构图.png)

&emsp;&emsp;Adaptee类并没有sampleOperation2()方法,而客户端则期待这个方法。为使客户端能够使用Adaptee类,提供一个中间环节,即类Adapter,把Adaptee的API与Target类的API衔接起来.Adapter与Adaptee是继承关系,这决定了这个适配器模式是类的.

- Target:所期待的接口
- Adapee:现在需要适配的接口
- Adapter:适配器类是本模式的核心.适配器把源接口转换成目标接口.显然,这一角色不可以是接口,而必须是具体类.

        public interface Target{
           //源类Adaptee
           public void simpleOperation1();
           //源类Adaptee没有的方法
           public void simpleOperation2();
        }
        public class Adaptee{
           public void sampleOperation1(){}
        }
        public class Adapter extends Adatee implements Target{
           @Override
           public void sampleOperation2(){
               //TODO
           }
        }


&emsp;&emsp;对象适配器模式
与类的适配器模式一样,对象的适配器模式把被适配的类的APi转换为目标类的API,与类的适配器模式不同的是,对象的适配器模式不是使用继承关系连接Adaptee类,而是使用委派关系连接到Adaptee类.

![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Structural-Type/adapter/对象适配器.png)

&emsp;&emsp;从上图可以看出,Adaptee类并没有sampleOperation2()方法,而客户端期待这个方法.提供一个包装(Wrapper)的Adapter.这个包装类包装了一个Adaptee的实例,从而此包装类能够把Adaptee的API与Target类的APiece衔接起来.


        public interface Target{
           public void sampleOperation1();
           public void sampleOperation2();
        }
        public class Adaptee {
           public void sampleOperation1() {}
        }
        public class Adapter {
           @Autowired
           private Adaptee adaptee;
        ​
           public void sampleOperation1() {
               this.adaptee.sampleOperation1();
           }
        ​
           public void sampleOperation2(){
               //TODO
           }
        }

- 类适配器和对象适配器的权衡
    - 类适配器使用对象继承的方式,是静态的定义方式;而对象适配器使用组合的方式,是动态组合方式.
    - 对于类适配器,由于适配器直接继承了Adaptee,使得适配器不能和Adaptee的子类一起工作，因为继承是静态的关系,当适配器继承了Adaptee后就不可能再去处理Adaptee的子类了.
    - 对于对象适配器,一个适配器可以把多种不同的源适配到同一个目标.换言之,同一个适配器可以把源类和它的子类都适配到目标接口上.因为对象适配器采用组合关系,只要对象类型正确,是不是子类都无所谓.
    - 对于对象适配器,要重定义Adaptee的行为比较困难,这种情况下,需要定义Adaptee的子类来实现重定义,然后让适配器组合子类.虽然重定义Adaptee的行为比较困难,但是想要增加一些新的行为则方便很多,而且新增加的行为可同时适用于所有的源.
- 建议使用对象适配器的实现方式,多用合成/聚合,少用继承.
- 主要解决问题:在软件系统中,常常要将一些"现存的对象"放到新的环境中,而新环境要求的接口是现对象不能满足的.
- 何时使用:
    - 系统需要使用现有的类,而此时类的接口不符合系统的需求
    想要建立一个可以重复使用的类,用于与一些彼此之间没有太大关联的一些类,包括一些可能在竟来引进的类一起工作,这些源类不一定有一致的接口
    通过接口转换,将一个类插入另一个类系中
- 优点:
    - 可以让任何两个没有关联的类一起运行
    - 提交了类的复用
    - 增加了类的透明度
    - 灵活性好
- 缺点:
    - 过多的使用适配器,会让系统非常零乱,不易整体进行把握

> 实现

&emsp;&emsp;我们有一个 MediaPlayer 接口和一个实现了 MediaPlayer 接口的实体类 AudioPlayer。默认情况下，AudioPlayer 可以播放 mp3 格式的音频文件。我们还有另一个接口 AdvancedMediaPlayer 和实现AdvancedMediaPlayer 接口的实体类。该类可以播放vlc和mp4格式的文件。我们想要让 AudioPlayer 播放其他格式的音频文件。为了实现这个功能，我们需要创建一个实现了 MediaPlayer 接口的适配器类 MediaAdapter，并使用AdvancedMediaPlayer对象来播放所需的格式。AudioPlayer 使用适配器类 MediaAdapter 传递所需的音频类型，不需要知道能播放所需格式音频的实际类。AdapterPatternDemo，我们的演示类使用 AudioPlayer 类来播放各种格式.
 
 ![avatar](https://cdn.jsdelivr.net/gh/facedamon/MarkDownPhotos@master/Design-Patterns/Structural-Type/adapter/案例图.png)

 - `AdvancedMediaPlayer.Interface`

        public interface AdvancedMediaPlayer {
            public void playVlc(String fileName);
            public void playMp4(String fileName);
        }

- `MediaPlayer.Interface`

        public interface MediaPlayer {
            public void play (String autoType,String fileName);
        }

- `Mp4Player`

        public class Mp4Player implements AdvancedMediaPlayer {
            @Override
            public void playVlc (String fileName) {
                //NOTHING TODO
            }
            
            @Override
            public void playMp4 (String fileName) {
                log.info("Playing mp4 file name:{}",fileName);
            }
        }

- `VlcPlayer`

        public class VlcPlayer implements AdvancedMediaPlayer {
            @Override
            public void playVlc (String fileName){
                log.info("Playing vlc file name:{}",fileName);
            }
            
            @Override
            public void playMp4 (String fileName){
                //NOTHING TODO
            }
        }

- `MediaAdapter`

        public class MediaAdapter implements MediaPlayer {
            private AdvancedMediaPlayer advancedMusicPlayer;
            
            public MediaAdapter (String autoType) {
                if (autoType.equalsIgnoreCase("vlc")) {
                    advancedMusicPlayer = new VlcPlayer();
                }else if (autoType.equalsIgnoreCase("mp4")){
                    advancedMusicPlayer = new Mp4Player();
                }
            }
            
            @Override
            public void play (String autoType, String fileName) {
                if (autoType.equalsIgnoreCase("vlc")){
                    advancedMusicPlayer.playVlc(fileName);
                }else if (autoType.equalsIgnoreCase("mp4")){
                    advancedMusicPlayer.playMp4(fileName);
                }
            }
        }

- `AudioPlayer`

        public class AudioPlayer implements MediaPlayer {
            private MediaAdapter mediaAdapter;
            
            @Override
            public void play (String audioType,String fileName){
                if (audioType.equalsIgnoreCase("mp3")){
                    log.info("Playing mp3 file name:{}",fileName);
                }else if (audioType.equalsIgnoreCase("vlc")
                    || audioType.equalsIgnoreCase("mp4")){
                    mediaAdapter = new MediaAdapter(audioType);
                    mediaAdapter.play(audioType,fileName);
                }else{
                    log.info("INvalid media {} format not supported",audioType);
                }
            }
        }
