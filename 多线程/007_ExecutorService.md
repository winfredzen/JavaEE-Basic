# ExecutorService

`ExecutorService`继承自`Executor`接口，也是一个接口

![002](https://github.com/winfredzen/JavaEE-Basic/blob/master/%E5%A4%9A%E7%BA%BF%E7%A8%8B/images/002.png)

参考：

+ [ThreadPoolExecutor – Java Thread Pool Example](https://www.journaldev.com/1069/threadpoolexecutor-java-thread-pool-example-executorservice)

> **Java thread pool** manages the pool of worker threads. It contains a queue that keeps tasks waiting to get executed. We can use `ThreadPoolExecutor` to create thread pool in Java.
>
> Java thread pool manages the collection of Runnable threads. The worker threads execute Runnable threads from the queue. **java.util.concurrent.Executors** provide factory and support methods for **java.util.concurrent.Executor** interface to create the thread pool in java.
>
> Executors is a utility class that also provides useful methods to work with ExecutorService, ScheduledExecutorService, ThreadFactory, and Callable classes through various factory methods.

如下的例子，创建一个`Runnable`类，叫做`WorkerThread.java`

```java
public class WorkerThread implements Runnable {
  
    private String command;
    
    public WorkerThread(String s){
        this.command=s;
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+" Start. Command = "+command);
        processCommand();
        System.out.println(Thread.currentThread().getName()+" End.");
    }

    private void processCommand() {
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    @Override
    public String toString(){
        return this.command;
    }
}
```

`ExecutorService` 的例子：

```java
public class SimpleThreadPool {

    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(5);
        for (int i = 0; i < 10; i++) {
            Runnable worker = new WorkerThread("" + i);
            executor.execute(worker);
          }
        executor.shutdown();
        while (!executor.isTerminated()) {
        }
        System.out.println("Finished all threads");
    }
}
```

最后输出的结果如下：

```java
pool-1-thread-2 Start. Command = 1
pool-1-thread-4 Start. Command = 3
pool-1-thread-1 Start. Command = 0
pool-1-thread-3 Start. Command = 2
pool-1-thread-5 Start. Command = 4
pool-1-thread-4 End.
pool-1-thread-5 End.
pool-1-thread-1 End.
pool-1-thread-3 End.
pool-1-thread-3 Start. Command = 8
pool-1-thread-2 End.
pool-1-thread-2 Start. Command = 9
pool-1-thread-1 Start. Command = 7
pool-1-thread-5 Start. Command = 6
pool-1-thread-4 Start. Command = 5
pool-1-thread-2 End.
pool-1-thread-4 End.
pool-1-thread-3 End.
pool-1-thread-5 End.
pool-1-thread-1 End.
Finished all threads
```

参考：

+ [Java线程池 ExecutorService](https://blog.csdn.net/suifeng3051/article/details/49443835)

> **ExecutorService的创建**
>
> 创建一个什么样的ExecutorService的实例（即线程池）需要g根据具体应用场景而定，不过Java给我们提供了一个`Executors工厂类`，它可以帮助我们很方便的创建各种类型ExecutorService线程池，Executors一共可以创建下面这四类线程池：
>
> ```
> 1. newCachedThreadPool 创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。
> 2. newFixedThreadPool 创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。
> 3. newScheduledThreadPool 创建一个定长线程池，支持定时及周期性任务执行。
> 4. newSingleThreadExecutor 创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。
> ```
>
> 备注：Executors只是一个工厂类，它所有的方法返回的都是`ThreadPoolExecutor`、`ScheduledThreadPoolExecutor`这两个类的实例。



> **ExecutorService的关闭**
>
> 当我们使用完成`ExecutorService`之后应该关闭它，否则它里面的线程会一直处于运行状态。
>
> 举个例子，如果的应用程序是通过`main()`方法启动的，在这个`main()`退出之后，如果应用程序中的`ExecutorService`没有关闭，这个应用将一直运行。之所以会出现这种情况，是因为`ExecutorService`中运行的线程会阻止JVM关闭。
>
> 如果要关闭`ExecutorService`中执行的线程，我们可以调用`ExecutorService.shutdown()`方法。在调用`shutdown()`方法之后，`ExecutorService`不会立即关闭，但是它不再接收新的任务，直到当前所有线程执行完成才会关闭，所有在`shutdown()`执行之前提交的任务都会被执行。
>
> 如果我们想立即关闭`ExecutorService`，我们可以调用`ExecutorService.shutdownNow()`方法。这个动作将跳过所有正在执行的任务和被提交还没有执行的任务。但是它并不对正在执行的任务做任何保证，有可能它们都会停止，也有可能执行完成。



## 其它

教程：

+ [A Guide to the Java ExecutorService](https://www.baeldung.com/java-executor-service-tutorial)









































