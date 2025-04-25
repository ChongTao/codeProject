# 1 介绍 ArrayList 的扩容机制

ArrayList的扩容机制是其在内部实现动态数组时的一个关键特性。当ArrayList中的元素数量超过了其当前容量时，它会自动扩容以容纳更多的元素。这个扩容过程是通过创建一个新的、更大的数组，并将旧数组中的元素复制到新数组中来完成的。

1. **判断是否需要扩容**：当向ArrayList添加元素时，首先会检查当前元素数量是否已经达到了数组的最大容量。如果达到了最大容量，就需要进行扩容。
2. **计算新的容量**：ArrayList的扩容大小通常是当前容量的1.5倍（即`newCapacity = oldCapacity + (oldCapacity >> 1)`）。这是通过右移操作实现的，相当于将当前容量除以2后再加上原容量。然而，有一个上限值，当ArrayList的大小超过`Integer.MAX_VALUE - 8`时，扩容就不再是1.5倍了，而是直接增加到`Integer.MAX_VALUE`。
3. **创建新数组**：根据计算出的新容量，创建一个新的数组。新数组的大小等于计算出的新容量。
4. **元素复制**：将旧数组中的所有元素复制到新数组中。这个过程是从数组的第一个元素开始，逐个复制到新数组的对应位置，直到所有元素都被复制完毕。
5. **更新引用**：将ArrayList内部对数组的引用更新为新创建的数组，这样后续添加的元素就会存储在新数组中。

# 2 介绍 LinkHashMap 原理

`LinkedHashMap` 是 Java 集合框架中的一个类，它是 `HashMap`的一个子类，并且在`HashMap`的基础上维护了一个双向链表来保持元素的插入顺序或者访问顺序。

1. **基于 HashMap**：LinkedHashMap 的底层仍然是基于 HashMap 来实现键值对的存储。HashMap 使用数组和链表（或者红黑树）来存储元素，其中数组用于存储键和值，而链表（或红黑树）用于处理哈希冲突。
2. **双向链表**：LinkedHashMap 在 HashMap 的基础上增加了一个双向链表，用于维护元素的顺序。这个双向链表保证了元素的迭代顺序与插入顺序或者访问顺序一致。每个节点（Entry）在链表中都包含了对前一个节点和后一个节点的引用。
3. **插入顺序和访问顺序**：LinkedHashMap 提供了两种顺序模式：插入顺序和访问顺序。在插入顺序模式下，元素的迭代顺序与它们被插入到映射中的顺序相同。在访问顺序模式下，元素的迭代顺序与它们最近被访问的顺序相同（访问顺序通过记录每次访问来更新链表）。
4. **性能特点**：由于 LinkedHashMap 的底层是 HashMap，因此它支持高效的查找、插入和删除操作，时间复杂度通常为 O(1)。同时，由于双向链表的存在，它还能够按照特定的顺序迭代元素，这使得它在需要保持元素顺序的场景下非常有用。
5. **线程安全**：与 HashMap 和 TreeMap 一样，LinkedHashMap 也不是线程安全的。如果在多线程环境中使用 LinkedHashMap，并且多个线程同时修改它，那么可能需要额外的同步措施来确保数据的完整性。

总之，LinkedHashMap 的底层原理是在 HashMap 的基础上增加了一个双向链表来维护元素的顺序。这使得它能够在保持元素顺序的同时提供高效的查找、插入和删除操作。然而，需要注意的是 LinkedHashMap 也不是线程安全的。

# 3 HashSet底层原理

HashSet 是 Java 集合框架的一部分，用于存储不重复的元素。它的底层原理主要基于 HashMap 来实现。以下是 HashSet 的底层原理：

1. **基于 HashMap**：HashSet 实际上是一个不包含值的 HashMap。在 HashSet 中，元素被存储为 HashMap 的键（key），而对应的值（value）则是一个常量对象（例如，`PRESENT`）。由于 HashMap 的键是唯一的，因此 HashSet 中的元素也是唯一的。
2. **无序性**：HashSet 是无序的，因为它继承了 HashMap 的无序性。HashMap 的键（即 HashSet 中的元素）是无序存储的，因此从 HashSet 中迭代元素时，元素的顺序可能与它们被添加到集合中的顺序不同。
3. **允许 null 元素**：HashSet 允许包含一个 null 元素，因为 HashMap 允许键为 null。但是，由于 HashSet 是基于 HashMap 的键来存储元素的，因此它只能包含一个 null 元素。
4. **非线程安全**：与 HashMap 一样，HashSet 也是非线程安全的。如果在多线程环境中使用 HashSet，并且多个线程同时修改它，那么可能需要额外的同步措施来确保数据的完整性。

# 4 TreeMap底层原理

TreeMap 是 Java 集合框架中的一部分，它实现了一个基于红黑树（Red-Black tree）的 SortedMap 接口。TreeMap 用于存储键值对，并且按键的自然顺序或者根据创建 TreeMap 时提供的 Comparator 进行排序。以下是 TreeMap 的底层原理：

1. **基于红黑树**：TreeMap 的底层数据结构是一个红黑树，这是一种自平衡的二叉搜索树。红黑树具有一些特性，如每个节点要么是红色，要么是黑色，并且从根到叶子的所有路径上不能有两个连续的红色节点。这些特性保证了树的平衡性，从而确保了 TreeMap 中的元素按照键的顺序排列。
2. **键值对存储**：TreeMap 中的元素是键值对（Key-Value），键是唯一的，而值可以重复。红黑树的节点存储了键值对，其中键用于排序和唯一性检查，值是与键相关联的数据。
3. **排序**：TreeMap 中的元素按照键的自然顺序进行排序，或者根据创建时提供的 Comparator 进行排序。这使得 TreeMap 能够支持高效的按键查找、插入和删除操作。
4. **不允许 null 键**：与 HashSet 和 TreeSet 不同，TreeMap 不允许使用 null 作为键（Key），因为红黑树不支持存储 null 值作为键。但是，TreeMap 允许使用 null 作为值（Value）。
5. **效率**：由于 TreeMap 的底层是红黑树，因此它支持高效的查找、插入和删除操作，时间复杂度通常为 O(log n)，其中 n 是 TreeMap 中元素的数量。
6. **线程安全**：与 HashSet 和 TreeSet 一样，TreeMap 也不是线程安全的。如果在多线程环境中使用 TreeMap，并且多个线程同时修改它，那么可能需要额外的同步措施来确保数据的完整性。

总之，TreeMap 的底层原理主要基于红黑树数据结构，利用红黑树的自平衡和排序特性来实现有序的键值对存储。然而，需要注意的是 TreeMap 不允许使用 null 作为键，并且它不是线程安全的。

# 5 JDK 1.7 和 JDK 1.8 的 ConcurrentHashMap 实现有什么不同？

JDK 1.7和JDK 1.8的ConcurrentHashMap实现之间存在几个主要的不同点：

1. **底层数据结构**：
   - **JDK 1.7**：ConcurrentHashMap使用了一个称为Segment的数组来分割整个哈希表。每个Segment都包含了一个HashEntry数组，这些数组以链表的形式存储元素。Segment是ReentrantLock的子类，充当了锁的角色，可以锁住一段哈希表结构。
   - **JDK 1.8**：ConcurrentHashMap摒弃了Segment，转而使用Node数组、链表和红黑树来实现。Node数组用于存放树或链表的头结点。当链表中的节点数量达到一个特定阈值时，链表会转化为红黑树，以提高查询效率。
2. **锁机制**：
   - **JDK 1.7**：ConcurrentHashMap使用了基于Segment的分段锁机制。每个Segment都有自己的锁，当一个线程访问某个Segment时，其他线程可以访问其他Segment，从而实现了真正的并发访问。
   - **JDK 1.8**：ConcurrentHashMap采用了CAS（Compare-and-Swap）操作和synchronized关键字来保证线程安全。这种方式的锁粒度更小，只有在CAS操作失败时才需要加锁，从而提高了性能。
3. **查询时间复杂度**：
   - **JDK 1.7**：由于使用链表作为存储结构，查询时间复杂度为O(n)。
   - **JDK 1.8**：将链表转化为红黑树后，查询时间复杂度变为O(logN)。

# 6 TreeSet底层原理

TreeSet 是 Java 集合框架中的一部分，它实现了一个基于红黑树（Red-Black tree）的 NavigableSet 接口。TreeSet 是一种有序的集合，用于存储不重复的元素。以下是 TreeSet 的底层原理：

1. **基于红黑树**：TreeSet 的底层数据结构是一个红黑树，这是一种自平衡的二叉搜索树。红黑树具有一些特性，如每个节点要么是红色，要么是黑色，并且从根到叶子的所有路径上不能有两个连续的红色节点。这些特性保证了树的平衡性，从而确保了 TreeSet 中的元素按照升序排列。
2. **唯一性**：TreeSet 中的元素是唯一的，这是因为它依赖于红黑树的特性来确保元素的唯一性。当尝试向 TreeSet 中添加一个已经存在的元素时，红黑树会检测到这一点并拒绝添加，因此 TreeSet 中的元素始终保持唯一。
3. **排序**：由于 TreeSet 的底层是红黑树，因此元素在添加时会按照自然顺序（或者根据创建 TreeSet 时提供的 Comparator）进行排序。这使得 TreeSet 中的元素始终按照升序排列。
4. **不允许 null 元素**：与 HashSet 不同，TreeSet 不允许包含 null 元素。这是因为红黑树不支持存储 null 值作为键。
5. **线程安全**：TreeSet 本身并不是线程安全的。如果在多线程环境中使用 TreeSet，并且多个线程同时修改它，那么可能需要额外的同步措施来确保数据的完整性。

总之，TreeSet 的底层原理主要基于红黑树数据结构，利用红黑树的自平衡和排序特性来实现有序、唯一的元素存储。然而，需要注意的是 TreeSet 不允许包含 null 元素，并且它不是线程安全的。

# 10 ConcurrentHashMap 为什么 key 和 value 不能为 null？

实际上，ConcurrentHashMap 允许 null 作为 key 和 value。在 ConcurrentHashMap 的实现中，并没有对 key 或 value 是否为 null 进行检查或限制。因此，你可以将 null 作为 key 或 value 插入到 ConcurrentHashMap 中。

然而，需要注意的是，虽然 ConcurrentHashMap 本身允许 null 值，但在使用它时仍需要谨慎处理 null 值的情况。这是因为在某些操作中，null 值可能会导致一些意想不到的结果或问题。

例如，当使用 `get()` 方法根据 key 获取值时，如果 key 为 null，那么将会返回 null。这可能会导致与期望不符的结果，特别是当期望的返回值是某个特定对象而不是 null 时。

另外，当使用 `remove()` 方法删除键值对时，如果提供的 key 为 null，那么将会删除所有 key 为 null 的键值对。这可能会导致误删除其他重要的键值对。

因此，虽然 ConcurrentHashMap 允许 null 值，但在使用它时仍需要仔细考虑和处理 null 值的情况，以避免出现意外的问题。最佳实践是尽量避免使用 null 作为 key 或 value，或者在使用前进行适当的检查和处理。
