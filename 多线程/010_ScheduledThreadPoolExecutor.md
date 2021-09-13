# ScheduledThreadPoolExecutor

> A `ThreadPoolExecutor` that can additionally schedule commands to run after a given delay, or to execute periodically.

即周期执行或者延迟执行

**继承关系**

![003](https://github.com/winfredzen/JavaEE-Basic/blob/master/%E5%A4%9A%E7%BA%BF%E7%A8%8B/images/003.png)



**延迟执行**

```java
    public ScheduledFuture<?> schedule(Runnable command,
                                       long delay,
                                       TimeUnit unit)
```

如下的例子：

```java
        ScheduledThreadPoolExecutor executor = new ScheduledThreadPoolExecutor(2);
        Task task1 = new Task("Task1");
        Task task2 = new Task("Task2");

        System.out.println("The time is : " + new Date());
        executor.schedule(task1, 5, TimeUnit.SECONDS);
        executor.schedule(task1, 10, TimeUnit.SECONDS);

        executor.shutdown();
        try {
            executor.awaitTermination(1, TimeUnit.DAYS);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("executor.shutdown()");
```

```java
public class Task implements Runnable {
    private String name;

    public Task(String name) {
        this.name = name;
    }

    @Override
    public void run() {
        try {
            System.out.println("Doing a task during : " + name + " - Time - " + new Date());
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

输出结果顺序为：

```java
The time is : Mon Sep 13 10:53:45 CST 2021
Doing a task during : Task1 - Time - Mon Sep 13 10:53:50 CST 2021
Doing a task during : Task1 - Time - Mon Sep 13 10:53:55 CST 2021
executor.shutdown()

Process finished with exit code 0
```

一些说明：

> `awaitTermination()`
>
> > The `ExecutorService` `awaitTermination()` method will block the thread calling it until either the `ExecutorService` has shutdown completely, or until a given time out occurs. The `awaitTermination()` method is typically called after calling `shutdown()` or `shutdownNow()`. Here is an example of calling `ExecutorService` `awaitTermination()`:
> >
> > ```java
> > executorService.shutdown();
> > 
> > executorService.awaitTermination(10_000L, TimeUnit.MILLISECONDS );
> > ```



**Execute a task periodically**

`scheduleAtFixedRate` 与 `scheduleWithFixedDelay`

+ `scheduleAtFixedRate`，固定频率周期任务，注意一次任务超时，会持续的影响后续的任务周期；
+ `scheduleWithFixedDelay`，固定延迟周期任务，即每次任务结束后，超时等待固定时间；



如下的例子：

```java
        ScheduledExecutorService executor = Executors.newScheduledThreadPool(1);
        Task task1 = new Task ("Demo Task 1");

        System.out.println("The time is : " + new Date());

        ScheduledFuture<?> result = executor.scheduleAtFixedRate(task1, 2, 5, TimeUnit.SECONDS);

        try {
            TimeUnit.MILLISECONDS.sleep(20000);
        }
        catch (InterruptedException e) {
            e.printStackTrace();
        }

        executor.shutdown();
```

输出结果如下：

```java
The time is : Mon Sep 13 11:10:32 CST 2021
Doing a task during : Demo Task 1 - Time - Mon Sep 13 11:10:34 CST 2021
Doing a task during : Demo Task 1 - Time - Mon Sep 13 11:10:39 CST 2021
Doing a task during : Demo Task 1 - Time - Mon Sep 13 11:10:44 CST 2021
Doing a task during : Demo Task 1 - Time - Mon Sep 13 11:10:49 CST 2021

Process finished with exit code 0
```





## 其它参考

+ [并发系列（7）之 ScheduledThreadPoolExecutor 详解](https://www.cnblogs.com/sanzao/p/10760641.html)





