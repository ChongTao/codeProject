`ReentrantLock` 是 Java 并发包（`java.util.concurrent.locks`）中提供的**可重入独占锁**，比传统的 `synchronized` 关键字更灵活、功能更丰富，也称为递归锁，是一种线程同步机制，其特点是一个线程可以多次获取同一个锁而不产生死锁。这种锁机制在 Java 中由 `ReentrantLock` 类实现，同时也体现在 `synchronized` 关键字的使用上。

## 1.源码解析

```java
public class ReentrantLock implements Lock, java.io.Serializable
```

- 核心结构Sync类是公平和非公平锁的基类

  ```java
  abstract static class Sync extends AbstractQueuedSynchronizer
  ```

  - NonfairSync：非公平锁实现，允许线程插队。

  - FairSync：公平锁实现，按请求顺序获取锁。

    ```java
    static final class NonfairSync extends Sync
    static final class FairSync extends Sync
    ```

- **锁模式**：通过构造函数指定

  ```java
  public ReentrantLock(boolean fair) {
      sync = fair ? new FairSync() : new NonfairSync();
  }
  ```

- **获取锁流程**

  - **非公平锁（NonfairSync）**

    - **`lock()` 方法**

      ```java
      final void lock() {
          if (compareAndSetState(0, 1)) // 直接尝试 CAS 抢锁
              setExclusiveOwnerThread(Thread.currentThread());
          else
              acquire(1); // 进入 AQS 队列
      }
      ```

    - **tryAcquire()**：调用 nonfairTryAcquire：

      ```java
      final boolean nonfairTryAcquire(int acquires) {
          if (state == 0 && compareAndSetState(0, acquires)) {
              setExclusiveOwnerThread(currentThread());
              return true;
          } else if (currentThread() == getExclusiveOwnerThread()) {
              // 重入：state += acquires
              int nextc = state + acquires;
              setState(nextc);
              return true;
          }
          return false;
      }
      ```

      

  - **公平锁（FairSync）**

    - lock() 方法：直接调用 acquire(1)，不插队。

    - tryAcquire()：

      ```java
      protected final boolean tryAcquire(int acquires) {
          if (state == 0) {
              // 关键区别：检查是否有前驱节点
              if (!hasQueuedPredecessors() && compareAndSetState(0, acquires)) {
                  setExclusiveOwnerThread(currentThread());
                  return true;
              }
          } else if (currentThread() == getExclusiveOwnerThread()) {
              // 重入逻辑与非公平锁相同
          }
          return false;
      }
      ```

- **释放锁流程**

  - **`unlock()` 方法**：调用 `sync.release(1)`。

  - **`tryRelease()`**：

    ```java
    protected final boolean tryRelease(int releases) {
        int c = state - releases;
        if (Thread.currentThread() != getExclusiveOwnerThread())
            throw new IllegalMonitorStateException();
        boolean free = false;
        if (c == 0) { // 完全释放锁
            free = true;
            setExclusiveOwnerThread(null);
        }
        setState(c); // 更新 state
        return free;
    }
    ```

- **关键机制**

  - 重入性：通过 state 记录重入次数，同一线程每次获取锁时 state 递增。
  - 队列管理：AQS 维护一个 CLH 队列，未获取锁的线程被包装为 Node 加入队列等待。
  - 公平性：公平锁通过 hasQueuedPredecessors() 确保无前驱节点才尝试获取锁。

## 2 ReentrantLock原理

ReentrantLock是Java中提供的一种可重入的互斥锁，它支持公平锁和非公平锁两种模式，允许在并发编程中实现更灵活和强大的线程同步。与Java内置的synchronized关键字相比，ReentrantLock提供了更多的功能和灵活性。

ReentrantLock的原理主要基于CAS（Compare-And-Swap）操作和AQS（AbstractQueuedSynchronizer）队列来实现。AQS是一个基于FIFO（先进先出）队列的同步器框架，用于构建锁或其他同步组件。ReentrantLock内部通过继承AQS并实现其相关方法来管理锁的获取和释放。

在ReentrantLock中，每个线程在尝试获取锁时，会首先通过CAS操作尝试修改AQS的同步状态（state）。如果当前没有线程持有锁（即state为0），则CAS操作成功，将state设置为1，表示当前线程获取了锁。如果CAS操作失败，说明有其他线程已经持有锁，则当前线程会尝试加入AQS队列等待获取锁。

ReentrantLock支持可重入性，即同一个线程可以多次获取同一个锁。这是通过维护一个计数器来实现的，当线程获取锁时，计数器加1；当线程释放锁时，计数器减1。只有当计数器为0时，才表示没有线程持有该锁，此时其他线程才能获取该锁。

ReentrantLock还提供了公平锁和非公平锁两种模式。在公平锁模式下，线程按照获取锁的顺序来获取锁，即先来的线程先获取锁。这是通过AQS队列来实现的，线程在获取锁时会首先判断队列中是否有前驱节点，如果有则等待，否则尝试获取锁。而在非公平锁模式下，线程可以随机获取锁，这可能会导致某些线程“饥饿”，即长时间无法获取锁。

总的来说，ReentrantLock的原理是通过CAS操作和AQS队列来实现锁的获取和释放，支持可重入性、公平锁和非公平锁等特性，为Java并发编程提供了更强大和灵活的线程同步机制。

## 4 应用场景

可重入锁特别适用于需要递归访问共享资源的场景。例如，一个方法需要调用另一个需要相同锁的方法时，或者一个线程需要多次进入临界区代码时。在这种情况下，使用可重入锁可以避免死锁和简化编程模型。

此外，可重入锁也适用于那些需要长时间持有锁的场景，因为在这种情况下，线程可能会在执行过程中多次请求相同的锁。使用可重入锁可以确保线程在多次请求锁时不会造成死锁。

需要注意的是，虽然可重入锁提供了很大的灵活性，但在使用时仍需要谨慎，避免过度使用导致性能下降或死锁等问题。在决定使用可重入锁时，需要权衡其优缺点，并根据实际情况做出决策。



## 5 实现一个可重入独占锁

在Java中，可以通过以下步骤实现一个简单的**可重入独占锁**（类似`ReentrantLock`的核心逻辑），支持同一线程重复加锁和释放锁：

**1. 核心设计**

- **锁状态**：使用`volatile`变量`state`记录锁的持有次数（支持重入）。
- **持有线程**：记录当前持有锁的线程。
- **同步机制**：通过`wait/notify`或`CAS`（原子操作）实现线程阻塞和唤醒。

****

**2.代码实现**

```java
public class SimpleExclusiveLock implements Lock {
    // 锁状态：0表示未锁定，>0表示被同一线程重入的次数
    private volatile int state = 0;
    // 当前持有锁的线程
    private Thread owner;
    
    // 加锁（支持可重入）
    @Override
    public synchronized void lock() {
        Thread current = Thread.currentThread();
        // 自旋等待锁释放
        while (state != 0 && owner != current) {
            try {
                wait(); // 阻塞当前线程
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
        owner = current;
        state++;
    }

    // 解锁
    @Override
    public synchronized void unlock() {
        if (Thread.currentThread() != owner) {
            throw new IllegalMonitorStateException("当前线程未持有锁");
        }
        state--;
        if (state == 0) {
            owner = null;
            notifyAll(); // 唤醒等待线程
        }
    }

    // 其他方法（简化实现）
    @Override
    public void lockInterruptibly() throws InterruptedException {
        throw new UnsupportedOperationException();
    }

    @Override
    public boolean tryLock() {
        throw new UnsupportedOperationException();
    }

    @Override
    public boolean tryLock(long time, TimeUnit unit) throws InterruptedException {
        throw new UnsupportedOperationException();
    }

    @Override
    public Condition newCondition() {
        throw new UnsupportedOperationException();
    }
}
```
