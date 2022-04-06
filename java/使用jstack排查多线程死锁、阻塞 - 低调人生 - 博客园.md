# 使用jstack排查多线程死锁、阻塞 - 低调人生 - 博客园
问题:

　　针对线上多线程死锁、阻塞，跑着跑着就卡住了

　　查看线上线程池的状态

jstack 用于生成 java 虚拟机当前时刻的线程快照。

线程快照是当前 java 虚拟机内每一条线程正在执行的方法堆栈的集合，生成线程快照的主要目的是定位线程出现长时间停顿的原因，如线程间死锁、死循环、请求外部资源导致的长时间等待等。

### jstack:java 堆栈跟踪工具

> jstack 用于生成 java 虚拟机当前时刻的线程快照。
>
> 线程快照是当前 java 虚拟机内每一条线程正在执行的方法堆栈的集合，生成线程快照的主要目的是定位线程出现长时间停顿的原因，如线程间死锁、死循环、请求外部资源导致的长时间等待等。

命令格式：

jstack \[ option ] pid

jstack \[ option ] executable core

jstack \[ option ] \[server-id@]remote-hostname-or-IP

-   执行`jstack`命令，将得到进程的堆栈信息。我一般使用`jstack -l pid`来得到长列表，显示其详细信息。
-   有时线程挂起的时候，需要执行`jstack -F pid`来获取。
-   在实际运行中，往往一次 dump 的信息，还不足以确认问题。建议产生三次 dump 信息，如果每次 dump 都指向同一个问题，我们才确定问题的典型性。
-   堆栈信息只是一种参考，一些正常 RUNNING 的线程，由于复杂网络环境和 IO 的影响，也是有问题的，用 jstack 就无法定位，需要结合对业务代码的理解。

使用：查看服务进程的线程情况

![](https://img2018.cnblogs.com/blog/1182892/201911/1182892-20191101105921235-592903861.png)

**taskExecutor- 为线程词的名称**

**java.lang.Thread.State 线程的状态**

**具体为线程的执行到什么动作 代码行数**

**一个 Thread 对象可以有多个状态，在 java.lang.Thread.State 中，总共定义六种状态：** 

![](https://common.cnblogs.com/images/copycode.gif)

 **1、NEW** 线程刚刚被创建，也就是已经 new 过了，但是还没有调用 start() 方法，jstack 命令不会列出处于此状态的线程信息 **2\*\***、RUNNABLE\*\* #java.lang.Thread.State: RUNNABLE

RUNNABLE 这个名字很具有欺骗性，很容易让人误以为处于这个状态的线程正在运行。事实上，这个状态只是表示，线程是可运行的。我们已经无数次提到过，一个单核 CPU 在同一时刻，只能运行一个线程。 **3、BLOCKED** # java.lang.Thread.State: BLOCKED (on object monitor)

线程处于阻塞状态，正在等待一个 monitor lock。通常情况下，是因为本线程与其他线程公用了一个锁。其他在线程正在使用这个锁进入某个 synchronized 同步方法块或者方法，而本线程进入这个同步代码块也需要这个锁，最终导致本线程处于阻塞状态。 **4\*\***、WAITING\*\*

等待状态，调用以下方法可能会导致一个线程处于等待状态： Object.wait 不指定超时时间 # java.lang.Thread.State: WAITING (on object monitor)

Thread.join with no timeout

LockSupport.park #java.lang.Thread.State: WAITING (parking)

例如：对于 wait() 方法，一个线程处于等待状态，通常是在等待其他线程完成某个操作。本线程调用某个对象的 wait() 方法，其他线程处于完成之后，调用同一个对象的 notify 或者 notifyAll() 方法。Object.wait() 方法只能够在同步代码块中调用。调用了 wait() 方法后，会释放锁。 **5\*\***、TIMED_WAITING\*\*

线程等待指定的时间，对于以下方法的调用，可能会导致线程处于这个状态：

Thread.sleep #java.lang.Thread.State: TIMED_WAITING (sleeping) Object.wait 指定超时时间 #java.lang.Thread.State: TIMED_WAITING (on object monitor)

Thread.join with timeout

LockSupport.parkNanos #java.lang.Thread.State: TIMED_WAITING (parking)

LockSupport.parkUntil #java.lang.Thread.State: TIMED_WAITING (parking) **6\*\***、TERMINATED\*\*

线程终止。

![](https://common.cnblogs.com/images/copycode.gif)

在 dump 文件里，写法可能不太一样：

-   死锁，Deadlock（重点关注）
-   执行中，Runnable
-   等待资源，Waiting on condition（重点关注）
-   等待获取监视器，Waiting on monitor entry（重点关注）
-   对象等待中，Object.wait() 或 TIMED_WAITING
-   暂停，Suspended
-   阻塞，Blocked（重点关注）
-   停止，Parked

### Runnable

> 线程正在运行中。
>
> 一般指该线程正在执行状态中，该线程占用了资源，正在处理某个请求，有可能正在传递 SQL 到数据库执行，有可能在对某个文件操作，有可能进行数据类型等转换。

### Deadlock

1.  "t2" prio=6 tid=0x02bcf000 nid=0xc70 waiting for monitor entry \[0x02f6f000]
2.  java.lang.Thread.State: BLOCKED (on object monitor)
3.  at com.demo.DeadLock$2.run(DeadLock.java:40)
4.  \- waiting to lock &lt;0x22a297a8> (a java.lang.Object)
5.  \- locked &lt;0x22a297b0> (a java.lang.Object)
6.  Locked ownable synchronizers:


8.  "t1" prio=6 tid=0x02bce400 nid=0xba0 waiting for monitor entry \[0x02f1f000]
9.  java.lang.Thread.State: BLOCKED (on object monitor)
10. at com.demo.DeadLock$1.run(DeadLock.java:25)
11. \- waiting to lock &lt;0x22a297b0> (a java.lang.Object)
12. \- locked &lt;0x22a297a8> (a java.lang.Object)
13. Locked ownable synchronizers:

> 上面是一个典型的死锁堆栈，t1 线程 lock 在地址`0x22a297a8`，同时 t2 线程在`waiting to lock`这个地址。
>
> 更有意思的是，堆栈也记录了发生死锁的代码行数，这对我们定位问题起到很大的帮助。

### Wait on condition

等待资源，或等待某个条件的发生。具体原因需结合 stacktrace 来分析。

> 常见情况是该线程在 sleep，等待 sleep 的时间到了时候，将被唤醒。
>
> 关键字：`TIMED_WAITING`,`sleeping`,`parking`。TIMED_WAITING 可能是调用了有超时参数的 wait 所引起的。
>
> parking 指线程处于挂起中。
>
> 下面是一个典型的 sleep 引起的`Wait on condition`。

1.  "thread-1" prio=10 tid=0x00007fbe985cd000 nid=0x7bc6 waiting on condition \[0x00007fbe65848000]
2.  java.lang.Thread.State: TIMED_WAITING (sleeping)
3.  at java.lang.Thread.sleep(Native Method)
4.  at com.xxx.MonitorManager$2.run(MonitorManager.java:124)
5.  at java.util.concurrent.ThreadPoolExecutor$Worker.runTask(ThreadPoolExecutor.java:895)
6.  at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:918)
7.  at java.lang.Thread.run(Thread.java:662)

-   如果发现有大量的线程都在处在 Wait on condition，从线程 stack 看，正等待网络读写，这可能是一个网络瓶颈的征兆。因为网络阻塞导致线程无法执行。一种情况是网络非常忙，几乎消耗了所有的带宽，仍然有大量数据等待网络读 写；另一种情况也可能是网络空闲，但由于路由等问题，导致包无法正常的到达。可以结合其他网络分析工具定位问题。如下面的堆栈，线程等待在 LinkedBlockingQueue 上等待数据的生成。
-   如果堆栈信息明确是应用代码，则证明该线程正在等待资源。一般是大量读取某资源，且该资源采用了资源锁的情况下，线程进入等待状态，等待资源的读取。

1.  "IoWaitThread" prio=6 tid=0x0000000007334800 nid=0x2b3c waiting on condition \[0x000000000893f000]
2.  java.lang.Thread.State: WAITING (parking)
3.  at sun.misc.Unsafe.park(Native Method)
4.  \- parking to wait for &lt;0x00000007d5c45850> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
5.  at java.util.concurrent.locks.LockSupport.park(LockSupport.java:156)
6.  at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(AbstractQueuedSynchronizer.java:1987)
7.  at java.util.concurrent.LinkedBlockingDeque.takeFirst(LinkedBlockingDeque.java:440)
8.  at java.util.concurrent.LinkedBlockingDeque.take(LinkedBlockingDeque.java:629)
9.  at com.xxx.ThreadIoWaitState$IoWaitHandler2.run(ThreadIoWaitState.java:89)
10. at java.lang.Thread.run(Thread.java:662)

### Waiting on monitor entry 和 Object.wait()

> 意味着线程在等待进入一个临界区  
> Monitor 是 Java 中用以实现线程之间的互斥与协作的主要手段，它可以看成是对象或者 Class 的锁。
>
> 每一个对象都有，也仅有一个 monitor。

![](https://img-blog.csdnimg.cn/2019022619592110.png)

-   所有期待获得锁的线程，在锁已经被其它线程拥有的时候，这些期待获得锁的线程就进入了`Object Lock`的`entry set`区域。
-   所有曾经获得过锁，但是由于其它必要条件不满足而需要 wait 的时候，线程就进入了`Object Lock`的`wait set`区域 。
-   在`wait set`区域的线程获得`notify/notifyAll`通知的时候，随机的一个 Thread（notify）或者是全部的 Thread（notifyALL）从 Object Lock 的 wait set 区域进入了 entry set 中。
-   在当前拥有锁的线程释放掉锁的时候，处于该`Object Lock`的`entryset`区域的线程都会抢占该锁，但是只能有任意的一个 Thread 能取得该锁，而其他线程依然在`entry set`中等待下次来抢占到锁之后再执行。

看下面的堆栈，线程在等待数据库连接池返回一个可用的链接

1.  "DB-Processor-13" daemon prio=5 tid=0x003edf98 nid=0xca waiting for monitor entry \[0x000000000825f000]
2.  java.lang.Thread.State: BLOCKED (on object monitor)
3.  at beans.ConnectionPool.getConnection(ConnectionPool.java:102)
4.  \- waiting to lock &lt;0xe0375410> (a beans.ConnectionPool)
5.  at xxx.getTodayCount(ServiceCnt.java:111)
6.  at xxx.ServiceCnt.insertCount(ServiceCnt.java:43)
7.  "DB-Processor-14" daemon prio=5 tid=0x003edf98 nid=0xca waiting for monitor entry \[0x000000000825f020]
8.  java.lang.Thread.State: BLOCKED (on object monitor)
9.  at beans.ConnectionPool.getConnection(ConnectionPool.java:102)
10. \- waiting to lock &lt;0xe0375410> (a beans.ConnectionPool)
11. at xxx.ServiceCnt.getTodayCount(ServiceCnt.java:111)
12. at xxx.ServiceCnt.insertCount(ServiceCnt.java:43)
13. "DB-Processor-3" daemon prio=5 tid=0x00928248 nid=0x8b waiting for monitor entry \[0x000000000825d080]
14. java.lang.Thread.State: RUNNABLE
15. at oracle.jdbc.driver.OracleConnection.isClosed(OracleConnection.java:570)
16. \- waiting to lock &lt;0xe03ba2e0> (a oracle.jdbc.driver.OracleConnection)
17. at beans.ConnectionPool.getConnection(ConnectionPool.java:112)
18. \- locked &lt;0xe0386580> (a java.util.Vector)
19. \- locked &lt;0xe0375410> (a beans.ConnectionPool)
20. at xxx.Cue_1700c.GetNationList(Cue_1700c.java:66)
21. at org.apache.jsp.cue_1700c_jsp.\_jspService(cue_1700c_jsp.java:120)

### Blocked

> 线程阻塞，是指当前线程执行过程中，所需要的资源长时间等待却一直未能获取到，被容器的线程管理器标识为阻塞状态，可以理解为等待资源超时的线程。  
> 如果线程处于 Blocked 状态，但是原因不清楚。可以使用`jstack -m pid`得到线程的 mixed 信息。

1.  \----------------- t@13 -----------------
2.  0xff31e8b8 \_\_\_lwp_cond_wait + 0x4
3.  0xfea8c810 void ObjectMonitor::EnterI(Thread\*) + 0x2b8
4.  0xfeac86b8 void ObjectMonitor::enter2(Thread\*) + 0x250

例如，以上信息表明，线程在尝试进入同步块时阻塞了。 
 [https://www.cnblogs.com/lemon-flm/p/11775893.html](https://www.cnblogs.com/lemon-flm/p/11775893.html)
