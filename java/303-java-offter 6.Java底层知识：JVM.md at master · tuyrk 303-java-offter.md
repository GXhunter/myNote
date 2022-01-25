# 303-java-offter/6.Java底层知识：JVM.md at master · tuyrk/303-java-offter
Java 思维导图 [![](https://camo.githubusercontent.com/288ccd04a9a65c86a9e0bbdee0e2cd711f53cf0803a72bb3f99bbb01c67337b7/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e39327137666a33313066307233646a342e6a7067)
](https://camo.githubusercontent.com/288ccd04a9a65c86a9e0bbdee0e2cd711f53cf0803a72bb3f99bbb01c67337b7/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e39327137666a33313066307233646a342e6a7067)

谈谈你对 Java 的理解：

-   平台无关性 -- 一次编译到处运行
-   GC -- 垃圾回收机制
-   语言特性 -- 泛型、反射、lambda 表达式
-   面向对象 -- 封装、继承、多态
-   类库 -- 集合、并发库、网络、IO、NIO
-   异常处理

1.  Compile Once，Run AnyWhere 如何实现

    -   编译时 使用 javac 指令编译 java 源码，即将源码编译生成字节码并存入到对应的. class 文件中
    -   运行时

    答：Java 源码首先被编译成字节码，再由不同平台的 JVM 进行解析，Java 语言在不同的平台上运行时不需要进行重新编译，Java 虚拟机在执行字节码的时候，把字节码转换成具体平台上的机器指令 [![](https://camo.githubusercontent.com/cef5c341cea5f32ac4bce79ed6c1afee2134394e7d1803d20328e6fb06d18924/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e34393678696a3330786b3039393075372e6a7067)
    ](https://camo.githubusercontent.com/cef5c341cea5f32ac4bce79ed6c1afee2134394e7d1803d20328e6fb06d18924/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e34393678696a3330786b3039393075372e6a7067)
2.  为什么 JVM 不直接将源码解析成机器码去执行

    -   准备工作：每次执行都需要进行各种检查，如语法校验
    -   兼容性：也可以将别的语言解析成字节码
3.  如何查看. class 字节码：使用 javap -c 指令

Java 虚拟机：JVM 内存结构模型、GC 垃圾回收机制 [![](https://camo.githubusercontent.com/fceaae4e4fccf535a9dea0b188269d04339242b5b2c002d5aa491cae7f3b62be/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e39763933766a33306a78303972676d642e6a7067)
](https://camo.githubusercontent.com/fceaae4e4fccf535a9dea0b188269d04339242b5b2c002d5aa491cae7f3b62be/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e39763933766a33306a78303972676d642e6a7067)

-   Class Loader：依据指定格式，加载 class 文件到内存
-   Execution Engine：对命令进行解析
-   Native Interface：融合不同开发语言的原生库为 Java 所用
-   Runtime Data Area：JVM 内存空间结构模型

JVM 架构主要由 Class Loader、Runtime Data Area、Execution Engine、Native Interface 四部分组成

答：JVM 通过 Class Loader 将符合其格式要求的 class 文件加载到内存里，并通过 Execution Engine 去解析 class 文件里边的字节码，并提交给操作系统去执行。

Java 反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意方法和属性；这种动态获取信息以及动态调用对象方法的功能称为 Java 语言的反射机制

写一个反射的例子：

```java
public class Robot {
    private String name;
    public void sayHi(String helloSentence) {
        System.out.println(helloSentence + " " + name);
    }
    private String throwHello(String tag) {
        return "Hello " + tag;
    }
}
```

```java
public class ReflectSample {
    public static void main(String[] args) throws Exception {
        Class<?> rc = Class.forName("com.tuyrk.reflect.Robot");
        Robot robot = (Robot) rc.newInstance();
        System.out.println("Class name is " + rc.getName());

        //private私有方法
        Method getHello = rc.getDeclaredMethod("throwHello", String.class);//可以获取所有(包括私有方法)自定义的方法，不能获取继承或者实现接口的方法
        getHello.setAccessible(true);//throwHello方法为私有方法，默认为false
        Object str = getHello.invoke(robot, "Bob");
        System.out.println("getHello result is " + str);

        //public公有方法
        Method sayHi = rc.getMethod("sayHi", String.class);//只能获取public方法，但是可以获取继承或接口的方法
        sayHi.invoke(robot, "Welcome");

        //私有属性
        Field name = rc.getDeclaredField("name");
        name.setAccessible(true);
        name.set(robot, "Alice");
        sayHi.invoke(robot, "Welcome");
    }
}
```

1.  类从编译到执行的过程：

    -   编译器将 Robot.java 源文件编译为 Robot.class 字节码文件
    -   ClassLoader 将字节码转换为 JVM 中的 Class 对象
    -   JVM 利用 Class 对象实例化为 Robot 对象
2.  谈谈什么是 ClassLoader

    -   ClassLoader 在 Java 中有着非常重要的作用，它主要工作在 Class 装载的加载阶段，其主要作用是从系统外部获取 Class 二进制数据流。他是 Java 的核心组件，所有的 Class 都是由 ClassLoader 负责通过将 Class 文件里的二进制数据流装载进系统，然后交给 Java 虚拟机进行连接、初始化等操作。
3.  ClassLoader 的种类：

    -   BootStrapClassLoader：C++ 编写，加载核心库 java.\*
    -   ExtClassLoader：Java 编写，加载扩展库 javax.\*
    -   AppClassLoader：Java 编写，下载程序所在目录
    -   自定义 ClassLoader：Java 编写，定制化加载
4.  自定义 ClassLoader 的实现

    关键函数：

    ```
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        throw new ClassNotFoundException(name);
    }

    ```

    ```
    protected final Class<?> defineClass(byte[] b, int off, int len) throws ClassFormatError {
        return defineClass(null, b, off, len, null);
    }

    ```

    ```java
    public class MyClassLoader extends ClassLoader {
        private String path;
        private String classLoaderName;

        public MyClassLoader(String path, String classLoaderName) {
            this.path = path;
            this.classLoaderName = classLoaderName;
        }

        //用于寻找类文件
        @Override
        protected Class<?> findClass(String name) {
            byte[] b = loadClassData(name);
            return defineClass(name, b, 0, b.length);
        }

        //用于加载类文件
        private byte[] loadClassData(String name) {
            name = path + name + ".class";
            InputStream in = null;
            ByteArrayOutputStream out = null;
            try {
                in = new FileInputStream(new File(name));
                out = new ByteArrayOutputStream();
                int b = 0;
                while ((b = in.read()) != -1) {
                    out.write(b);
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                try {
                    out.close();
                    in.close();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return out.toByteArray();
        }
    }
    ```

    ```java
    public class ClassLoaderChecker {
        public static void main(String[] args) throws Exception {
            MyClassLoader m = new MyClassLoader("E:\\java\\imooc_coding\\303-java-offter\\src\\main\\resources\\", "Wali");
            Class c = m.loadClass("Wali");
            System.out.println(c.getClassLoader());
            c.newInstance();
        }
    }
    ```

谈谈类加载器的双亲委派机制： [![](https://camo.githubusercontent.com/e98297012188469a56758937949ecfac886c6956a7400c8798ce569cf8da076d/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e376f3266326a33306c6e3065646d79712e6a7067)
](https://camo.githubusercontent.com/e98297012188469a56758937949ecfac886c6956a7400c8798ce569cf8da076d/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e376f3266326a33306c6e3065646d79712e6a7067) Xbootclasspath 是 java -jar 命令的一个参数

[native 方法实现](http://hg.openjdk.java.net/jdk8u/jdk8u/jdk/file/f0b93fbd8cf8/src/share/native)

为什么要使用双亲委派机制去加载类：

-   避免多份同样字节码的加载，内存是相当宝贵的，没有必要保存两份一样的类对象，即 Class 对象。

类的加载方式：

-   隐式加载：new
-   显式加载：loadClass，formName 等

显式加载当获取到 Class 对象之后需要调用 Class 对象的 newInstance 方法来生成对象的实例，而通过隐式加载则无需调用 Class 对象的 newInstance 方法即可获取对象的实例，并且 new 支持带参数的构造器生成对象实例，而 Class 对象的 newInstance 方法则不支持传入参数，需要通过反射调用构造器的 newInstance 方法才能支持参数。

类的装载过程：

-   加载：通过 ClassLoader 加载 class 文件字节码，生成 Class 对象
-   链接：检验：检查加载的 class 的正确性和安全性，如检查 class 文件的格式是否正确 准备：为类变量分配存储空间并设置类变量初始值，类变量随类型信息存放在方法区中，生命周期很长。 解析：JVM 将常量池内的符号引用转换为支持引用
-   初始化：执行类变量赋值和静态代码块

loadClass 和 forName 的联系： 他们都能够在运行时对任意一个类进行动态加载，都能够知道该类的所有属性和方法，对于任意一个对象都能够调用它的任意方法和属性。

loadClass 和 forName 的区别： Class.forName 得到的 class 是已经初始化完成的，即已经完成装载的整个过程 ClassLoader.loadClass 得到的 class 是还没有链接的，即只完成装载的第一步

```java
public class Robot {
    static {
        System.out.println("Hello Robot");
    }
}
```

```java
public class LoadDifference {
    public static void main(String[] args) throws ClassNotFoundException {
//        ClassLoader cl = Robot.class.getClassLoader();
        Class c = Class.forName("com.tuyrk.reflect.Robot");
    }
}
```

区别产生的作用： 例如程序中需要使用 MySQL 需要加载 driver，这里只能使用 forName，而不能使用 loadClass

有了 forName 装载类，为什么还要使用 loadClass 呢？ 在 Spring IOC 中，在资源加载器获取要读入的资源的时候，即读取 Bean 的配置文件的时候，如果是以 ClassPath 的方式来加载就需要使用 ClassLoader.loadClass 来加载。这是因为 SpringIOC 使用了 lazy loading 延迟加载，Spring ICO 为了加快初始化速度因此大量使用了延迟加载技术，而使用 ClassLoader 不需要执行类中的初始化代码和链接步骤，从而加快了加载速度，把类的初始化工作留到实际使用到这个类的时候再做。

内存简介： [![](https://camo.githubusercontent.com/97829bb75ae3a9068ce4a53469b490213f8d7fee57c67216babc5d886c73a149/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e327a7773306a333076343038356d78772e6a7067)
](https://camo.githubusercontent.com/97829bb75ae3a9068ce4a53469b490213f8d7fee57c67216babc5d886c73a149/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e327a7773306a333076343038356d78772e6a7067) 32 位处理器：2^32 的可寻址范围 64 位处理器：2^64 位可寻址范围

地址空间划分： 内核空间：内核是主要操作系统程序和运行时空间，包含用于连接计算机调度程序以及提供联网，虚拟内存等服务的逻辑和进程 用户空间：Java 进程实际运行时的内存空间

你了解 Java 的内存模型么

JVM 内存模型 - JDK8 [![](https://camo.githubusercontent.com/06544443a69b668fa1f5cb707fd25edd12b571415ec03967a75e033cc5490ef3/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e36367932616a3330763630667a6d7a6d2e6a7067)
](https://camo.githubusercontent.com/06544443a69b668fa1f5cb707fd25edd12b571415ec03967a75e033cc5490ef3/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e36367932616a3330763630667a6d7a6d2e6a7067) 线程私有：程序计数器、虚拟机栈、本地方法栈 线程共享：MetaSpace、Java 堆

程序计数器（Program Counter Register）

-   当前线程所执行的字节码行号指示器（逻辑）
-   改变计数器的值来选取下一条需要执行的字节码指令，包括分支、循环、跳转、异常处理、线程恢复等基础功能
-   和线程是一对一的关系，即 “线程私有”
-   对 Java 方法技术，如果是 Native 方法则计数器值为 Undefined
-   不会发生内存泄漏

程序计数器是逻辑计数器，而非物理计数器；为了线程切换后都能恢复正确的执行位置，每个线程都有一个独立的程序计数器，是线程独立的，只对 Java 方法计数，对 Native 方法则为 Undefined，不会发生内存泄漏。

Java 虚拟机栈（Stack）

-   Java 方法执行的内存模型
-   包含多个栈帧，方法运行期间的基础数据结构，用于存储局部变量表、操作栈、动态链接、返回地址等。

局部变量表和操作数栈

-   局部变量表：包含方法执行过程中的所有变量
-   操作数栈：入栈、出栈、复制、交换、产生消费变量

[![](https://camo.githubusercontent.com/8b7366fe211590496c85a95e3a95a7b4243ee842f7e479fecbd3cf1cb4dbaa5b/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e33666b65356a33307467306562676f702e6a7067)
](https://camo.githubusercontent.com/8b7366fe211590496c85a95e3a95a7b4243ee842f7e479fecbd3cf1cb4dbaa5b/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e33666b65356a33307467306562676f702e6a7067)

递归为什么会引发 java.lang.StackOverflowError 异常？

-   递归过深，栈帧数超过虚拟机栈深度。限制递归次数，使用循环替代。

```java
public class Fibonacci {
    //F(0) = 0, F(1) = 1, 当n >= 2的时候, F(n) = F(n - 1) + F(n - 2),
    //F(2) = F(1) + F(0) = 1, F(3) = F(2) + F(1) = 1 + 1 = 2
    //F(0) - F(N)依次为 0, 1, 1, 2, 3, 5, 8, 13, 21, 34...
    public static int fibonacci(int n) {
        if (n == 0) {return 0;}
        if (n == 1) {return 1;}
        return fibonacci(n - 1) + fibonacci(n - 2);
    }
    public static void main(String[] args) {
        System.out.println(fibonacci(100000));
    }
}
```

虚拟机栈过多会引发 java.lang.OutOfMemoryError 异常

```
public static void stackLeakByThread() {
    while (true) {
        new Thread() {
            public void run() {
                while (true) {
                }
            }
        }.start();
    }
}

```

虚拟机栈也是 Java 虚拟机自动管理的，栈类似于一个集合，但是有固定的容量，是由多个栈帧合起来的。每调用一个方法 Java 虚拟机就会在内存中分配对应的一块空间，这块空间也就是一块栈帧，当方法调用结束后，对应的栈帧就会被自动释放掉。栈的内存不需要通过 GC 去回收，而会自动释放。

本地方法栈：

-   与虚拟机栈相似，主要作用于标注了 native 的方法。

元空间 (MetaSpace) 和永久代 (PermGen) 的区别：

-   元空间使用本地内存，而永久代使用的是 jvm 的内存

MetaSpace 相比 PermGen 的优势：

-   字符串常量池存在于永久代中，容易出现性能问题和内存溢出
-   类和方法的信息大小难以确定，给永久代的大小指定带来困难
-   永久代会为 GC 带来不必要的复杂性，并且回收效率低
-   方便 HotSpot 与其他 JVM 如 Jrockit 的集成

Java 堆（Heap）：

-   对象实例的分配区域
-   GC 管理的主要区域 - 新生代、老年代

JVM 三大性能调优参数 - Xms、-Xmx、-Xss 的含义： java -Xms128m -Xmx128m -Xss256k -jar xxx.jar

-   Xss：规定了每个线程虚拟机栈（堆栈）的大小，256k 足够。影响并发线程数的大小。
-   Xms：堆的初始值，
-   Xmx：堆能扩展达到的最大值，

一般讲 Xms 和 Xmx 设置成一样的，因为当 Heap 不够用发生扩容时会发生内存抖动，从而影响程序运行的稳定性。

Java 内存模型中堆和栈的区别 - 内存分配策略

-   静态存储：编译时确定每个数据目标在运行时的存储空间需求，不允许有可变数据结构、嵌套、递归存在
-   栈式存储：数据区需求在编译时未知，运行时模块入口前确定
-   堆式存储：编译时或运行时模块入口都无法确定，动态分配

Java 内存模型中堆和栈的区别： 联系：引用对象、数组时，栈里定义变量保存堆中目标的首地址 区别： 管理方式：栈自动释放，堆需要 GC 空间大小：栈比堆小 碎片相关：栈产生的碎片远小于堆 分配方式：栈支持动态和静态分配，而堆只支持动态分配 效率：栈的效率比堆高 [![](https://camo.githubusercontent.com/76c81238fa83bb93780885556ea30086d1ef3c85b145af1185c1eacd96bcd11d/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e35373632696a3330667430636d7439622e6a7067)
](https://camo.githubusercontent.com/76c81238fa83bb93780885556ea30086d1ef3c85b145af1185c1eacd96bcd11d/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e35373632696a3330667430636d7439622e6a7067)

元空间、堆、线程独占部分间的联系 - 内存角度

```java
public class HelloWorld {
    private String name;
    public void sayHello() {
        System.out.println("Hello " + name);
    }
    public void setName(String name) {
        this.name = name;
    }
    public static void main(String[] args) {
        int a = 1;
        HelloWorld hw = new HelloWorld();
        hw.setName("test");
        hw.sayHelo();
    }
}
```

.intern() 添加进字符串常量池，JDK6 在永久代引发 OutOfMemoryError 异常，JDK7 + 则不受永久代约束。

```java
/*
VM options：-XX:MaxPermSize=6M -XX:PermSize=6M
 */
public class PermGenErrTest {
    public static void main(String[] args) {
        for (int i = 0; i <= 1000; i++) {
            //将返回的随机字符串添加到字符串常量池中。
            getRandomString(1000000).intern();
        }
        System.out.println("Mission Complete");
    }
    //返回指定长度的随机字符串
    private static String getRandomString(int length) {
        //字符串源
        String str = "qwertyuiopasdfghjklzxcvbnmQWERTYUIOPASDFGHJKLZXCVBNM1234567890";
        Random random = new Random();
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < length; i++) {
            int number = random.nextInt(62);
            sb.append(str.charAt(number));
        }
        return sb.toString();
    }
}
```

```java
public class InternDifference {
    public static void main(String[] args) {
        String s = new String("a");
        s.intern();
        String s2 = "a";
        System.out.println(s == s2);//比较字符串的地址

        String s3 = new String("a") + new String("a");
        s3.intern();
        String s4 = "aa";
        System.out.println(s3 == s4);
    }
}
/*
JDK7+：false true
JDK6：false false
 */
```

不同 JDK 版本之间的 intern() 方法的区别 - JDK6： [![](https://camo.githubusercontent.com/77d78f7d5faca38b780f972fc380f1743342eb6c637f28f74dd90e900c1ccb79/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e373536706e6a3330753630673271346c2e6a7067)
](https://camo.githubusercontent.com/77d78f7d5faca38b780f972fc380f1743342eb6c637f28f74dd90e900c1ccb79/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e373536706e6a3330753630673271346c2e6a7067) 不同 JDK 版本之间的 intern() 方法的区别 - JDK7+： [![](https://camo.githubusercontent.com/86a8692be5d5bab8b15ebcfce0baef98bcdad1078e838c6990c9c10cb78461fe/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e61636d68666a3330766c30656c676e642e6a7067)
](https://camo.githubusercontent.com/86a8692be5d5bab8b15ebcfce0baef98bcdad1078e838c6990c9c10cb78461fe/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e61636d68666a3330766c30656c676e642e6a7067) JDK6 使用 intern() 向字符串常量池添加的是字符串的副本；而 JDK7 + 添加的是字符串的引用

知识点结构图： [![](https://camo.githubusercontent.com/ec6b1beef0d4d9f5fd9938413466f334d3bc5e26b3ae075a40036676a008714c/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e38693938346a333231393064776e30772e6a7067)
](https://camo.githubusercontent.com/ec6b1beef0d4d9f5fd9938413466f334d3bc5e26b3ae075a40036676a008714c/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303038327a7962706779316763316b6e38693938346a333231393064776e30772e6a7067) 
 [https://github.com/tuyrk/303-java-offter/blob/master/doc/6.Java%E5%BA%95%E5%B1%82%E7%9F%A5%E8%AF%86%EF%BC%9AJVM.md](https://github.com/tuyrk/303-java-offter/blob/master/doc/6.Java%E5%BA%95%E5%B1%82%E7%9F%A5%E8%AF%86%EF%BC%9AJVM.md)
