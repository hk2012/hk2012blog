---
title: volatile的可见性和禁止指令重排序怎么实现的
category: 文档
tag: 总结
date: 2024-01-02 16:10:00
abbrlink: 13
---
## volatile的可见性和禁止指令重排序怎么实现的

《深入理解JAVA虚拟机》中有如下描述：

 “观察加入volatile关键字和没有加入volatile关键字时所生成的汇编代码发现，加入volatile关键字时，会多出一个lock前缀指令”

lock前缀指令实际上相当于一个**内存屏障**（也成内存栅栏），内存屏障会提供3个功能：

1. 它确保指令重排序时不会把其后面的指令排到内存屏障之前的位置，也不会把前面的指令排到内存屏障的后面；即在执行到内存屏障这句指令时，在它前面的操作已经全部完成；
2. 它会强制将对缓存的修改操作立即写入主存；
3. 如果是写操作，它会导致其他CPU中对应的缓存行无效。

所以可见性和禁止指令重排序如下：

- 可见性：
  volatile的功能就是被修饰的变量在被修改后可以立即同步到主内存，被修饰的变量在每次是用之前都从主内存刷新。本质也是通过内存屏障来实现可见性
  写内存屏障（Store Memory Barrier）可以促使处理器将当前store buffer（存储缓存）的值写回主存。读内存屏障（Load Memory Barrier）可以促使处理器处理invalidate queue（失效队列）。进而避免由于Store Buffer和Invalidate Queue的非实时性带来的问题。
- 禁止指令重排序：
  volatile是通过**内存屏障**来禁止指令重排序
  JMM内存屏障的策略
   - 在每个 volatile 写操作的前面插入一个 StoreStore 屏障。
   - 在每个 volatile 写操作的后面插入一个 StoreLoad 屏障。
   - 在每个 volatile 读操作的后面插入一个 LoadLoad 屏障。
   - 在每个 volatile 读操作的后面插入一个 LoadStore 屏障。

典型使用场景DCL：

```java
public class Singleton {
    //volatile是防止指令重排
    private static volatile Singleton singleton;
    private Singleton() {}
    public static Singleton getInstance() {
        //第一层判断singleton是不是为null
        //如果不为null直接返回，这样就不必加锁了
        if (singleton == null) {
            //现在再加锁
            synchronized (Singleton.class) {
                //第二层判断
                //如果A,B两个线程都在synchronized等待
                //A创建完对象之后，B还会再进入，如果不再检查一遍，B又会创建一个对象
                if (singleton == null) {
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }
}
```

