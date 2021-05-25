# Callable

参考：

+ [Java并发编程：Callable、Future和FutureTask](https://www.cnblogs.com/dolphin0520/p/3949310.html)
+ [Java多线程编程：Callable、Future和FutureTask浅析（多线程编程之四）](https://blog.csdn.net/javazejian/article/details/50896505)

`Callable`是一个接口

> ```java
> public interface Callable<V>
> ```
>
> A task that returns a result and may throw an exception. Implementors define a single method with no arguments called `call`.
>
> The `Callable` interface is similar to [`Runnable`](https://docs.oracle.com/javase/7/docs/api/java/lang/Runnable.html), in that both are designed for classes whose instances are potentially executed by another thread. A `Runnable`, however, does not return a result and cannot throw a checked exception.
>
> The [`Executors`](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Executors.html) class contains utility methods to convert from other common forms to `Callable` classes.

**A `Runnable`, however, does not return a result and cannot throw a checked exception.**

定义：

```java
public interface Callable<V> {

    V call() throws Exception;

}
```

> The `call()` method is called in order to execute the asynchronous task. The `call()` method can return a result. If the task is executed asynchronously, the result is typically propagated back to the creator of the task via a [Java Future](http://tutorials.jenkov.com/java-util-concurrent/java-future.html). This is the case when a `Callable` is submitted to an `ExecutorService` for concurrent execution.
>
> The `call()` method can also thrown an `Exception` in case the task fails during execution.

**那么怎么使用Callable呢？**一般情况下是配合`ExecutorService`来使用的，在ExecutorService接口中声明了若干个`submit`方法的重载版本：

```java
<T> Future<T> submit(Callable<T> task);
<T> Future<T> submit(Runnable task, T result);
Future<?> submit(Runnable task);
```

## Future

`Future`就是对于具体的`Runnable`或者`Callable`任务的执行结果进行取消、查询是否完成、获取结果。必要时可以通过`get`方法获取执行结果，该方法会阻塞直到任务返回结果。

`Future`类位于`java.util.concurrent`包下，它是一个接口：

```java
public interface Future<V> {
    boolean cancel(boolean mayInterruptIfRunning);
    boolean isCancelled();
    boolean isDone();
    V get() throws InterruptedException, ExecutionException;
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

> 因为`Future`只是一个接口，所以是无法直接用来创建对象使用的，因此就有了下面的`FutureTask`。
>
>  在Future接口中声明了5个方法，下面依次解释每个方法的作用：
>
> - cancel方法用来取消任务，如果取消任务成功则返回true，如果取消任务失败则返回false。参数mayInterruptIfRunning表示是否允许取消正在执行却没有执行完毕的任务，如果设置true，则表示可以取消正在执行过程中的任务。如果任务已经完成，则无论mayInterruptIfRunning为true还是false，此方法肯定返回false，即如果取消已经完成的任务会返回false；如果任务正在执行，若mayInterruptIfRunning设置为true，则返回true，若mayInterruptIfRunning设置为false，则返回false；如果任务还没有执行，则无论mayInterruptIfRunning为true还是false，肯定返回true。
> - isCancelled方法表示任务是否被取消成功，如果在任务正常完成前被取消成功，则返回 true。
> - isDone方法表示任务是否已经完成，若任务完成，则返回true；
> - get()方法用来获取执行结果，这个方法会产生阻塞，会一直等到任务执行完毕才返回；
> - get(long timeout, TimeUnit unit)用来获取执行结果，如果在指定时间内，还没获取到结果，就直接返回null。
>
> 　　也就是说Future提供了三种功能：
>
> 　　1）判断任务是否完成；
>
> 　　2）能够中断任务；
>
> 　　3）能够获取任务执行结果。
>
> 　　因为Future只是一个接口，所以是无法直接用来创建对象使用的，因此就有了下面的FutureTask。

## FutureTask

我们先来看一下`FutureTask`的实现：

```java
public class FutureTask<V> implements RunnableFuture<V>
```

 `FutureTask`类实现了`RunnableFuture`接口，我们看一下`RunnableFuture`接口的实现：

```java
public interface RunnableFuture<V> extends Runnable, Future<V> {
    void run();
}
```

 可以看出`RunnableFuture`继承了`Runnable`接口和Future接口，而`FutureTask`实现了`RunnableFuture`接口。所以它既可以作为`Runnable`被线程执行，又可以作为`Future`得到Callable的返回值。

`FutureTask`提供了2个构造器：

```java
public FutureTask(Callable<V> callable) {
}
public FutureTask(Runnable runnable, V result) {
}
```



## 例子

**1.使用Callable+Future获取执行结果**

```java
public class Test {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newCachedThreadPool();
        Task task = new Task();
        Future<Integer> result = executor.submit(task);
        executor.shutdown();
         
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e1) {
            e1.printStackTrace();
        }
         
        System.out.println("主线程在执行任务");
         
        try {
            System.out.println("task运行结果"+result.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
         
        System.out.println("所有任务执行完毕");
    }
}
class Task implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
        System.out.println("子线程在进行计算");
        Thread.sleep(3000);
        int sum = 0;
        for(int i=0;i<100;i++)
            sum += i;
        return sum;
    }
}
```

执行结果：

```java
子线程在进行计算
主线程在执行任务
task运行结果4950
所有任务执行完毕
```



**2.使用Callable+FutureTask获取执行结果**

```java
public class Test {
    public static void main(String[] args) {
        //第一种方式
        ExecutorService executor = Executors.newCachedThreadPool();
        Task task = new Task();
        FutureTask<Integer> futureTask = new FutureTask<Integer>(task);
        executor.submit(futureTask);
        executor.shutdown();
         
        //第二种方式，注意这种方式和第一种方式效果是类似的，只不过一个使用的是ExecutorService，一个使用的是Thread
        /*Task task = new Task();
        FutureTask<Integer> futureTask = new FutureTask<Integer>(task);
        Thread thread = new Thread(futureTask);
        thread.start();*/
         
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e1) {
            e1.printStackTrace();
        }
         
        System.out.println("主线程在执行任务");
         
        try {
            System.out.println("task运行结果"+futureTask.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
         
        System.out.println("所有任务执行完毕");
    }
}
class Task implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
        System.out.println("子线程在进行计算");
        Thread.sleep(3000);
        int sum = 0;
        for(int i=0;i<100;i++)
            sum += i;
        return sum;
    }
}
```

 如果为了可取消性而使用 `Future` 但又不提供可用的结果，则可以声明 `Future<?>` 形式类型、并返回 `null` 作为底层任务的结果。















































