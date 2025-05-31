线程池（Thread Pool）是一种基于池化思想管理线程的工具。它维护多个线程，并复用这些线程来执行多个任务。线程池的主要目的是避免频繁地创建和销毁线程，以及控制并发执行的线程数量，从而提高系统的性能和资源利用率。

线程池由线程池管理器、工作队列和线程池线程组成。当需要执行任务时，线程池管理器会从线程池中获取一个空闲的线程，将任务分配给该线程执行。当任务执行完毕后，线程并不会死亡，而是返回线程池，等待执行下一个任务。这种机制避免了线程数量膨胀导致的过分调度问题，保证了对内核的充分利用。

线程池是一种多线程处理形式，将任务添加到队列，然后在创建线程后自动启动这些任务。每个线程都使用默认的堆栈大小，以默认的优先级运行，并处于多线程单元中。如果某个线程在托管代码中空闲（如正在等待某个事件），则线程池将插入另一个辅助线程来使所有处理器保持繁忙。如果所有线程池线程都始终保持繁忙，但队列中包含挂起的工作，则线程池将在一段时间后创建另一个辅助线程，但线程的数目永远不会超过最大值。超过最大值的线程可以排队，但他们要等到其他线程完成后才启动。

线程池适用于处理大量短时间任务的情况，它可以有效地提高系统性能和资源利用率，减少因频繁创建和销毁线程带来的开销。在互联网应用中，线程池被广泛使用来管理并发请求和执行任务。

## 1 线程池详解

线程池主要由以下几个部分组成，每个部分都通过特定的参数进行配置：

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler)
```

1. **corePoolSize**：线程池的基本大小，即使没有任务需要执行，线程池也会保持的线程数量。这个参数应根据系统的可用处理器数量和任务的性质来设置。

2. **maximumPoolSize**：线程池允许的最大线程数量。当队列满了，并且已创建的线程数小于 `maximumPoolSize`，线程池会再创建新的线程来执行任务。这个参数应根据系统的资源限制和任务的并发需求来设置。

3. **keepAliveTime**：当线程池中的线程数量大于 corePoolSize 时，这是多余的空闲线程在终止前等待新任务的最长时间。这个参数可以根据任务的执行时间和系统的资源利用率来设置。

4. **unit**：`keepAliveTime` 的时间单位，常用的是 `TimeUnit.SECONDS` 和 `TimeUnit.MILLISECONDS`。

5. **workQueue**：用于存储待执行任务的队列（实现`BlockingQueue`接口）。常见的队列类型有 `ArrayBlockingQueue`、`LinkedBlockingQueue`、`SynchronousQueue` 和 `PriorityBlockingQueue`。选择哪种队列类型取决于任务的性质和对性能的要求。

   - **ArrayBlockingQueue**：基于数组的有界阻塞队列。一旦创建，其容量不能改变。此队列按照 FIFO（先进先出）原则对元素进行排序。如果试图向已满的队列添加元素，操作将会阻塞；如果队列为空，尝试从中获取元素的操作将会阻塞。

   - **LinkedBlockingQueue**：基于链表结构的可选有界阻塞队列。如果不指定其容量，它将默认为 Integer.MAX_VALUE，可以认为是无界队列。此队列也按照 FIFO（先进先出）原则对元素进行排序。

   - **PriorityBlockingQueue**：支持优先级排序的无界阻塞队列。元素按照指定的比较器（Comparator）或自然顺序进行排序。不保证具有相同优先级的元素的顺序。

   - **DelayQueue**：使用优先级队列实现的无界阻塞队列。仅包含实现了 Delayed 接口的元素，元素只能在其延迟到期后被取出。适用于实现延迟相关的功能，如定时任务。

   - **SynchronousQueue**：一种不存储元素的阻塞队列。每个插入操作必须等待一个相应的移除操作，反之亦然。适用于传递性的任务分配。

   - **LinkedTransferQueue**：一种基于链表的无界阻塞队列。提供了 transfer 方法，用于在需要时等待元素的消费者。

   - **LinkedBlockingDeque**：基于链表的可选有界阻塞双端队列（Deque）。支持从两端插入和移除元素。

6. **threadFactory**：用于创建新线程的工厂。可以通过这个参数来定制线程的创建方式，例如设置线程的名称、优先级等。

7. **handler**：当线程池无法处理新任务时的策略。常见的策略有 `ThreadPoolExecutor.AbortPolicy`（直接抛出异常）、`ThreadPoolExecutor.CallerRunsPolicy`（调用者所在线程执行任务）、`ThreadPoolExecutor.DiscardOldestPolicy`（丢弃队列中最老的任务）和 `ThreadPoolExecutor.DiscardPolicy`（直接丢弃任务），这些策略都实现了 `RejectedExecutionHandler` 接口。

   - **AbortPolicy**（默认策略）: 当线程池无法处理新任务时，它直接抛出 `RejectedExecutionException` 异常。这种策略可以保护系统免受大量新任务的冲击，但可能会中断当前任务的执行流程。

   - **CallerRunsPolicy**: 如果线程池无法处理新任务，那么该策略会在调用者的线程中直接运行任务。这实际上是一种调节机制，将部分任务回退给调用者执行，从而降低了新任务的流量。这种策略适用于那些并发量比较小，性能要求不高，且不允许失败的业务场景。

   - **DiscardPolicy**: 这种策略会直接丢弃无法处理的新任务，但不抛出任何异常。它适用于那些无关紧要的任务，例如文章的阅读量统计等。

   - **DiscardOldestPolicy**: 当线程池无法处理新任务时，这种策略会丢弃工作队列中最老的任务，并将新任务添加到队列中。这种策略适用于那些允许丢弃最老任务，以便为新任务腾出空间的业务场景。但需要注意的是，这种策略可能会导致业务数据的不一致。

   除了这些内置的拒绝策略外，还可以自定义拒绝策略来满足特定的业务需求。自定义的拒绝策略需要实现 `RejectedExecutionHandler` 接口，并重写 `rejectedExecution` 方法来定义具体的执行逻辑。

   在实际应用中，选择哪种拒绝策略取决于具体的业务场景和需求。例如，对于关键业务，可能需要选择抛出异常的 `AbortPolicy`；而对于那些非关键业务，可以选择丢弃任务或回退给调用者执行。同时，还需要考虑系统的性能、资源利用率以及任务的重要性等因素。

## 2 Java 线程池工作过程

Java线程池的工作过程可以概括为以下几个步骤：

1. **创建线程池**：首先，需要创建一个线程池实例。这可以通过`ThreadPoolExecutor`类或其子类来实现，也可以使用`Executors`类提供的工厂方法来简化创建过程。在创建线程池时，需要指定一些核心参数，如核心线程数（corePoolSize）、最大线程数（maximumPoolSize）、任务队列（workQueue）等。
2. **提交任务**：一旦线程池创建完成，就可以将任务提交给线程池执行。任务通常实现`Runnable`接口或`Callable`接口。提交任务可以通过`execute()`方法（适用于`Runnable`任务）或`submit()`方法（适用于`Callable`任务）来实现。提交的任务会被线程池中的线程执行。
3. **线程执行任务**：线程池中的线程会不断从任务队列中取出任务并执行。当线程池中的线程数量小于核心线程数时，会优先创建新线程执行任务。当线程数量达到核心线程数后，新提交的任务会被放入任务队列中等待执行。如果任务队列已满，且线程数量小于最大线程数，则会创建非核心线程执行任务。如果任务队列已满且线程数量达到最大线程数，线程池会根据设定的拒绝策略来处理新提交的任务，如抛出异常、丢弃任务等。
4. **线程回收与复用**：当线程完成任务后，它们不会立即被销毁，而是会等待一段时间（keepAliveTime），以便复用。如果在这段时间内没有新的任务提交给线程池，且线程池中的线程数量大于核心线程数，那么多余的线程会被销毁以释放资源。如果后续有新的任务提交，线程池会优先复用已有的线程而不是创建新线程。
5. **关闭线程池**：当不再需要线程池时，应该显式地关闭线程池以释放资源。这可以通过调用`shutdown()`方法来实现平缓关闭（等待所有任务执行完毕后再关闭线程池），或者通过调用`shutdownNow()`方法来立即关闭线程池（尝试停止所有正在执行的任务并返回等待执行的任务列表）。

## 3 常见的线程池

Java 提供了几种类型的线程池，这些线程池在 `java.util.concurrent` 包中定义。下面列出了 Java 中的主要线程池类型及其用途：

1. **FixedThreadPool**
   - **原理**：创建一个固定大小的线程池，核心线程数和最大线程数相等。它有一个有界的任务队列（通常默认是`LinkedBlockingQueue`）。当线程数量达到上限时，新提交的任务会被放入队列中等待执行。
   - **适用场景**：适用于资源有限或系统对资源消耗有严格要求的情况，如数据库连接池大小固定。需要控制并发数，防止过多的线程导致系统负载过重。
2. **CachedThreadPool**
   - **原理**：根据需要创建新线程，并且可以灵活地回收空闲线程。其核心线程数为0，最大线程数为`Integer.MAX_VALUE`，即理论上没有限制。它使用`SynchronousQueue`作为任务队列，这是一个无界但不存储元素的队列，每个插入操作必须等待另一个线程的移除操作。当线程空闲超过60秒后会被终止并从线程池中移除，从而实现动态调整线程数量的目的。
   - **适用场景**：适用于执行大量短生命周期的任务，且任务的执行时间远小于线程创建销毁的时间开销。对于并发执行的数量需求不确定，适合处理突发性的高并发请求。
3. **ScheduledThreadPool**
   - **原理**：用于执行定时任务和具有固定延迟的任务。其核心线程数可自定义，非核心线程闲置时会被回收，但不会低于核心线程数。它支持延迟或定期执行`Runnable`或`Callable`任务，并提供了`schedule()`和`scheduleAtFixedRate()`等方法来安排任务执行。
   - **适用场景**：适用于定时任务调度，如每隔一定时间执行一次清理、统计或其他后台服务的操作。也适用于周期性任务执行，如心跳检测、数据同步等。
4. **SingleThreadExecutor**
   - **原理**：创建一个只有一个工作线程的线程池。提交到该线程池的任务将按照FIFO（先进先出）顺序执行，确保所有任务在一个线程上按序完成。即使有多个任务提交，也不会同时执行，而是排队等待当前任务完成后才开始下一个任务。
   - **适用场景**：适用于任务执行顺序有严格要求的环境，确保任务间串行执行。

![线程池各个参数的关系](https://oss.javaguide.cn/github/javaguide/java/concurrent/relationship-between-thread-pool-parameters.png)

# 线程池的核心线程会被回收吗

`ThreadPoolExecutor` 默认不会回收核心线程，即使它们已经空闲了。这是为了减少创建线程的开销，因为核心线程通常是要长期保持活跃的。但是，如果线程池是被用于周期性使用的场景，且频率不高（周期之间有明显的空闲时间），可以考虑将 `allowCoreThreadTimeOut(boolean value)` 方法的参数设置为 `true`，这样就会回收空闲（时间间隔由 `keepAliveTime` 指定）的核心线程了。

```java
public void allowCoreThreadTimeOut(boolean value) {
    // 核心线程的 keepAliveTime 必须大于 0 才能启用超时机制
    if (value && keepAliveTime <= 0) {
        throw new IllegalArgumentException("Core threads must have nonzero keep alive times");
    }
    // 设置 allowCoreThreadTimeOut 的值
    if (value != allowCoreThreadTimeOut) {
        allowCoreThreadTimeOut = value;
        // 如果启用了超时机制，清理所有空闲的线程，包括核心线程
        if (value) {
            interruptIdleWorkers();
        }
    }
}
```



