# 1 AbstractQueuedSynchronizer

`AbstractQueuedSynchronizer`（AQS）是 Java 并发包（`java.util.concurrent.locks`）的核心基础框架，

几乎所有的同步器（如 `ReentrantLock`、`Semaphore`、`CountDownLatch` 等）都基于它实现。它的设计目标是

**通过模板方法模式，将同步器的实现与线程排队、阻塞/唤醒等底层机制解耦**，开发者只需关注同步状态的获取与

释放逻辑。

## 1.1 核心思想

AQS 的核心是管理一个 **同步状态（`state`）** 和一个 **FIFO 线程等待队列（CLH 队列的变体）**：

- **`state`**：整型变量，表示共享资源的状态（如锁的持有次数、信号量剩余许可数等）。
- **CLH 队列**：通过双向链表实现，未获取到资源的线程会被封装为 `Node` 对象加入队列等待。

## 1.2 两种同步模式

AQS 支持两种资源访问模式：

- **独占模式（Exclusive）**：资源一次只能被一个线程占用（如 `ReentrantLock`）。关键方法：

  - `acquire(int arg)`：尝试获取资源，失败则阻塞线程。

  - `release(int arg)`：释放资源，唤醒后继线程。

    > **实现类**：`ReentrantLock`、`ReentrantReadWriteLock.WriteLock`。

- **共享模式（Shared）**：资源可被多个线程同时占用（如 `Semaphore`、`CountDownLatch`）

  - `acquireShared(int arg)`：尝试获取共享资源。

  - `releaseShared(int arg)`：释放共享资源。

    > `Semaphore`、`CountDownLatch`、`ReentrantReadWriteLock.ReadLock`

AQS 是 Java 并发编程的基石，它通过模板方法模式将同步器的核心逻辑抽象为对 `state` 和 CLH 队列的操作。理解 AQS 的设计原理，不仅能帮助深入掌握 Java 并发工具类，还能为自定义同步器提供清晰的实现思路。

## 1.3 AQS的应用场景

AQS被广泛应用于Java并发包中的各种同步器，如`ReentrantLock`、`ReentrantReadWriteLock`、`Semaphore`、`CountDownLatch`等。AQS为这些同步器提供了一个统一的基础框架，并且可以让开发人员基于此进行扩展和定制化。使用AQS，开发人员可以避免自己重复实现同步器的底层机制，从而更加专注于业务的实现。

# 2 ReentrantLock 

`ReentrantLock` 实现了 `Lock` 接口，是一个可重入且独占式的锁，和 `synchronized` 关键字类似。不过，`ReentrantLock` 更灵活、更强大，增加了轮询、超时、中断、公平锁和非公平锁等高级功能。

```java
public class ReentrantLock implements Lock, java.io.Serializable {}
```

`ReentrantLock` 里面有一个内部类 `Sync`，`Sync` 继承 AQS（`AbstractQueuedSynchronizer`），添加锁和释放锁的大部分操作实际上都是在 `Sync` 中实现的。`Sync` 有公平锁 `FairSync` 和非公平锁 `NonfairSync` 两个子类。

`ReentrantLock` 默认使用非公平锁，也可以通过构造器来显式的指定使用公平锁。

```java
// 传入一个 boolean 值，true 时为公平锁，false 时为非公平锁
public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();
}
```





# 3 Semaphore

`Semaphore`是一个信号量同步工具类，它可以用来控制多个线程对共享资源的访问。Semaphore内部维护了一组许可证（permits），线程在访问共享资源前需要先获取许可证。如果许可证数量不足，线程将被阻塞直到有许可证可用。`Semaphore`可以用来实现资源的公平或非公平访问。

 控制对共享资源的并发访问线程数，常用于限流或资源池管理（如数据库连接池）

- 基于**许可证（Permit）**的机制。
- 初始化时指定许可证数量，线程通过 `acquire()` 获取许可证（若没有则阻塞），通过 `release()` 释放许可证。
- 支持公平/非公平模式。

首先，创建一个`Semaphore`对象，并指定一个整数作为初始许可证数量。然后，在需要访问共享资源的线程中，调用Semaphore的acquire()方法获取许可证。如果许可证数量不足，线程将被阻塞。当线程访问完共享资源后，调用release()方法释放许可证，以便其他线程可以获取并访问共享资源。

```java
Semaphore semaphore = new Semaphore(3); // 允许3个线程同时访问

void accessResource() {
    semaphore.acquire();
    try {
        // 访问共享资源
    } finally {
        semaphore.release();
    }
}
```

# 4 CountDownLatch

`CountDownLatch`是一个计数器类型的同步工具类，它允许一个或多个线程等待其他线程完成操作。`CountDownLatch`的初始值表示需要等待的线程数量。每当一个线程完成其任务后，就会调用`countDown()`方法将计数器减1。当计数器值减为0时，表示所有线程都已经完成任务，此时在`CountDownLatch`上等待的线程将被唤醒并继续执行。

- 初始化时指定计数器值（如 `N`）。
- 子线程完成任务后调用 `countDown()` 减少计数器。
- 主线程通过 `await()` 阻塞，直到计数器归零。

首先，创建一个`CountDownLatch`对象，并指定一个整数作为初始值，表示需要等待的线程数量。然后，在需要等待的线程中，调用`CountDownLatch`的`await()`方法。在其他线程完成任务后，调用`countDown()`方法将计数器减1。当计数器减为0时，所有在`await()`方法上等待的线程将被唤醒并继续执行。 

```java
CountDownLatch latch = new CountDownLatch(3); // 需要3个子线程完成任务

// 子线程
void doWork() {
    // 执行任务
    latch.countDown();
}

// 主线程
latch.await(); // 阻塞直到计数器归零
```

# 5 CyclicBarrier

`CyclicBarrier`是一个线程同步工具类，它可以让一组线程互相等待，直到所有线程都到达某个公共屏障点（barrier point）。当所有线程都到达屏障点时，屏障才会打开，然后所有线程可以继续执行。`CyclicBarrier`非常适合用于一组线程需要互相等待，然后一起执行某个操作的场景。

创建`CyclicBarrier`对象时，需要指定一个整数参数，表示需要等待的线程数量。然后，每个线程在需要等待的位置调用`CyclicBarrier`的await()方法。当所有线程都调用await()方法后，屏障才会打开，所有线程可以继续执行。

- 初始化时指定参与线程数和一个可选的 `Runnable` 任务（所有线程到达后执行）。

- 线程调用 `await()` 进入等待，当所有线程到达屏障点时，执行指定任务并继续。

  ```java
  CyclicBarrier barrier = new CyclicBarrier(3, () -> {
      System.out.println("所有线程到达屏障点！");
  });
  
  void process() {
      // 第一阶段任务
      barrier.await();
      // 第二阶段任务（所有线程同步后执行）
  }
  ```
