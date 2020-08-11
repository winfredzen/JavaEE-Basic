# interrupt

[interrupt](https://docs.oracle.com/javase/7/docs/api/java/lang/Thread.html)是Java提供的一种中断机制

>If this thread is blocked in an invocation of the `wait()`, `wait(long)`, or `wait(long, int)` methods of the `Object` class, or of the `join()`, `join(long)`, `join(long, int)`, `sleep(long)`, or `sleep(long, int)`, methods of this class, then its interrupt status will be cleared and it will receive an `InterruptedException`.

参考：[Java并发编程：Thread类的使用](https://www.cnblogs.com/dolphin0520/p/3920357.html)

interrupt，顾名思义，即中断的意思。单独调用interrupt方法可以使得处于阻塞状态的线程抛出一个异常，也就说，它可以用来中断一个正处于阻塞状态的线程；另外，通过`interrupt`方法和`isInterrupted()`方法来停止正在运行的线程。

```java
package com.wz.thread;

import java.io.IOException;

public class InterruptTest {

    public static void main(String[] args) throws IOException  {
    	InterruptTest test = new InterruptTest();
        MyThread thread = test.new MyThread();
        thread.start();
        try {
            Thread.currentThread().sleep(2000);
        } catch (InterruptedException e) {
             
        }
        thread.interrupt();
    } 
     
    class MyThread extends Thread{
        @Override
        public void run() {
            try {
                System.out.println("进入睡眠状态");
                Thread.currentThread().sleep(10000);
                System.out.println("睡眠完毕");
            } catch (InterruptedException e) {
                System.out.println("得到中断异常");
            }
            System.out.println("run方法执行完毕");
        }
    }
	
}
```
输出为：
```
进入睡眠状态
得到中断异常
run方法执行完毕
```

从这里可以看出，通过interrupt方法可以中断处于阻塞状态的线程。那么能不能中断处于非阻塞状态的线程呢？看下面这个例子：

```java
public class Test {
     
    public static void main(String[] args) throws IOException  {
        Test test = new Test();
        MyThread thread = test.new MyThread();
        thread.start();
        try {
            Thread.currentThread().sleep(2000);
        } catch (InterruptedException e) {
             
        }
        thread.interrupt();
    } 
     
    class MyThread extends Thread{
        @Override
        public void run() {
            int i = 0;
            while(i<Integer.MAX_VALUE){
                System.out.println(i+" while循环");
                i++;
            }
        }
    }
}
```
运行该程序会发现，`while`循环会一直运行直到变量i的值超出`Integer.MAX_VALUE`。所以说直接调用`interrupt`方法不能中断正在运行中的线程。

但是如果配合`isInterrupted()`能够中断正在运行的线程，因为调用`interrupt`方法相当于将中断标志位置为`true`，那么可以通过调用`isInterrupted()`判断中断标志是否被置位来中断线程的执行。比如下面这段代码：

```java
public class Test {
     
    public static void main(String[] args) throws IOException  {
        Test test = new Test();
        MyThread thread = test.new MyThread();
        thread.start();
        try {
            Thread.currentThread().sleep(2000);
        } catch (InterruptedException e) {
             
        }
        thread.interrupt();
    } 
     
    class MyThread extends Thread{
        @Override
        public void run() {
            int i = 0;
            while(!isInterrupted() && i<Integer.MAX_VALUE){
                System.out.println(i+" while循环");
                i++;
            }
        }
    }
}
```

运行会发现，打印若干个值之后，while循环就停止打印了。

但是一般情况下不建议通过这种方式来中断线程，一般会在MyThread类中增加一个属性 `isStop`来标志是否结束`while`循环，然后再在`while`循环中判断`isStop`的值。

```java
class MyThread extends Thread{
        private volatile boolean isStop = false;
        @Override
        public void run() {
            int i = 0;
            while(!isStop){
                i++;
            }
        }
         
        public void setStop(boolean stop){
            this.isStop = stop;
        }
    }
```









