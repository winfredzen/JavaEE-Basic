# yield

参考：

+ [Java Concurrency – Difference between yield() and join()](https://howtodoinjava.com/java/multi-threading/difference-between-yield-and-join-in-threads-in-java/)

```java
/**
  * A hint to the scheduler that the current thread is willing to yield its current use of a processor. The scheduler is free to ignore
  * this hint. Yield is a heuristic attempt to improve relative progression between threads that would otherwise over-utilize a CPU.
  * Its use should be combined with detailed profiling and benchmarking to ensure that it actually has the desired effect.
  */
 
public static native void yield();
```

`yield`方法仅仅释放线程所占有的CPU资源，从而让其他线程有机会运行，但是并不能保证某个特定的线程能够获得CPU资源。谁能获得CPU完全取决于调度器，在有些情况下调用`yield`方法的线程甚至会再次得到CPU资源

`yield`使线程状态从`Running`状态变为`Runnable`状态，而不是`wait`或者`blocked`状态


## 线程的优先级

Java线程的优先级是一个整数，其取值范围是1 （`Thread.MIN_PRIORITY` ） - 10 （`Thread.MAX_PRIORITY` ）。

Thread源代码里对`NORM_PRIORITY` （数值为5） 的注释是“线程默认的优先级”

```java
    public static final int MIN_PRIORITY = 1;
    public static final int NORM_PRIORITY = 5;
    public static final int MAX_PRIORITY = 10;
```

可以通过`setPriority`方法（`final`的，不能被子类重载）更改优先级。优先级不能超出`1-10`的取值范围，否则抛出`IllegalArgumentException`

`getPriority()`方法返回线程的当前优先级


## 例子

```java
package com.wz.thread;

public class YieldTest {

	public static void main(String[] args) {

		Thread producer = new YProducer();
		Thread consumer = new YConsumer();
		
		producer.setPriority(Thread.MIN_PRIORITY);
		consumer.setPriority(Thread.MAX_PRIORITY);

		producer.start();
		consumer.start();
		
	}

}

class YProducer extends Thread {
	@Override
	public void run() {
		for (int i = 0; i < 5; i++) {
			System.out.println("I am Producer : Produced Item " + i);
			Thread.yield();
		}
	}
}

class YConsumer extends Thread {
	@Override
	public void run() {
		for (int i = 0; i < 5; i++) {
			System.out.println("I am Consumer : Consumed Item " + i);
			Thread.yield();
		}
	}
}
```

输出为：

```
I am Consumer : Consumed Item 0
I am Producer : Produced Item 0
I am Consumer : Consumed Item 1
I am Consumer : Consumed Item 2
I am Producer : Produced Item 1
I am Producer : Produced Item 2
I am Consumer : Consumed Item 3
I am Producer : Produced Item 3
I am Producer : Produced Item 4
I am Consumer : Consumed Item 4
```


