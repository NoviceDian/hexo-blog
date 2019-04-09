---
title: The Java™ Tutorials Tutorials--Concurrency(1)
date: 2019-04-03 17:39:43
categories:
- javadoc
tags: 
---


# <center>The Java™ Tutorials Tutorials--Concurrency(1)</center>


## 前言

这是进行ARST活动的第三周，前2周的read分别看了java各模块的概览，以及基础（有关变量的知识），在前2周的活动中，本着加强阅读英文文档的能力去看了javadoc，但是在做博客总结的时候，觉得都是在做翻译，没有什么重点，虽然对英文阅读的能力有一定的加强，但是感觉学习效果并不是很理想，针对一些知识点。所以本次总结，决定更换一种方式，不在进行文章的翻译，而是对知识点的梳理。


以下是本次doc链接：

[The Java™ Tutorials Tutorials--Concurrency](https://docs.oracle.com/javase/tutorial/essential/concurrency/locksync.html)


----------

由于本次内容偏大，所以分几周完成，本周主要看前2章知识点：

1. Porcesses and Thread
2. Thread Objects

## Porcesses and Thread--进程和线程

### 概述

- 对程序而言，有2种**基本执行单元**：线程和进程。对于java而言，并发的主要手段便是线程。
- 核数越多，并发能力越强，但即使是单核计算机，也是能执行多进程和线程的。
- **单个内核**在任意给定时刻，**只有一个**线程实际执行。
- 时间分片：内核**通过操作系统分配**给线程或进程**处理时间**

### Porcesses

进程主要有以下特点：

- A process has a self-contained execution environment. A process generally has a complete, private set of basic run-time resources; in particular, each process has its own memory space.

&emsp;&emsp;拥有自己的执行环境。特别是每个进程有自己的内存空间。


- Processes are often seen as synonymous with programs or applications. However, what the user sees as a single application may in fact be a set of cooperating processes.

&emsp;&emsp;进程通知被视为一个程序或者应用。但是通常一个应用是由多个进程协作完成的。

- To facilitate communication between processes, most operating systems support Inter Process Communication (IPC) resources, such as pipes and sockets. IPC is used not just for communication between processes on the same system, but processes on different systems.

&emsp;&emsp;大多数操作系统提供IPC资源，比如管道和套接字，让进程间能进行通信而达到协作运行。另外IPC不仅能在进程间通信，还能再不同操作系统间的进程通信。

- Most implementations of the Java virtual machine run as a single process. A Java application can create additional processes using a ProcessBuilder object. Multiprocess applications are beyond the scope of this lesson.

&emsp;&emsp;大多数JAVA虚拟机就是作为一个应用运行的。JAVA应用程序能通过ProcessBuilder对象创建一个额外的进程。

### Threads

- Threads are sometimes called lightweight processes. Both processes and threads provide an execution environment, but creating a new thread requires fewer resources than creating a new process.

&emsp;&emsp;线程有时被称为轻量级进程。进程和线程都会为程序提供一个可执行的环境，但是创建线程消耗的资源要比进程少。

-Threads exist within a process — every process has at least one. Threads share the process's resources, including memory and open files. This makes for efficient, but potentially problematic, communication

&emsp;&emsp;线程存在以个一个进程内（进程至少有一个线程）。同一个进程中的所有线程共享其资源，包括内存资源和打开的文件资源。这让线程更高效的同时也引进了一些问题。

- Multithreaded execution is an essential feature of the Java platform. Every application has at least one thread — or several, if you count "system" threads that do things like memory management and signal handling. But from the application programmer's point of view, you start with just one thread, called the main thread. This thread has the ability to create additional threads, as we'll demonstrate in the next section

在JAVA平台，多线程执行时一个很有必要的特性。作为JAVA应用程序的使用者来说，我们只需要知道，我们是从main线程启动我们的应用，然后通过该线程创建出其他另外的线程的。


### 思考和总结

- 进程是包含很多个线程以及这些线程运行时的环境的。

&emsp;&emsp;个人认为进程的作用主要是为完成一项功能而划分了一定的独立计算机资源单位，为了和其他进程相互没有影响。而线程是组成进程的必要单位，他需要和本进程内的其他线程共享资源，协同操作以达到进程要完成的功能

- 通信方式的区别。
 
&emsp;&emsp;同一个进程间的线程进行协作由于共享该进程的运行环境，所以通信较为方便，而如果是不同进程间的线程进行通信，其实就是所谓的进程间通信IPC，就比较麻烦。

- 线程是计算机执行的最小单位
- 时间分片TimeSlicing

&emsp;&emsp;时间分片是指将可用的CPU时间分配给可用的Runnable线程的过程。（摘自百度）



## Thread Objects 线程对象

### 概述

线程对象，是Thread的实例。在应用中一般有2种使用策略：

-  To directly control thread creation and management, simply instantiate Thread each time the application needs to initiate an asynchronous task.

直接使用Thread类去创建，管理，初始化线程对象。

- To abstract thread management from the rest of your application, pass the application's tasks to an executor.

使用executor对象管理。


### 定义和启动线程

- 定义

&emsp;&emsp;1.实现 Runnable 接口

&emsp;&emsp;2.继承 Thread 类

- 启动

&emsp;&emsp;1.start方法，开启新的线程运行任务---正确的启动方式

&emsp;&emsp;2.run方法，在本线程运行任务




### sleep

- Causes the currently executing thread to sleep 

&emsp;&emsp;使当前线程暂停执行。能空出内核的时间片段让其他线程执行。

- The thread does not lose ownership of any monitors

&emsp;&emsp;线程不会释放持有的锁。

-  these sleep times are not guaranteed to be precise, because they are limited by the facilities provided by the underlying OS

&emsp;&emsp;sleep的时间是没有保证的，线程sleep时间到了之后的执行取决于操作系统。

- the sleep period can be terminated by interrupts

&emsp;&emsp;sleep能被中断。

### Interrupts

- 中断方式：调用线程对象的interrupt方法

- 中断实现方式

&emsp;&emsp;中断实现方式是使用一个中断标志位The Interrupt Status Flag。

**使用中的注意事项：**

- 当调用线程对象的interrupt方法后，会将该标志位置位中断状态。当中断异常抛出后，该状态将被置回为非中断状态。
-  public static boolean interrupted()--->currentThread().isInterrupted(true),会清除标志位。
-   public boolean isInterrupted()--->isInterrupted(false),不会清除标志位。


### Joins

The join method allows one thread to wait for the completion of another.

join方法运行一个线程等待另外一个线程执行完毕。

```
/**
     * Waits at most {@code millis} milliseconds for this thread to
     * die. A timeout of {@code 0} means to wait forever.
     *
     * <p> This implementation uses a loop of {@code this.wait} calls
     * conditioned on {@code this.isAlive}. As a thread terminates the
     * {@code this.notifyAll} method is invoked. It is recommended that
     * applications not use {@code wait}, {@code notify}, or
     * {@code notifyAll} on {@code Thread} instances.
     *
     * @param  millis
     *         the time to wait in milliseconds
     *
     * @throws  IllegalArgumentException
     *          if the value of {@code millis} is negative
     *
     * @throws  InterruptedException
     *          if any thread has interrupted the current thread. The
     *          <i>interrupted status</i> of the current thread is
     *          cleared when this exception is thrown.
     */
```

以上是jdk源码里对join的doc，主要注意的是他的实现方式是通知wait和notify实现的，所以当使用join时，推荐尽量不要使用wait，notify，notifyAll这些方法。


### 思考和总结

本章主要讲的就是线程对象，也就是线程的实例的创建，启动，暂停，中断，和join的使用。这些单个方法意义不是很大，主要是线程间通信需要熟练的使用各种方式，让线程间能高效的通信，这些需要大量的经验和实践，需要在平时注意积累练习。另外就是通过书籍博客，借鉴其他人的用法。