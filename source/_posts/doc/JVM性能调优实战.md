---
title: JVM性能调优实战
category: 文档
tag: 总结
date: 2024-02-12 16:00:00
abbrlink: 7
---
# JVM性能调优实战

### 调优的一些原则

1. 多数的Java应用不需要在服务器上进行GC优化，虚拟机内部已有很多优化来保证应用的稳定运行，所以不要为了调优而调优，不当的调优可能适得其反
1. 在应用上线之前，先考虑将机器的JVM参数设置到最优（适合）
1. 在进行GC优化之前，需要确认项目的架构和代码等已经没有优化空间。我们不能指望一个系统架构有缺陷或者代码层次优化没有穷尽的应用，通过GC优化令其性能达到一个质的飞跃
1. GC优化是一个系统而复杂的工作，没有万能的调优策略可以满足所有的性能指标。GC优化必须建立在我们深入理解各种垃圾回收器的基础上，才能有事半功倍的效果
1. 处理吞吐量和延迟问题时，垃圾处理器能使用的内存越大，即java堆空间越大垃圾收集效果越好，应用运行也越流畅。这称之为GC内存最大化原则
1. 在这三个属性（吞吐量、延迟、内存）中选择其中两个进行jvm调优，称之为GC调优3选2



### 什么情况下需要调优


- Heap内存（老年代）持续上涨达到设置的最大内存值
- Full GC 次数频繁
- GC 停顿（Stop World）时间过长（超过1秒，具体值按应用场景而定）
- 应用出现OutOfMemory 等内存异常
- 应用出现OutOfDirectMemoryError等内存异常（ failed to allocate 16777216 byte(s) of direct memory (used: 1056964615, max: 1073741824)）
- 应用中有使用本地缓存且占用大量内存空间
- 系统吞吐量与响应性能不高或下降
- 应用的CPU占用过高不下或内存占用过高不下



### 调优前需知的一些概念

1. **吞吐量：**用户代码时间 / （用户代码执行时间 + 垃圾回收时间）。是评价垃圾收集器能力的重要指标之一，是不考虑垃圾收集引起的停顿时间或内存消耗，垃圾收集器能支撑应用程序达到的最高性能指标。吞吐量越高算法越好。
2. **低延迟：**STW越短，响应时间越好。评价垃圾收集器能力的重要指标，度量标准是缩短由于垃圾收集引起的停顿时间或完全消除因垃圾收集所引起的停顿，避免应用程序运行时发生抖动。暂停时间越短算法越好
3. 在设计（或使用）GC 算法时，我们必须确定我们的目标：一个 GC 算法只可能针对两个目标之一（即只专注于最大吞吐量或最小暂停时间），或尝试找到一个二者的折中
4. MinorGC尽可能多的收集垃圾对象。我们把这个称作MinorGC原则，遵守这一原则可以降低应用程序FullGC 的发生频率。FullGC 较耗时，是应用程序无法达到延迟要求或吞吐量的罪魁祸首
5. 堆大小调整的着手点、分析点：
   1. 统计Minor GC 持续时间
   1. 统计Minor GC 的次数
   1. 统计Full GC的最长持续时间
   1. 统计最差情况下Full GC频率
   1. 统计GC持续时间和频率对优化堆的大小是主要着手点
   1. 我们按照业务系统对延迟和吞吐量的需求，在按照这些分析我们可以进行各个区大小的调整
6. 一般来说吞吐量优先的垃圾回收器：-XX:+UseParallelGC  -XX:+UseParallelOldGC，即常规的（PS/PO）
7. 响应时间优先的垃圾回收器：CMS、G1

### JVM常用参数解读

1. **Xms** 是指设定程序启动时占用内存大小。一般来讲，大点，程序会启动的快一点，但是也可能会导致机器暂时变慢
2. **Xmx** 是指设定程序运行期间最大可占用的内存大小。如果程序运行需要占用更多的内存，超出了这个设置值，就会抛出OutOfMemory异常
3. **Xss** 是指设定每个线程的堆栈大小。这个就要依据你的程序，看一个线程大约需要占用多少内存，可能会有多少线程同时运行等
4. **-Xmn、-XX:NewSize/-XX:MaxNewSize、-XX:NewRatio **
   1. 高优先级：-XX:NewSize/-XX:MaxNewSize 
   1. 中优先级：-Xmn（默认等效 -Xmn=-XX:NewSize=-XX:MaxNewSize=?） 
   1. 低优先级：-XX:NewRatio 
5. 如果想在日志中追踪类加载与类卸载的情况，可以使用启动参数  **-XX:TraceClassLoading -XX:TraceClassUnloading **



### 常用性能调优工具

1. MAT
   - 提示可能的内存泄露的点
2. jvisualvm
3. jconsole
4. Arthas
5. show-busy-java-threads
   1. [https://github.com/oldratlee/useful-scripts/blob/master/docs/java.md#-show-busy-java-threads](https://github.com/oldratlee/useful-scripts/blob/master/docs/java.md#-show-busy-java-threads)​
      #### 

### 线上排查问题的一般流程

1. CPU占用过高排查流程
   1. 利用 top 命令可以查出占 CPU 最高的的进程pid ，如果pid为 9876
   1. 然后查看该进程下占用最高的线程id【top -Hp 9876】
   1. 假设占用率最高的线程 ID 为 6900，将其转换为 16 进制形式 (因为 java native 线程以 16 进制形式输出) 【printf '%x\n' 6900】
   1. 利用 jstack 打印出 java 线程调用栈信息【jstack 9876 | grep '0x1af4' -A 50 --color】，这样就可以更好定位问题
2. 内存占用过高排查流程
   1. 查找进程id: 【top -d 2 -c】
   1. 查看JVM堆内存分配情况：jmap -heap pid
   1. 查看占用内存比较多的对象 jmap -histo pid | head -n 100
   1. 查看占用内存比较多的存活对象 jmap -histo:live pid | head -n 100





### OOM内存泄露分析

### **什么情况下，会抛出OOM呢？**

- JVM98%的时间都花费在内存回收
- 每次回收的内存小于2%

满足这两个条件将触发OutOfMemoryException，这将会留给系统一个微小的间隙以做一些Down之前的操作，比如手动打印Heap Dump。并不是内存被耗空的时候才抛出
**​**

### **系统OOM之前的一些现象**

- 每次垃圾回收的时间越来越长，由之前的10ms延长到50ms左右，FullGC的时间也有之前的0.5s延长到4、5s
- FullGC的次数越来越多，最频繁时隔不到1分钟就进行一次FullGC
- 老年代的内存越来越大并且每次FullGC后，老年代只有少量的内存被释放掉



### **堆Dump文件分析**

可以通过指定启动参数 -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/usr/app/data/dump/heapdump.hpro 在发生OOM的时候自动导出Dump文件
​

### **GC日志分析**

为了方便分析GC日志信息，可以指定启动参数 【-Xloggc: app-gc.log  -XX:+PrintGCDetails -XX:+PrintGCDateStamps】,方便详细地查看GC日志信息

1. 使用 【jinfo pid】查看当前JVM堆的相关参数
1. 继续使用 【jstat -gcutil 2315 1s 10】查看10s内当前堆的占用情况
1. 也可以使用【jmap -heap pid】查看当前JVM堆的情况
1. 我们可以继续使用 【jmap -F -histo pid | head -n 20】，查看前20行打印，即查看当前top20的大对象，一般从这里可以发现一些异常的大对象，如果没有，那么可以继续排名前50的大对象，分析
1. 最后使用【jmap -F -dump:file=a.bin pid】，如果dump文件很大，可以压缩一下【tar -czvf a.tar.gz a.bin】
1. 再之后，就是对dump文件进行分析了，使用MAT分析内存泄露
1. 参考案例： [https://www.lagou.com/lgeduarticle/142372.html](https://www.lagou.com/lgeduarticle/142372.html)

### 线上死锁排查

1. jps 查找一个可能有问题的**进程id**
1. 然后执行 【jstack -F **进程id**】
1. 如果环境允许远程连接JVM，可以使用jconsole或者jvisualvm，图形化界面检测是否存在死锁

### 线上YGC耗时过长优化

1. 如果生命周期过长的对象越来越多（比如全局变量或者静态变量等），会导致标注和复制过程的耗时增加
1. 对存活对象标注时间过长：比如重载了Object类的Finalize方法，导致标注Final Reference耗时过长；或者String.intern方法使用不当，导致YGC扫描StringTable时间过长。可以通过以下参数显示GC处理Reference的耗时-XX:+PrintReferenceGC
1. 长周期对象积累过多：比如本地缓存使用不当，积累了太多存活对象；或者锁竞争严重导致线程阻塞，局部变量的生命周期变长
1. 案例参考： [https://my.oschina.net/lishangzhi/blog/4703942](https://my.oschina.net/lishangzhi/blog/4703942)

### 线上频繁FullGC优化

1. 线上频繁FullGC一般会有这么几个特征：
   1. 线上多个线程的CPU都超过了100%，通过jstack命令可以看到这些线程主要是垃圾回收线程
   1. 通过jstat命令监控GC情况，可以看到Full GC次数非常多，并且次数在不断增加
2. 排查流程：
   1. top找到cpu占用最高的一个 **进程id**
   1. 然后 【top -Hp 进程id】，找到cpu占用最高的 **线程id**
   1. 【printf "%x\n" **线程id 】**，假设16进制结果为 a
   1. jstack 线程id | grep '0xa' -A 50 --color
   1. 如果是正常的用户线程， 则通过该线程的堆栈信息查看其具体是在哪处用户代码处运行比较消耗CPU
   1. 如果该线程是 VMThread，则通过 jstat-gcutil命令监控当前系统的GC状况，然后通过 jmapdump:format=b,file=导出系统当前的内存数据。导出之后将内存情况放到eclipse的mat工具中进行分析即可得出内存中主要是什么对象比较消耗内存，进而可以处理相关代码；正常情况下会发现VM Thread指的就是垃圾回收的线程
   1. 再执行【jstat -gcutil  **进程id】, **看到结果，如果FGC的数量很高，且在不断增长，那么可以定位是由于内存溢出导致FullGC频繁，系统缓慢
   1. 然后就可以Dump出内存日志，然后使用MAT的工具分析哪些对象占用内存较大，然后找到对象的创建位置，处理即可
3. 参考案例：[https://mp.weixin.qq.com/s/g8KJhOtiBHWb6wNFrCcLVg](https://mp.weixin.qq.com/s/g8KJhOtiBHWb6wNFrCcLVg)
   #### 

### 线上堆外内存泄漏分析（Netty尤其居多）

1. JVM的堆外内存泄露的定位一直是个比较棘手的问题
1. 对外内存的泄漏分析一般都是先从堆内内存分析的过程中衍生出来的。有可能我们分析堆内内存泄露过程中发现，我们计算出来的JVM堆内存竟然大于了整个JVM的**Xmx**的大小，那说明多出来的是堆外内存
1. 如果使用了 Netty 堆外内存，那么可以自行监控堆外内存的使用情况，不需要借助第三方工具，我们是使用的“反射”拿到的堆外内存的情况
1. 逐渐缩小范围，直到 Bug 被找到。当我们确认某个线程的执行带来 Bug 时，可单步执行，可二分执行，定位到某行代码之后，跟到这段代码，然后继续单步执行或者二分的方式来定位最终出 Bug 的代码。这个方法屡试不爽，最后总能找到想要的 Bug
1. 熟练掌握 idea 的调试，让我们的“捉虫”速度快如闪电（“闪电侠”就是这么来的）。这里，最常见的调试方式是**预执行表达式**，以及通过**线程调用栈**，死盯某个对象，就能够掌握这个对象的定义、赋值之类
1. 在使用直接内存的项目中，最好建议配置 -XX:MaxDirectMemorySize，设定一个系统实际可达的最大的直接内存的值，默认的最大直接内存大小等于 -Xmx的值
1. 排查堆外泄露，建议指定启动参数： -XX:NativeMemoryTracking=summary - Dio.netty.leakDetection.targetRecords=100-Dio.netty.leakDetection.level=PARANOID，后面两个参数是Netty的相关内存泄露检测的级别与采样级别
1. 参考案例： [https://tech.meituan.com/2018/10/18/netty-direct-memory-screening.html](https://tech.meituan.com/2018/10/18/netty-direct-memory-screening.html)

### 线上元空间内存泄露优化

1. 需要注意的一点是 Java8以及Java8+的JVM已经将永久代废弃了，取而代之的是元空间，且元空间是不是在JVM堆中的，而属于堆外内存，受最大物理内存限制。最佳实践就是我们在启动参数中最好设置上 -XX:MetaspaceSize=1024m -XX:MaxMetaspaceSize=1024m。具体的值根据情况设置。为避免动态申请，可以直接都设置为最大值
1. 元空间主要存放的是类元数据，而且metaspace判断类元数据是否可以回收，是根据加载这些类元数据的Classloader是否可以回收来判断的，只要Classloader不能回收，通过其加载的类元数据就不会被回收。所以线上有时候会出现一种问题，由于框架中，往往大量采用类似ASM、javassist等工具进行字节码增强，生成代理类。如果项目中由主线程频繁生成动态代理类，那么就会导致元空间迅速占满，无法回收
1. 具体案例可以参见： [https://zhuanlan.zhihu.com/p/200802910](