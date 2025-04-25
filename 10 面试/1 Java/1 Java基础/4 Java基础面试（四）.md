# 1 介绍一下序列化和反序列化

- 序列化（Serialization）是指将对象的状态信息转换为可以存储或传输的形式的过程。在序列化过程中，对象将其当前状态写入到临时或持久性存储区。具体来说，序列化是将对象实例的公共字段和私有字段以及类的名称（包括类所在的程序集）转换为字节流，然后再把字节流写入数据流。序列化使其他代码可以查看或修改那些不序列化便无法访问的对象实例数据。

- 反序列化（Deserialization）是指将序列化后的数据还原成原始数据类型的过程。即将之前序列化保存的字节流用来恢复对象，也就是说把字节序列恢复为对象的过程。通过反序列化，可以从存储的字节流中还原对象的状态，或者在分布式系统中，将字节序列转换为可操作的对象，或者实现远程方法调用。

序列化和反序列化的过程结合起来，可以轻松地存储和传输数据，使对象可以在不同的环境之间进行传输，并在接收端还原成原始数据类型，从而实现数据的持久化、远程调用等功能。

# 2 serialVersionUID作用

`serialVersionUID`是Java中序列化机制的一部分，用于**验证版本一致性**。在进行序列化操作时，`serialVersionUID`用于标识当前对象的版本，并将其写入到序列化文件中。当反序列化时，JVM会将文件中的`serialVersionUID`与本地相应实体（类）的`serialVersionUID`进行比较，如果相同，则认为版本一致，可以进行反序列化；否则，会抛出序列化版本不一致的异常。







# 10 Comparable 和 Comparator 的区别

`Comparable`和`Comparator`都是Java中用于比较对象并对其进行排序的接口，但它们之间存在一些关键的区别。

1. 接口定义的方法不同：Comparable接口定义了一个方法`compareTo(T o)`，而Comparator接口定义了一个方法`compare(T o1, T o2)`。
2. 内部与外部的比较：Comparable是一个内部比较接口，通常用于对象本身已经支持自比较的情况，如String、Integer等。而Comparator是一个外部比较接口，通常用于对象没有实现排序接口但需要进行排序的情况。
3. 排序的实现位置：Comparable的排序方法是在集合内部定义的，而Comparator的排序方法是在集合外部实现的。因此，要实现排序，需要在集合外定义Comparator接口的方法或在集合内实现Comparable接口的方法。
4. 位置和包：Comparable接口位于java.lang包中，而Comparator接口位于java.util包中。

总的来说，Comparable和Comparator的主要区别在于它们的比较方式、排序的实现位置以及接口定义的方法。Comparable是内部比较接口，通常用于对象本身已经支持自比较的情况，而Comparator是外部比较接口，用于对象没有实现排序接口但需要进行排序的情况。

# 11 CompletableFuture 类有什么用？

CompletableFuture类是Java 8中引入的一个新特性，它是对Future和CompletionStage接口的实现，并提供了函数式编程的能力。CompletableFuture类的主要用途包括：

1. **异步编程**：CompletableFuture支持异步编程，允许你非阻塞地执行代码，从而提高程序的响应性和吞吐量。通过CompletableFuture，你可以轻松地将任务提交给线程池执行，并在任务完成时获取结果。
2. **任务编排**：CompletableFuture提供了强大的任务编排能力，允许你以链式方式组合多个异步任务，并定义它们之间的依赖关系。这使得你可以更灵活地控制任务的执行顺序，实现复杂的异步操作。
3. **异常处理**：CompletableFuture提供了统一的异常处理机制。当异步任务抛出异常时，你可以通过CompletableFuture的异常处理方法来获取和处理这些异常，而无需在每个任务中显式地处理异常。
4. **结果转换**：CompletableFuture允许你对异步任务的结果进行转换。例如，你可以将一个异步任务的结果作为另一个异步任务的输入，从而实现结果的传递和转换。
5. **函数式编程支持**：CompletableFuture提供了函数式编程的能力，允许你使用Lambda表达式和方法引用来定义任务和执行操作。这使得代码更加简洁和易读。

总之，CompletableFuture类是Java 8中引入的一个强大的工具，用于简化异步编程和任务编排。它提供了异步编程、任务编排、异常处理、结果转换和函数式编程等功能，使得在Java中进行异步操作更加简单和高效。

# 20 JAVA守护线程

在Java中，后台线程（Daemon Thread）是一种特殊的线程，它与前台线程（User Thread）的主要区别在于它们的行为和应用程序退出时的处理方式。

## 20.1 后台线程的特点

1. **守护性质**：后台线程是守护线程，也被称为守护进程。这意味着当所有的前台线程都结束时，JVM（Java虚拟机）会自动退出，不会等待后台线程完成。
2. **自动结束**：如果后台线程是守护线程，并且所有的前台线程都已经结束，那么JVM会退出，此时后台线程会被突然终止，可能导致一些资源没有被正确释放或数据不一致的问题。
3. **线程类型**：在Java中，可以通过调用`Thread`类的`setDaemon(true)`方法将线程设置为后台线程。需要注意的是，必须在调用`start()`方法之前设置线程为守护线程，否则会抛出`IllegalThreadStateException`异常。
4. **用途**：后台线程通常用于执行一些后台任务，如垃圾回收、资源清理等。这些任务不需要等待主程序执行完毕，而且在主程序结束后它们可以自动结束。

## 20.2 前台线程与后台线程的比较：

- **前台线程**：主线程、通过`new Thread()`创建的线程默认都是前台线程。只要有一个前台线程在运行，JVM就不会退出。
- **后台线程**：通过`setDaemon(true)`设置为守护线程。当所有前台线程都结束时，JVM会自动退出，不会等待后台线程执行完毕。

下面是一个简单的示例，演示如何创建和启动一个后台线程：

```java
public class DaemonThreadExample {
    public static void main(String[] args) {
        // 创建一个线程
        Thread daemonThread = new Thread(() -> {
            while (true) {
                System.out.println("Daemon Thread is running...");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });

        // 将线程设置为守护线程
        daemonThread.setDaemon(true);

        // 启动线程
        daemonThread.start();

        // 主线程休眠一段时间后结束
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("Main Thread is exiting...");
    }
}
```

在这个示例中，我们创建了一个无限循环的后台线程，每隔一秒打印一条消息。主线程休眠5秒后结束。由于`daemonThread`被设置为守护线程，并且所有前台线程（这里只有主线程）都已结束，因此JVM会在主线程结束后立即退出，不会等待`daemonThread`线程完成。所以，你可能会看到"Daemon Thread is running..."的消息只打印了几次，然后程序就退出了。

