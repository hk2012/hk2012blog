---
title: 线程的sleep、wait、join、yield如何使用
category: 文档
tag: 总结
date: 2024-02-15 09:00:00
abbrlink: 25
---
## 线程的sleep、wait、join、yield如何使用

**sleep**:

sleep的作用是让目前正在执行的线程休眠，让CPU去执行其他的任务。从线程状态来说，就是从执行状态变成限时阻塞状态。Sleep()方法定义在Thread类中，是一组静态方法，有两个重载版本：

```java
     //使目前正在执行的线程休眠millis毫秒
     public static void sleep(long millis) throws InterruptException；
     
     //使目前正在执行的线程休眠millis毫秒，nanos纳秒
     public static void sleep(long millis，int nanos) throws InterruptException；
```



sleep()方法会有InterruptException受检异常抛出，如果调用了sleep()方法，就必须进行异常审查，捕获InterruptedException异常，或者再次通过方法声明存在InterruptedException异常。

**wait**(必须先获得对应的锁才能调用):让线程进入等待状态,释放当前线程持有的锁资源线程只有在notify 或者notifyAll方法调用后才会被唤醒,然后去争夺锁.

```java
class ThreadA extends Thread{
	public ThreadA(String name) {
		super(name);
	}
	public void run() {
		synchronized (this) {
			try {						
				Thread.sleep(1000);	//	使当前线阻塞 1s，确保主程序的 t1.wait(); 执行之后再执行 notify()
			} catch (Exception e) {
				e.printStackTrace();
			}			
			System.out.println(Thread.currentThread().getName()+" call notify()");
			// 唤醒当前的wait线程
			this.notify();
		}
	}
}
public class WaitTest {
	public static void main(String[] args) {
		ThreadA t1 = new ThreadA("t1");
		synchronized(t1) {
			try {
				// 启动“线程t1”
				System.out.println(Thread.currentThread().getName()+" start t1");
				t1.start();
				// 主线程等待t1通过notify()唤醒。
				System.out.println(Thread.currentThread().getName()+" wait()");
				t1.wait();  //  不是使t1线程等待，而是当前执行wait的线程等待
				System.out.println(Thread.currentThread().getName()+" continue");
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
}
```

输出：

> main start t1
> main wait()
> t1 call notify()
> main continue



**join**:线程之间协同方式,使用场景: 线程C必须等待线程B运行完毕后才可以执行,那么就可以在线程B的代码中加入thread_c.join();;

```java
    public class TestThread {
        
        public static void main(String[] args) {
            ThreadB t1 = new ThreadB("thread-b");
            t1.start();
        }

    }
	
	class ThreadB extends Thread {

        public ThreadB(String name) {
            super(name);
        }

        public void run() {
            System.out.println(Thread.currentThread().getName());
            Thread thread_c = new ThreadC("thread-c");
            try {
                thread_c.join();
                thread_c.start();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

	class ThreadC extends Thread {

        public ThreadC(String name) {
            super(name);
        }

        public void run() {
            System.out.println(Thread.currentThread().getName());
        }
    }

//输出
//thread-b
//thread-c

         
     //重载版本1：此方法会把当前线程变为TIMED_WAITING，直到被合并线程执行结束
     public final void join() throws InterruptedException：
     
     //重载版本2：此方法会把当前线程变为TIMED_WAITING，直到被合并线程执行结束，或者等待被合并线程执行millis的时间
     public final synchronized void join(long millis) throws InterruptedException：
     
     //重载版本3：此方法会把当前线程变为TIMED_WAITING，直到被合并线程执行结束，或者等待被合并线程执行millis+nanos的时间
     public final synchroinzed void join(long millis, int nanos) throws InterruptedException：
```



**yield**:让当前正在运行的线程回到可运行状态，以允许具有相同优先级的其他线程获得运行的机会。因此，使用yield()的目的是让具有相同优先级的线程之间能够适当的轮换执行。但是，实际中无法保证yield()达到让步的目的，因为，让步的线程可能被线程调度程序再次选中。

```java

public class Test03 implements Runnable{
 
 
private String name;     
   
   private Test03(String name) {     
       this.name = name;     
   }  
 
    public synchronized void run() {
            System.out.println(name + " -> Start.");
            for(int i = 0; i < 100; i++) {
                if(i == 50) {
                	Thread.yield();
    			}
    			System.out.println(Thread.currentThread().getName() + "'s i=" + (i+1));
   			 }
    		System.out.println(name + "X -> End.");
    }

    public static void main(String[] args) {
            Test03 t1 = new Test03("A");
            Thread thread1 = new Thread(t1);
            Thread thread2 = new Thread(t1);
            thread1.start();
            thread2.start();
    }
结果:

A -> Start.
Thread-0's i=1
Thread-0's i=2
.........省略
Thread-0's i=99
Thread-0's i=100
AX -> End.
A -> Start.
Thread-1's i=1
Thread-1's i=2
...省略
Thread-1's i=99
Thread-1's i=100
AX -> End.

```



总结起来，Thread.yeid()方法有以下特点：

（1）yield仅能使一个线程从运行状态转到就绪状态，而不是阻塞状态。

（2）yield不能保证使得当前正在运行的线程迅速转换到就绪状态。

（3）即使完成了迅速切换，系统通过线程调度机制从所有就绪线程中挑选下一个执行线程时，就绪的线程有可能被选中，也有可能不被选中，其调度的过程受到其他因素（如优先级）的影响。

