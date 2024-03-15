### LinkedBlockingQueue

`LinkedBlockingQueue` 底层基于**单向链表**实现的阻塞队列，可以当做无界队列也可以当做有界队列来使用，满足 FIFO 的特性.

与 `ArrayBlockingQueue` 相比起来具有更高的吞吐量。

> **在大部分并发场景下，LinkedBlockingQueue的吞吐量比ArrayBlockingQueue更高**。这主要是因为LinkedBlockingQueue使用双锁可并行读写，而ArrayBlockingQueue在插入或删除元素时需要直接操作数组，可能会产生额外的开销。

通常在创建 `LinkedBlockingQueue` 对象时，会指定其大小，如果未指定，容量等于 `Integer.MAX_VALUE` (可能会导致损耗大量内存，所以一般都会初始化大小)。

**相关构造方法:**

```java
    /**
     *某种意义上的无界队列
     * Creates a {@code LinkedBlockingQueue} with a capacity of
     * {@link Integer#MAX_VALUE}.
     */
    public LinkedBlockingQueue() {
        this(Integer.MAX_VALUE);
    }

    /**
     *有界队列
     * Creates a {@code LinkedBlockingQueue} with the given (fixed) capacity.
     *
     * @param capacity the capacity of this queue
     * @throws IllegalArgumentException if {@code capacity} is not greater
     *         than zero
     */
    public LinkedBlockingQueue(int capacity) {
        if (capacity <= 0) throw new IllegalArgumentException();
        this.capacity = capacity;
        last = head = new Node<E>(null);
    }
```

