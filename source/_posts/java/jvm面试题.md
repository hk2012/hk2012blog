---
title: jvm面试题整理
category: JAVA
tag: 总结
abbrlink: 11059
date: 2023-01-24 15:48:53
---

## JVM底层面试题

1.  说一说JVM 主要组成部分及其作用
    1.1. class loader: 类加载器, 加载类文件到内存，class loader 只管加载，只要符合文件结构就加载，至于能否运行，它不负责（由 execution engine 负责）
    1.2. execution engine: 执行引擎或解释器, 负责解释命令，交由操作系统执行
    1.3. native interface：本地接口, 本地接口的作用是融合不同的语言为java所用。
    1.4. runtimeDate area: 运行数据区,这是jvm的重点，我们所写的程序都被加载到这里，之后才开始运行。
        - 1.4.1. java virtual machine stacks: 栈也叫栈内存，是java程序的运行区，是在线程创建时创建，他的生命周期跟随线程的生命周期，线程结束时，栈内存释放;
        - 1.4.2. 堆内存：一个JVM实例只存在一个堆内存，堆内存的大小是可以调节的
        - 1.4.3. program counter register: 程序计数器 当前线程所执行的字节码的行号指示器，字节码解析器的工作是通过改变这个计数器的值，来选取下一条需要执行的字节码指令，

    1.5. 组件作用：通过class loader 将java代码转换成字节码，（runtime data area）再把字节码加载到内存中，而字节码文件只是JVM的一套指令集规范，并不能直接交给操作系统执行，因此还需要特定命令的执行引擎（execution engine）, 将字节码翻译成底层系统指令，再交由CPU执行，而这个过程中需要调用其他语言的本地库接口（Native Interface）来实现整个程序的功能。
2.  说一下 JVM 运行时数据区
程序计数器（Program Counter Register）：当前线程所执行的字节码的行号指示器，字节码解析器的工作是通过改变这个计数器的值，来选取下一条需要执行的字节码指令，分支、循环、跳转、异常处理、线程恢复等基础功能，都需要依赖这个计数器来完成；
Java 虚拟机栈（Java Virtual Machine Stacks）：用于存储局部变量表、操作数栈、动态链接、方法出口等信息；
本地方法栈（Native Method Stack）：与虚拟机栈的作用是一样的，只不过虚拟机栈是服务 Java 方法的，而本地方法栈是为虚拟机调用 Native 方法服务的；
Java 堆（Java Heap）：Java 虚拟机中内存最大的一块，是被所有线程共享的，几乎所有的对象实例都在这里分配内存；
方法区（Methed Area）：用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译后的代码等数据。

总结下来
- spring是核心，提供了基础功能
- springmvc 是基于spring mvc框架
- springboot 是为了简化spring配置的快速开发整合包
- spring cloud 是构建在 spring boot 之上的服务治理框架