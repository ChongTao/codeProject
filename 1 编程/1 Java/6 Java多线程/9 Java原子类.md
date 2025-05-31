`java.util.concurrent.atomic` 包是 Java 并发库中的一个重要部分，它提供了一组原子变量类，这些类可以用于在多线程环境中实现无锁编程。原子变量类保证了对其单个操作的原子性，这意味着这些操作在执行过程中不会被其他线程打断，从而避免了多线程竞态条件的问题。

原子变量类的实现主要依赖于硬件级别的原子操作，以及 Java 内存模型（Java Memory Model, JMM）提供的 `volatile` 语义和内存屏障（Memory Barrier）来保证操作的原子性和可见性。

## 1.1 原子性保证

原子变量类（如 `AtomicInteger`、`AtomicLong`、`AtomicReference` 等）通常使用了 CAS（Compare-And-Swap）操作来实现原子性。CAS 是一种由硬件支持的原子操作，它包含三个操作数：内存位置（V）、预期原值（A）和更新值（B）。CAS 会将内存位置 V 的值与预期原值 A 进行比较，如果相匹配，那么处理器会自动将该内存位置 V 的值更新为 B。如果不相匹配，处理器不做任何操作。

在 Java 中，CAS 操作是通过 `sun.misc.Unsafe` 类提供的 `compareAndSwap` 方法来实现的。`Unsafe` 类是 Java 的一个非公开类，它提供了许多可以直接操作内存的方法，包括 CAS 操作。

## 1.2 可见性保证

原子变量类还使用了 Java 内存模型提供的 volatile 语义来保证可见性。在 Java 中，对 volatile 变量的读写操作会具有特殊的内存屏障效果，可以确保线程之间的内存可见性。

当一个线程修改了一个 volatile 变量的值，这个修改会立即同步到主内存，并且其他线程会立即看到这个修改。因此，当多个线程共享一个原子变量时，每个线程都能看到其他线程对该变量的修改。

## 1.3 内存屏障

内存屏障（Memory Barrier）是一种特殊的指令，用于确保指令的执行顺序。在原子变量类的实现中，内存屏障被用来防止指令重排序，确保操作的原子性和可见性。



# ThreadLocal 

通常情况下，我们创建的变量可以被任何一个线程访问和修改。这在多线程环境中可能导致数据竞争和线程安全问题。那么，**如果想让每个线程都有自己的专属本地变量，该如何实现呢？**

JDK 中提供的 `ThreadLocal` 类正是为了解决这个问题。**`ThreadLocal` 类允许每个线程绑定自己的值**，可以将其形象地比喻为一个“存放数据的盒子”。每个线程都有自己独立的盒子，用于存储私有数据，确保不同线程之间的数据互不干扰。

当你创建一个 `ThreadLocal` 变量时，每个访问该变量的线程都会拥有一个独立的副本。这也是 `ThreadLocal` 名称的由来。线程可以通过 `get()` 方法获取自己线程的本地副本，或通过 `set()` 方法修改该副本的值，从而避免了线程安全问题。

```java
public class Thread implements Runnable {
    //......
    //与此线程有关的ThreadLocal值。由ThreadLocal类维护
    ThreadLocal.ThreadLocalMap threadLocals = null;

    //与此线程有关的InheritableThreadLocal值。由InheritableThreadLocal类维护
    ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
    //......
}
```

从上面`Thread`类 源代码可以看出`Thread` 类中有一个 `threadLocals` 和 一个 `inheritableThreadLocals` 变量，它们都是 `ThreadLocalMap` 类型的变量,我们可以把 `ThreadLocalMap` 理解为`ThreadLocal` 类实现的定制化的 `HashMap`。默认情况下这两个变量都是 null，只有当前线程调用 `ThreadLocal` 类的 `set`或`get`方法时才创建它们，实际上调用这两个方法的时候，我们调用的是`ThreadLocalMap`类对应的 `get()`、`set()`方法。

`ThreadLocal`类的`set()`方法

```java
public void set(T value) {
    //获取当前请求的线程
    Thread t = Thread.currentThread();
    //取出 Thread 类内部的 threadLocals 变量(哈希表结构)
    ThreadLocalMap map = getMap(t);
    if (map != null)
        // 将需要存储的值放入到这个哈希表中
        map.set(this, value);
    else
        createMap(t, value);
}
ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}
```

通过上面这些内容，我们足以通过猜测得出结论：**最终的变量是放在了当前线程的 `ThreadLocalMap` 中，并不是存在 `ThreadLocal` 上，`ThreadLocal` 可以理解为只是`ThreadLocalMap`的封装，传递了变量值。** `ThrealLocal` 类中可以通过`Thread.currentThread()`获取到当前线程对象后，直接通过`getMap(Thread t)`可以访问到该线程的`ThreadLocalMap`对象。

**每个`Thread`中都具备一个`ThreadLocalMap`，而`ThreadLocalMap`可以存储以`ThreadLocal`为 key ，Object 对象为 value 的键值对。**

```java
ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue) {
    //......
}
```

比如我们在同一个线程中声明了两个 `ThreadLocal` 对象的话， `Thread`内部都是使用仅有的那个`ThreadLocalMap` 存放数据的，`ThreadLocalMap`的 key 就是 `ThreadLocal`对象，value 就是 `ThreadLocal` 对象调用`set`方法设置的值。

`ThreadLocal` 数据结构如下图所示：

![ThreadLocal 数据结构](https://oss.javaguide.cn/github/javaguide/java/concurrent/threadlocal-data-structure.png)
