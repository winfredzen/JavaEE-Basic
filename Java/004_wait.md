# wait

`wait()`方法是`Object`类中定义的方法，有如下的几种形式

```java
public final void wait() throws InterruptedException {
	wait(0);
}

public final native void wait(long timeout) throws InterruptedException;

public final void wait(long timeout, int nanos) throws InterruptedException
```

`wait()`的作用是让当前线程进入等待状态，同时，`wait()`也会让当前线程释放它所持有的锁。直到其他线程调用此对象的 `notify()` 方法或 `notifyAll()` 方法

调用 `wait()`方法的语法一般如下：

```java
synchronized( lockObject )
{
    while( ! condition )
    {
        lockObject.wait();
    }
     
    //take the action here;
}
```

调用`notify()`方法的语法一般如下：

```java
synchronized(lockObject)
{
    //establish_the_condition;
 
    lockObject.notify();
     
    //any additional code if needed
}
```

参考：[How to work with wait(), notify() and notifyAll() in Java?](https://howtodoinjava.com/java/multi-threading/wait-notify-and-notifyall-methods/)实现生产者-消费者问题

生产者Producer

```java
package com.wz.thread;

import java.util.List;

public class Producer implements Runnable {

	private final List<Integer> taskQueue;
	private final int MAX_CAPACITY;

	public Producer(List<Integer> sharedQueue, int size) {
		this.taskQueue = sharedQueue;
		this.MAX_CAPACITY = size;
	}

	@Override
	public void run() {
		int counter = 0;
		while (true) {
			try {
				produce(counter++);
			} catch (InterruptedException ex) {
				ex.printStackTrace();
			}
		}
	}

	private void produce(int i) throws InterruptedException {
		synchronized (taskQueue) {
			while (taskQueue.size() == MAX_CAPACITY) {
				System.out.println("Queue is full " + Thread.currentThread().getName() + " is waiting , size: "
						+ taskQueue.size());
				taskQueue.wait();
			}
			Thread.sleep(1000);
			taskQueue.add(i);
			System.out.println("Produced: " + i);
			taskQueue.notifyAll();
		}
	}

}

```

消费者Consumer

```java
package com.wz.thread;

import java.util.List;

class Consumer implements Runnable {
	private final List<Integer> taskQueue;

	public Consumer(List<Integer> sharedQueue) {
		this.taskQueue = sharedQueue;
	}

	@Override
	public void run() {
		while (true) {
			try {
				consume();
			} catch (InterruptedException ex) {
				ex.printStackTrace();
			}
		}
	}

	private void consume() throws InterruptedException {
		synchronized (taskQueue) {
			while (taskQueue.isEmpty()) {
				System.out.println("Queue is empty " + Thread.currentThread().getName() + " is waiting , size: "
						+ taskQueue.size());
				taskQueue.wait();
			}
			Thread.sleep(1000);
			int i = (Integer) taskQueue.remove(0);
			System.out.println("Consumed: " + i);
			taskQueue.notifyAll();
		}
	}
}
```

测试如下：

```java
package com.wz.thread;

import java.util.ArrayList;
import java.util.List;

public class Test {

	public static void main(String[] args) {
		
	      List<Integer> taskQueue = new ArrayList<Integer>();
	      int MAX_CAPACITY = 5;
	      Thread tProducer = new Thread(new Producer(taskQueue, MAX_CAPACITY), "Producer");
	      Thread tConsumer = new Thread(new Consumer(taskQueue), "Consumer");
	      tProducer.start();
	      tConsumer.start();
		
	}
	
}

```

控制台输出如下：

```
Produced: 0
Produced: 1
Produced: 2
Produced: 3
Produced: 4
Queue is full Producer is waiting , size: 5
Consumed: 0
Consumed: 1
Consumed: 2
Consumed: 3
Consumed: 4
Queue is empty Consumer is waiting , size: 0
Produced: 5
Produced: 6
Produced: 7
Produced: 8
Produced: 9
Queue is full Producer is waiting , size: 5
Consumed: 5
Consumed: 6
```



















