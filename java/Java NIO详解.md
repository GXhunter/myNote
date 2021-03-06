# Java NIO详解
[![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/16892c66-b77c-497c-b286-d2d238e30f0c.webp?raw=true)
](https://www.jianshu.com/u/88c706ace84a)

0.0662017.07.04 22:29:05 字数 1,629 阅读 4,720

## NIO 含义

New I/O，原因在于它相对于之前的 I/O 类库是新增的。  
由于之前老的 I/O 类库是阻塞 I/O，New I/O 类库的目标就是要让 Java 支持非阻塞 I/O，所以，更多的人喜欢称之为非阻塞 I/O（Non-block I/O）。

## SocketChannel 和 ServerSocketChannel

与 Socket 类和 ServerSocket 类相对应，NIO 也提供了 SocketChannel 和 ServerSocketChannel 两种不同的套接字通道实现。这两种新增的通道都支持阻塞和非阻塞两种模式。  
低负载、低并发的应用程序可以选择同步阻塞 I/O 以降低编程复杂度；对于高负载、高并发的网络应用，需要使用 NIO 的非阻塞模式进行开发。

## NIO 类库简介

NIO 弥补了原来同步阻塞 I/O 的不足，它在标准 Java 代码中提供了高速的、面向块的 I/O。

通过定义包含数据的类，以及通过以块的形式处理这些数据，NIO 不用使用本机代码就可以利用低级优化，这是原来的 I/O 包所无法做到的。

## NIO 类库简介 缓冲区 Buffer

Buffer 是一个对象，它包含一些要写入或者要读出的数据。  
在 NIO 类库中加入 Buffer 对象，体现了新库与原 I/O 的一个重要区别。  
在面向流的 I/O 中，可以将数据直接写入或者将数据直接读到 Stream 对象中。  
在 NIO 库中，所有数据都是用缓冲区处理的。在读取数据时，它是直接读到缓冲区中的；在写入数据时，写入到缓冲区中。任何时候访问 NIO 中的数据，都是通过缓冲区进行操作。  
缓冲区实质上是一个数组。  
缓冲区不仅仅是一个数组，缓冲区提供了对数据的结构化访问以及维护读写位置（limit）等信息。  
一个 ByteBuffer 提供了一组功能用于操作 byte 数组。  
每一种 Java 基本类型（除了 Boolean 类型）都对应有一种缓冲区：  
ByteBuffer：字节缓冲区  
CharBuffer：字符缓冲区  
ShortBuffer：短整型缓冲区  
IntBuffer：整形缓冲区  
LongBuffer：长整形缓冲区  
FloatBuffer：浮点型缓冲区  
DoubleBuffer：双精度浮点型缓冲区

![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/6a3a2b75-72ee-45d1-b1b5-3822b5ca9ad5.png?raw=true)

每一个 Buffer 类都是 Buffer 接口的一个子实例。除了 ByteBuffer，每一个 Buffer 类都有完全一样的操作，只是它们所处理的数据类型不一样。  
因为大多数标准 I/O 操作都使用 ByteBuffer，所以它在具有一般缓冲区的操作之外还提供了一些特有的操作，以方便网络读写。

## NIO 类库简介 通道 Channel

Channel 是一个通道，网络数据通过 Channel 读取和写入。  
通道与流的不同之处在于通道是双向的，流只是在一个方向上移动（一个流必须是 InputStream 或者 OutputStream 的子类），而通道可以用于读、写或者二者同时进行。  
因为 Channel 是全双工的，所以它可以比流更好地映射底层操作系统的 API。特别是在 UNIX 网络编程模型中，底层操作系统的通道都是全双工的，同时支持读写操作。

![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/dbc5526e-87a3-4e09-9e5c-36fa88b9634a.png?raw=true)

自顶向下看，前三层主要是 Channel 接口，用于定义它的功能，后面是一些具体的功能类（抽象类）。从类图可以看出，实际上 Channel 可以分为两大类：用于网络读写的 SelectableChannel 和用于文件操作的 FileChannel。  
ServerSocketChannel 和 SocketChannel 都是 SelectableChannel 的子类

## 多路复用器 Selector

多路复用器 Selector 是 Java NIO 编程的基础  
多路复用器提供选择已经就绪的任务的能力。  
Selector 会不断地轮询注册在其上的 Channel，如果某个 Channel 上面发生读或者写事件，这个 Channnel 就处于就绪状态，会被 Selector 轮询出来，然后通过 SelectionKey 可以获取就绪 Channel 的集合，进行后续的 I/O 操作。  
一个多路复用器 Selector 可以同时轮询多个 Channel，由于 JDK 使用了 epoll() 代替传统的 select 实现，所以它并没有最大连接句柄 1024/2048 的限制。这也就意味着只需要一个线程负责 Selector 的轮询，就可以接入成千上万的客户端，这确实是个非常巨大的进步。

![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/924952f5-40e9-46cb-afb7-31c81619f9b8.png?raw=true)

下面用代码简单描述这个过程：

![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/4b743dee-f1a4-4bcc-ad5a-327e142ca71d.png?raw=true)

## 使用 NIO 技术编写 TimeServer

![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/6bdff314-2a9c-4495-b7bc-46568c0858cd.png?raw=true)

![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/decd5b26-455b-4662-a5e3-020e9994f969.png?raw=true)

## 使用 NIO 技术编写 TimeClient 原理讲解

时序图：

![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/8c9a2649-e8db-4280-a8eb-d8237abe4f7a.png?raw=true)

用代码讲解一下过程：

![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/b7542397-68a8-4c06-9e1e-4290a7859675.png?raw=true)

## 使用 NIO 技术编写 TimeClient

![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/9de8f8bf-9545-4f28-820f-aa379238b3e0.png?raw=true)

![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/8c8a3c97-0e8c-4ba2-a673-e6365e422479.png?raw=true)

## 注意

下面这种关闭 socket 的方法是错误的

![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/1c5f8fbd-b93b-4939-9257-639e0a93b480.png?raw=true)

如果服务端没有对这种情况进行处理会出现一下的错误：

![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/90ee7d6e-fd75-4d26-9c2b-244bd8ab3e28.png?raw=true)

服务端最好是在读取数据的时候做一下处理：

![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/092589bf-e46a-481b-be83-c7e61628d257.png?raw=true)

## 优势

NIO 编程的难度比同步阻塞 BIO 大很多。  
请注意以上的代码中并没有考虑 “半包读” 和“半包写”，如果加上这些，代码将会更加复杂。。  
（1）客户端发起的连接操作是异步的，可以通过在多路复用器注册 OP_CONNECT 等待后续结果，不需要像之前的客户端那样被同步阻塞。  
（2）SocketChannel 的读写操作都是异步的，如果没有可读写的数据它不会同步等待，直接返回，这样 I/O 通信线程就可以处理其他的链路，不需要同步等待这个链路可用。  
（3）线程模型的优化：由于 JDK 的 Selector 在 Linux 等主流操作系统上通过 epoll 实现，它没有连接句柄数的限制（只受限于操作系统的最大句柄数或者对单个进程的句柄限制），这意味着一个 Selector 线程可以同时处理成千上万个客户端连接，而且性能不会随着客户端的增加而线性下降。因此，它非常适合做高性能、高负载的网络服务器。

更多精彩内容，就在简书 APP

"小礼物走一走，来简书关注我"

还没有人赞赏，支持一下

[![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/0b90b76f-b87d-422e-b084-7c21c334a241.webp?raw=true)
](https://www.jianshu.com/u/88c706ace84a)

[每天学点编程](https://www.jianshu.com/u/88c706ace84a "每天学点编程")主要发布关于 java 编程方面的内容，从 java 的基础语法到高级的并发、NIO 等，也会讲讲 jav...

总资产 2 共写了 9.7W 字获得 181 个赞共 290 个粉丝

### 被以下专题收入，发现更多相似内容

### 推荐阅读[更多精彩内容](https://www.jianshu.com/)

-   转自 [http://www.ibm.com/developerworks/cn/education/java/j-..](http://www.ibm.com/developerworks/cn/education/java/j-..).

    [![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/29513473-5a80-4f01-bb22-9d6ddb1251de.webp?raw=true)
    抓兔子的猫](https://www.jianshu.com/u/dd411031d270)阅读 1,863 评论 0 赞 22

-   概述 NIO 主要有三大核心部分：Channel(通道)，Buffer(缓冲区),Selector。 传统 IO 基于...

    [![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/5e4334ac-9fb1-4ae4-966c-8041bfd305e2.webp?raw=true)
    时之令](https://www.jianshu.com/u/445787b39371)阅读 3,240 评论 0 赞 8

    [![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/1fb0d424-134b-4f72-91ea-5b93cc4b90a3.webp?raw=true)
    ](https://www.jianshu.com/p/88ec3430ae8b)

-   这两天了解了一下关于 NIO 方面的知识，网上关于这一块的介绍只是介绍了一下基本用法，没有系统的解释 NIO 与阻塞、非阻...

    [![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/62c9e36f-8979-497e-8758-9cfc5d6b813d.webp?raw=true)
    ](https://www.jianshu.com/p/a9b2fec31fd1)

-   Java NIO（New IO）是从 Java 1.4 版本开始引入的一个新的 IO API，可以替代标准的 Java I...

    [![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/95a0d6cf-da7b-4892-94b7-3d8fb211188c.webp?raw=true)
    ](https://www.jianshu.com/p/919a7555991a)

-   博为峰小博老师： \[JVM 利弊] JVM 是把双刃剑。它提供了统一的操作环境, 让 Java 程序员不用再为操作系统...

    [![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/32d5b793-9834-477f-b887-899194624eea.webp?raw=true)
    ](https://www.jianshu.com/p/d2f637601521)

-   方法一： \[\[\[self.navigationController.navigationBar subviews]...


-   2015 年 5 月 4 日，我和家人来到江南水乡——周庄一游，夜宿镇里的金钩客栈。次日早起，独自一人走出客栈，沿街漫步，感...

    [![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/c92de8b2-4365-4614-a137-097882a6d252.webp?raw=true)
    ](https://www.jianshu.com/p/c26c3c11b7bc)
-   【捭阖第一】 （1.8）捭之者，开也，言也，阳也；阖之者，闭也，默也，阴也。阴阳其和，终始其义。故言长生，安乐，富...
-   当下的大都市中，单身一族的人越来越多，一小部分人主动单身，他们集美貌与才华一身，他们财务自由，他们独立自主，所以要...
       [![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/ff744aba-d3f7-4944-982b-6f6b6526f595.jpeg?raw=true)
       醒醒老师](https://www.jianshu.com/u/e01bb040ae61)阅读 135 评论 0 赞 0
       [![](https://github.com/GXhunter/myNote/blob/main/2022-3-6%2015-07-08/1685c862-6ca3-438d-88e9-ca97e076c178.webp?raw=true)
       ](https://www.jianshu.com/p/03ff1d690be2) 
    [https://www.jianshu.com/p/5442b04ccff8](https://www.jianshu.com/p/5442b04ccff8)
