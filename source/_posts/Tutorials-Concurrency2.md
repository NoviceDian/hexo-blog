---
title: The Java™ Tutorials Tutorials--Concurrency(2)
date: 2019-04-11 14:03:35
categories:
- javadoc
tags: 
---


# <center>The Java™ Tutorials Tutorials--Concurrency(2)</center>

## 前言

上周笔记本来是想进行一些阅读后的总结，但是感觉目前还没掌握如果总结的技巧，所以改为将原文比较重点的原文直接复制过来，因为有些觉得翻译的可能不够精确，主题还是需要以英文意思为主去理解，中文为辅，去辅助理解。

以下是本次doc链接：

[The Java™ Tutorials Tutorials--Concurrency](https://docs.oracle.com/javase/tutorial/essential/concurrency/sync.html)


----------

本周的read主要完成的是继上周之后的2节：

- Synchronization 
- Liveness


## Synchronization

Threads communicate primarily by sharing access to fields and the objects reference fields refer to. This form of communication is extremely efficient, but makes two kinds of errors possible: thread interference and memory consistency errors. The tool needed to prevent these errors is synchronization.

多线程之间主要通过共享字段和对象引用的字段进行通信。这是一种有效的通信手段，但是会造成2个问题：

-  线程干扰 thread interference
-  内存一致性 memory consistency
 
synchronization就是解决多线程之间通信问题的主要工具

### Thread Interference线程干扰

文中举了一个多线程执行一个实例字段的自增，C++操作。

在单线程中是一下方式执行：
 
1. Retrieve the current value of c.
2. Increment the retrieved value by 1.
3. Store the incremented value back in c.

在多线程中执行：


1. Thread A: Retrieve c.
2. Thread B: Retrieve c.
3. Thread A: Increment retrieved value; result is 1.
4. Thread B: Decrement retrieved value; result is -1.
5. Thread A: Store result in c; c is now 1.
6. Thread B: Store result in c; c is now -1.

Thread A's result is lost, overwritten by Thread B。
线程A的结果丢失了，被B的结果覆盖了。

### Memory Consistency Errors内存一致性问题

Memory consistency errors occur when different threads have inconsistent views of what should be the same data
当对同一个对象，但在不同线程中拥有不同的值，即内存一致性问题。


从这段话我们可以看出，其实内存一致性问题就是可见性问题。并且在文章中提出了解决方案，即happens-before。

The key to avoiding memory consistency errors is understanding the happens-before relationship.

以下是happens-before relationship的几种场景：

- Each action in a thread happens-before every action in that thread that comes later in the program's order.

&emsp;&emsp;在同一个线程中，保证语义上的前一段代码会先比后一段代码先执行

- An unlock (synchronized block or method exit) of a monitor happens-before every subsequent lock (synchronized block or method entry) of that same monitor. And because the happens-before relation is transitive, all actions of a thread prior to unlocking happen-before all actions subsequent to any thread locking that monitor.

&emsp;&emsp;在同一个监视器对象释放其锁之后，所有上锁的操作都发生在该操作之后。

- A write to a volatile field happens-before every subsequent read of that same field. Writes and reads of volatile fields have similar memory consistency effects as entering and exiting monitors, but do not entail mutual exclusion locking.

&emsp;&emsp;在对volatile修饰的字段上进行写操作，随后的读操作都会发生在该写操作之后，该操作比进入和退出监视器对象更有效，且不需要进行额外的锁操作。

- A call to start on a thread happens-before any action in the started thread.

在启动一个线程钱的操作，必定发生在启动线程中的代码之前。

- All actions in a thread happen-before any other thread successfully returns from a join on that thread.

另一个线程的所有操作，必定发生在本线程join后面的代码之前。

The methods of all classes in java.util.concurrent and its subpackages extend these guarantees to higher-level synchronization

这里还介绍到了JUC所有类以及继承这些类的方法都要保证更高级别的同步。这里就不将这些例子举出来了。


### Synchronized Methods同步方法

JAVA语言提供了2种同步的语法：

- synchronized methods 同步方法

&emsp;&emsp;To make a method synchronized, simply add the synchronized keyword to its declaration.

只需要在方法上加上synchronized关键字，就能简单的让这个方法变成同步方法


**Warning:** When constructing an object that will be shared between threads, be very careful that a reference to the object does not "leak" prematurely. 

这里需要注意的是，在一个对象没有构造完成之前，尽量不要将他过早的暴露出去。造成**对象逃逸**。


- synchronized statements 同步语句

该内容放到下一章节进行讲解。


### Intrinsic Locks and Synchronization 内置锁和同步


- Synchronization is built around an internal entity known as the intrinsic lock or monitor lock

&emsp;&emsp;同步是通过使用对象的内置锁或者监视器的内部锁（API将该对象统称为monitor）。

Intrinsic locks play a role in both aspects of synchronization: enforcing exclusive access to an object's state and establishing happens-before relationships that are essential to visibility.

&emsp;&emsp;内置锁能让对象被访问时是独占的，且能保证 happens-before relationships以达到可见性。


- Every object has an intrinsic lock associated with it. By convention, a thread that needs exclusive and consistent access to an object's fields has to acquire the object's intrinsic lock before accessing them, and then release the intrinsic lock when it's done with them. A thread is said to own the intrinsic lock between the time it has acquired the lock and released the lock. As long as a thread owns an intrinsic lock, no other thread can acquire the same lock. The other thread will block when it attempts to acquire the lock.

&emsp;&emsp;每个对象都有各自的内置锁。线程通过获取同一个对象的锁来进行同步，当一个线程获取了一个对象的内置锁时，其他线程来获取该对象的内置锁时将被阻塞直到持有锁的线程释放锁。


- Locks In Synchronized Methods

 synchronized methods同步方法的实现方式就是在调用该方法时，获取当前对象的内置锁，直到方法完成返回，或者抛出异常。

**在调用一个静态的同步方法时，这时候的监视器对象是类对象并不是实例对象，所以，在调用静态的同步方法和实例的同步方法，并不会形成同步**


- Synchronized Statements 同步语句

&emsp;&emsp;上一节中讲了Synchronized Methods同步方法，而Synchronized Statements同步语句与同步方法的区别在于，同步语句需要自己指定获取哪个对象的内置锁。

&emsp;&emsp;同步语句锁有着更细粒度的同步控制。同步语句由于是自己指定监视器对象，所以在同一个实例的不同方法之间，可以指定不同的监视器对象，以达到更高效的并发，但前提是要确认好同步语句块中对字段的访问确实不需要同步。

- Reentrant Synchronization 可重入同步


### Atomic Access 原子操作

- In programming, an atomic action is one that effectively happens all at once. An atomic action cannot stop in the middle: it either happens completely, or it doesn't happen at all. No side effects of an atomic action are visible until the action is complete.

原子操作：要么全部成功，要么全部失败。

以下2种情况必定是原子操作：

- Reads and writes are atomic for reference variables and for most primitive variables (all types except long and double).

读写原子的引用变量或者原生变量（除了long和double类型）

- Reads and writes are atomic for all variables declared volatile (including long and double variables).

读写所有被volatile修饰的变量（包括long和double）

- 原子操作不会被切断，所以原子操作不会出现线程干扰的问题。但是仍然存在内存一致性的问题。使用volatile 变量能降低内存一致性的问题，因为volatile 变量满足 happens-before relationship的准则。灵活的运用volatile变量来消除同步的锁性能消耗和很好的提高并发的效率。


### 总结和思考

线程间的通信方式就是共享一些引用和变量，但是在多线程共享这些资源时，就会产生2个问题：

1. 线程干扰

&emsp;&emsp;能使用原子操作解决该问题。

2. 内存一致性问题

&emsp;&emsp;内存一致性的问题就是可见性的问题，这类问题不一定需要通过同步解决，解决该问题的方法只要满足happens-before relationship即可。但是线程安全问题不仅仅是内存一致性的问题，还存在线程干扰的问题，只有在确认无线程干扰问题的情况下，可以使用一些非同步的happens-before relationship的解决方案提高并发效率。


----------


- **原子操作**

指该操作要么全部成功，要么全部失败，原子操作的中间是不能插入其他操作的，所以原子操作能有效的避免线程干扰的问题，但是原子操作没办法避免内存一致性（可见性）问题。使用volatile 的变量能解决该问题。

- **同步**

同步是直接有效的解决多线程2个问题的手段。

同步的实现方式是通过指定获取和释放对象的内置锁来实现的。

方式：**Synchronized Statements**&**Synchronized Methods**


## Liveness


A concurrent application's ability to execute in a timely manner is known as its liveness.

这个单词的含义是活性，不是很懂，根据上述定义，解释好像是并发程序的并发能力被称为liveness。感觉有点像并发性能的意思。并且介绍了下面3个问题：

- deadlock（死锁）
- starvation
- livelock

### deadlock

文中举了一段代码示例，在这里就不列出来了，主要造成死锁的原因就就是以下步骤：

1. 线程A获取Object-A的锁
2. 线程B获取Object-B的锁
3. 线程A想获取Object-B的锁等待B线程释放
4. 线程B想获取Object-A的锁等待A线程释放

以上步骤就造成死锁了。

### Starvation

相比于死锁，接下来的Starvation更少见，他发生的场景多见于周期性且耗时长的任务的场景。 For example, suppose an object provides a synchronized method that often takes a long time to return. If one thread invokes this method frequently, other threads that also need frequent synchronized access to the same object will often be blocked.翻译过来其实就是，当一个同步方法需要耗时很长时，有多个线程同时频繁的调用该同步方法（使用相同的监视器对象），大部分线程将被阻塞很长时间。并且因为频繁的调用阻塞的次数会越来越多。然后导致问题。


### Livelock

A thread often acts in response to the action of another thread. If the other thread's action is also a response to the action of another thread, then livelock may result. 

2个线程相互响应，会导致活锁。活锁和死锁的区别就是死锁是等待，而活锁的时候线程是在运行的，一直在改变状态，导致互相无法结束，而造成活锁。

### 总结和思考

其实对于最后2个饥饿和活锁，文章只给了一堆英文，并没有给出例子，导致难以理解。[More about Starvation and Livelock](https://www.codejava.net/java-core/concurrency/understanding-deadlock-livelock-and-starvation-with-code-examples-in-java)这里有更多的对于死锁，饥饿和活锁的解释和例子。







