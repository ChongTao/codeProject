# 1 线程状态

![](https://pic4.zhimg.com/v2-1319f27379e4745d02b40ea12b9307cb_r.jpg)

线程的生命周期如上图，一个线程的状态只能处于其中一个，这些状态并不是完全独立的，线程在不同状态之间进行转换，下面详细介绍每个状态的含义：

1. **新建（NEW）**：当一个新线程被创建但尚未启动时，它就处于这种状态。
2. **可运行（Runnable）**：当线程准备好运行但等待CPU分配时间片时，它就处于这种状态。在Java中，这种状态有时也称为"就绪"状态。
3. **运行（Running）**：当线程正在执行其代码时，它就处于这种状态。
4. **阻塞（Blocked）**：当一个线程正在等待某些条件满足（如I/O请求，申请缓存空间等）时，它就处于这种状态。这也称为等待或睡眠状态。
5. **定时等待（TIMED_WAITING）**：当线程在等待某个特定的时间或者直到另一个线程执行了某个特定的动作时，它就处于这种状态。
6. **等待（WAITING）**：当进程在等待另一个进程执行特定的动作时（如线程A等待线程B的某个操作完成），它就处于这种状态。
7. **终止（TERMINATED）**：当进程完成其执行或者由于某种原因被终止时，它就处于这种状态。



# 2 JAVA线程创建

在Java中，创建线程主要有四种方式：

1. **继承Thread类**： 创建一个新类，继承自`Thread`类，并重写`run()`方法。然后创建该类的实例，并调用其`start()`方法来启动线程。

   ```java
   public class MyThread extends Thread {
       @Override
       public void run() {
           // 线程执行的代码
       }
   
       public static void main(String[] args) {
           MyThread myThread = new MyThread();
           myThread.start(); // 启动线程
       }
   }
   ```

2. **实现Runnable接口**： 创建一个新类，实现`Runnable`接口，并重写`run()`方法。然后将该类的实例作为参数传递给`Thread`类的构造器来创建线程，并调用`start()`方法启动线程。

   ```java
   public class MyRunnable implements Runnable {
       @Override
       public void run() {
           // 线程执行的代码
       }
   
       public static void main(String[] args) {
           Thread thread = new Thread(new MyRunnable());
           thread.start(); // 启动线程
       }
   }
   ```

3. **实现Callable接口**： `Callable`接口与`Runnable`接口类似，但它允许线程返回一个结果，并且可以抛出异常。通常与`Future`和`ExecutorService`一起使用。

   ```java
   public class MyCallable implements Callable<String> {
       @Override
       public String call() throws Exception {
           // 线程执行的代码
           return "结果";
       }
   
       public static void main(String[] args) throws ExecutionException, InterruptedException {
           ExecutorService executorService = Executors.newSingleThreadExecutor();
           Future<String> future = executorService.submit(new MyCallable());
           String result = future.get(); // 获取线程执行结果
           executorService.shutdown();
       }
   }
   ```

4. **使用线程池**： 线程池是Java中一种高效的线程管理方式，它允许你重用已存在的线程，减少创建和销毁线程的开销。Java提供了`ExecutorService`接口及其实现类（如`ThreadPoolExecutor`）来管理线程池。

   ```java
   public class MyRunnable implements Runnable {
       @Override
       public void run() {
           // 线程执行的代码
       }
   
       public static void main(String[] args) {
           ExecutorService executorService = Executors.newFixedThreadPool(5); // 创建一个固定大小的线程池
           for (int i = 0; i < 10; i++) {
               executorService.submit(new MyRunnable()); // 提交任务到线程池
           }
           executorService.shutdown(); // 关闭线程池
       }
   }
   ```

以上四种方式中，前两种是最基本的线程创建方式，而后两种则更加适合处理大量并发任务，尤其是需要控制线程数量或获取线程执行结果的情况。使用线程池可以有效地管理线程资源，提高程序的性能和响应速度。



# 3 Thread详解

Thread实现Runnable接口`public class Thread implements Runnable `, Runnable接口是一个函数式接口，使用注解`@FunctionalInterface`.

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

1. **start()**：
   - 启动线程，使线程从新建状态进入就绪队列排队。一旦轮到线程享用CPU资源，线程就会脱离创建它的线程，独立开始自己的生命周期。
2. **run()**：
   - Thread类的run()方法与Runnable接口中的run()方法功能相同，都用来定义线程对象被调度之后所执行的操作。这是系统自动调用的方法，用户程序不得直接引用。
3. **sleep(int milliseconds)**：
   - 使当前线程放弃CPU资源，进入休眠状态一段时间（以毫秒为单位）。休眠期间，线程不会执行任何操作，但会继续拥有监视资源的所有权。休眠时间结束后，线程将继续执行。
4. **isAlive()**：
   - 判断线程是否仍然“存活”。如果线程处于新建状态，该方法将返回false。如果线程正在执行，或者准备执行，或者阻塞等待某些监视器锁，该方法将返回true。
5. **getPriority()**：
   - 返回线程的优先级数值。线程的优先级是一个整数，其值越大，线程的优先级越高。
6. **setPriority(int priority)**：
   - 设置线程的优先级。优先级高的线程有机会获得更多的执行时间。
7. **join()**：
   - 调用某个线程的join方法，会将当前线程与该线程“合并”，即当前线程会等待该线程结束，然后再恢复自己的执行。
8. **yield()**：
   - 当前线程放弃CPU资源，进入就绪队列等待调度。这与sleep()方法不同，因为yield()不会让线程进入休眠状态，而只是让出CPU给其他线程使用。
9. **wait()**：
   - 当前线程进入对象的等待池（wait pool），等待其他线程调用该对象的notify()或notifyAll()方法。调用wait()方法前，线程必须拥有对象的监视器锁（即必须在同步代码块或同步方法中）。
10. **notify()** 和 **notifyAll()**：
    - 唤醒对象的等待池中的一个或所有等待线程。这些方法必须在同步代码块或同步方法中调用，且调用前线程必须拥有对象的监视器锁。
