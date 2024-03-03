---
title: Happens-Before规则是什么
category: 文档
tag: 总结
date: 2024-01-04 17:20:00
abbrlink: 4
---
## Happens-Before规则是什么 ?

1. 程序顺序规则：一个线程中的每一个操作，happens-before于该线程中的任意后续操作。

   ```
   double pi = 3.14; // A
   double r = 1.0; // B
   double area = pi * r * r; // C
   ```

   **一个线程中，按照程序顺序，前面的操作 Happens-Before 于后续的任意操作**。这个还是非常好理解的，比如上面那三行代码，第一行的 "double pi = 3.14; " happens-before 于 “double r = 1.0;”，这就是规则1的内容，比较符合单线程里面的逻辑思维，很好理解。

1. 监视器规则：对一个锁的解锁，happens-before于随后对这个锁的加锁。

   这个规则中说的锁其实就是Java里的 synchronized。例如下面的代码，在进入同步块之前，会自动加锁，而在代码块执行完会自动释放锁，加锁以及释放锁都是编译器帮我们实现的。

   ```
   synchronized (this) { //此处自动加锁
     // x是共享变量,初始值=10
     if (this.x < 12) {
       this.x = 12; 
     }  
   } //此处自动解锁
   ```

   所以结合锁规则，可以理解为：假设 x 的初始值是 10，线程 A 执行完代码块后 x 的值会变成 12（执行完自动释放锁），线程 B 进入代码块时，能够看到线程 A 对 x 的写操作，也就是线程 B 能够看到 x==12。这个也是符合我们直觉的，非常好理解。

1. volatile规则：对一个volatile变量的写，happens-before于任意后续对一个volatile变量的读。

1. 传递性：若果A happens-before B，B happens-before C，那么A happens-before C。

   ```
   class VolatileExample {
     int x = 0;
     volatile boolean v = false;
     public void writer() {
       x = 42;
       v = true;
     }
     public void reader() {
       if (v == true) {
         // 这里x会是多少呢？
       }
     }
   }
   ```

   1. “x=42” Happens-Before 写变量 “v=true” ，这是规则 1 的内容；
   2. 写变量“v=true” Happens-Before 读变量 “v=true”，这是规则 3 的内容 。
   3. 再根据这个传递性规则，我们得到结果：“x=42” Happens-Before 读变量“v=true”。这意味着什么呢？

   如果线程 B 读到了“v=true”，那么线程 A 设置的“x=42”对线程 B 是可见的。也就是说，线程 B 能看到 “x == 42” ，有没有一种恍然大悟的感觉？这就是 1.5 版本对 volatile 语义的增强，这个增强意义重大，1.5 版本的并发工具包（java.util.concurrent）就是靠 volatile 语义来搞定可见性的。

1. 线程启动规则：Thread对象的start()方法，happens-before于这个线程的任意后续操作。

1. 线程终止规则：线程中的任意操作，happens-before于该线程的终止监测。我们可以通过Thread.join()方法结束、Thread.isAlive()的返回值等手段检测到线程已经终止执行。

1. 线程中断操作：对线程interrupt()方法的调用，happens-before于被中断线程的代码检测到中断事件的发生，可以通过Thread.interrupted()方法检测到线程是否有中断发生。

1. 对象终结规则：一个对象的初始化完成，happens-before于这个对象的finalize()方法的开始。

**总结**
在 Java 语言里面，Happens-Before 的语义本质上是一种可见性，A Happens-Before B 意味着 A 事件对 B 事件来说是可见的，无论 A 事件和 B 事件是否发生在同一个线程里。例如 A 事件发生在线程 1 上，B 事件发生在线程 2 上，Happens-Before 规则保证线程 2 上也能看到 A 事件的发生。

JMM的设计分为两部分，一部分是面向我们程序员提供的，也就是happens-before规则，它通俗易懂的向我们程序员阐述了一个强内存模型，我们只要理解 happens-before规则，就可以编写并发安全的程序了。 另一部分是针对JVM实现的，为了尽可能少的对编译器和处理器做约束，从而提高性能，JMM在不影响程序执行结果的前提下对其不做要求，即允许优化重排序。 我们只需要关注前者就好了，也就是理解happens-before规则。毕竟我们是做程序员的，术业有专攻，能写出安全的并发程序就好了。