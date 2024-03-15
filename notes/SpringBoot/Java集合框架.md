# Collection相关

如何选用集合：

需要根据键值获取到元素值时，选用 `Map` 接口下的集合

- 需要排序时选择 `TreeMap`
- 不需要排序时就选择 `HashMap`
- 需要保证线程安全就选用 `ConcurrentHashMap`

只需要存放元素值时，选择实现`Collection` 接口的集合：

- 需要保证元素唯一时选择实现 `Set` 接口的集合比如 `TreeSet(有序，唯一)` 或 `HashSet(无序，唯一)`
- 不需要就选择实现 `List` 接口的比如 `ArrayList` 或 `LinkedList`

## List

### ArrayList （线程不安全）

> 可以存储任何类型的对象，包括 `null` 值。不过，不建议向`ArrayList` 中添加 `null` 值， `null` 值无意义，会让代码难以维护比如忘记做判空处理就会导致空指针异常。

```java
System.out.println(Arrays.asList("element1", "element2").toArray().getClass() == Object[].class);
//false
```

当我们调用 List的 toArray() 时，我们实际上调用的是其具体实现类中重写的 String[] toArray() 方法。因此，返回数组并不是 Object[] 而是 String[]，而向下转型是不安全的，因此会抛出异常。

通过分析这个官方bug，我们就记住了：

1. 抽象类(接口)其具体类型，取决于实例化时所采用的导出类的类型。
2. 子类实现和父类同名的方法，仅仅返回值不一致时，默认调用的是子类的那个实现方法。如String[] toArray()

> 有了垃圾收集器并不意味着一定不会有内存泄露。 对象能否被GC的依据是 是否还有引用指向它，remove后，如果不手动赋值为null，除非对应位置被其它元素覆盖，否则原来的对象就一直不会回收。

### LinkedList

双向链表(JDK1.6 之前为循环链表，JDK1.7 取消了循环)

`RandomAccess` 是一个标记接口，用来表明实现该接口的类支持随机访问（即可以通过索引快速访问元素）。由于 `LinkedList` 底层数据结构是链表，内存地址不连续，只能通过指针来定位，不支持随机快速访问，所以不能实现 `RandomAccess` 接口。`ArrayList` 底层是数组，所以实现了 `RandomAccess` 接口，表明了他具有快速随机访问功能。 

> `RandomAccess` 接口只是标识，并不是说 `ArrayList` 实现 `RandomAccess` 接口才具有快速随机访问功能的！
>
> 在 Collections的`binarySearch（)` 方法中，它要判断传入的 list 是否 `RandomAccess` 的实例，如果是，调用`indexedBinarySearch()`方法，如果不是，那么调用`iteratorBinarySearch()`方法。
>
> 在项目中一般是不会使用到 `LinkedList` 的，需要用到 `LinkedList` 的场景几乎都可以使用 `ArrayList` 来代替，并且，性能通常会更好。

### Vector 和 Stack 的区别

- `Vector` 和 `Stack` 两者都是线程安全的，都是使用 `synchronized` 关键字进行同步处理。
- `Stack` 继承自 `Vector`，是一个后进先出的栈，而 `Vector` 是一个列表。

已经被淘汰，推荐使用并发集合类（ `ConcurrentHashMap`、`CopyOnWriteArrayList`）



## Queue

### ArrayDeque

> 底层通过循环数组实现。
>
> 是非线程安全的
>
> 不允许放入null元素；
>
> // 由于`ArrayDeque`中不允许放入`null`，当`elements[head] == null`时，意味着容器为空。

```Java
(tail + 1) & (elements.length - 1)
//由于elements.length一定为2的指数，所以elements.length - 1的二进制低位全为1，相与后起到了取模的作用；
//如果前者为-1，那就相当于
```

### *PriorityQueue*

> 不允许放入null元素，是通过完全二叉树实现的`小根堆`，即可以通过数组来作为*PriorityQueue*的底层实现。
>
> 移除队首元素时，除非队列已经为空，否则都需要对小根堆进行维护，维护后，方能取出剩余的元素。
>

> `remove(Object o)`方法用于删除队列中跟`o`相等的某一个元素(如果有多个相等，只删除一个)
>
> 分为2种情况: 
>
> - 1. 删除的是最后一个元素。直接删除即可，不需要调整。
>   2. 删除的不是最后一个元素，从删除点开始以最后一个元素为参照调用一次`siftDown()`即可。

## Set

### Comparable 和 Comparator 的区别

`Comparable` 接口和 `Comparator` 接口都是 Java 中用于排序的接口，它们在实现类对象之间比较大小、排序等方面发挥了重要作用：

- `Comparable` 接口实际上是出自`java.lang`包 它有一个 `compareTo(Object obj)`方法用来排序
- `Comparator`接口实际上是出自 `java.util` 包它有一个`compare(Object obj1, Object obj2)`方法用来排序

Comparable是被排序的类内部的方法，需要修改源码，而Comparator是另外实现一个比较器， 不需要修改源代码。

### HashSet、LinkedHashSet 和 TreeSet 三者的异同

相同点：都是 `Set` 接口的实现类，都能保证元素唯一，并且都不是线程安全的。

不同点：

底层数据结构不同：

- `HashSet` 的底层数据结构是哈希表（基于 `HashMap` 实现）。
- `LinkedHashSet` 的底层数据结构是链表和哈希表，元素的插入和取出顺序满足 FIFO。
- `TreeSet` 底层数据结构是红黑树，元素是有序的，排序的方式有自然排序和定制排序。

应用场景不同： 

- `HashSet` 用于不需要保证元素插入和取出顺序的场景
- `LinkedHashSet` 用于保证元素的插入和取出顺序满足 FIFO 的场景
- `TreeSet` 用于支持对元素自定义排序规则的场景

## Map相关

### HashMap

> 允许放入key为null的元素，也允许插入value为null的元素

> 未实现同步

> 不保证元素顺序

> JDK1.8 之前 `HashMap` 由数组+链表组成的，数组是 `HashMap` 的主体，链表则是主要为了解决哈希冲突而存在的（“拉链法”解决冲突）。JDK1.8 以后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。

> 对于`迭代比较频繁`的场景，不宜将*HashMap*的初始大小设的过大。

> 有两个参数可以影响*HashMap*的性能 :
>
> - 初始容量(inital capacity)： 指定初始table的大小
> - 负载系数(load factor)： 指定自动扩容的临界值。
>
> 当entry的数量超过`capacity * load_factor`时，容器将自动扩容并重新哈希。
>
> 对于`插入元素较多`的场景，将初始容量设大可以减少重新哈希的次数。

> *HashMap*要求`table.length`必须是2的指数，因此`table.length-1`就是二进制低位全是1，跟`hash(k)`相与会将哈希值的高位全抹掉，剩下的就是余数了。

> `resize()`时，如果原有的大小oldCap已经>= 1<<30 , 不能盲目的在此基础上将大小翻倍，因为会越界，所以设置大小为Integer.MAX_VALUE;	

### *LinkedHashMap*

> 实现了*Map*接口，底层仍然是基于拉链式散列结构即由数组和链表或红黑树组成。
>
> 允许放入`key`为`null`的元素，也允许插入`value`为`null`的元素。

> 在HashMap的基础上，增加了一条双向链表，将所有entry连接起来是为了保证元素的迭代顺序和插入顺序相同（双向链表的迭代顺序就是entry的插入顺序）
>
> // 迭代时只需要直接遍历双向链表，即LinkedHashMap的迭代时间只与entry的个数有关，和table的大小无关

> 性能受初始容量和负载系数影响，当entry数量超过初始容量*负载系数时，容器会自动扩容并重新哈希。所以对于插入元素较多的场景，将初始容量设大可以减少重新哈希的次数。

> 非同步的，多线程环境使用时需要手动同步，或者将其包装成为同步的
>
> ``` java
> Map m = Collections.synchronizedMap(new LinkedHashMap(...));
> ```
>
> 

>   插入新元素时：
>
>   1. 从`table`的角度看，新的`entry`需要插入到对应的`bucket`里，当有哈希冲突时，采用头插法将新的`entry`插入到冲突链表的头部。
>   2. 从`header`的角度看，新的`entry`需要插入到双向链表的尾部。

> 删除元素时：
>
> 从`table`的角度看，需要将该`entry`从对应的`bucket`里删除，如果对应的冲突链表不空，需要修改冲突链表的相应引用。
>
> 从`header`的角度来看，需要将该`entry`从双向链表中删除，同时修改链表中前面以及后面元素的相应引用。

### TreeMap

> 实现了SortedMap接口，会按照key的大小对元素进行排序

> 底层使用红黑树实现，即containsKey()、get()、put()、remove()时间复杂度都为log(n)

> ` 非同步的`,需要多线程环境下使用时，手动同步或者
>
> ``` java
> SortedMap m = Collections.synchronizedSortedMap(new TreeMap(...));
> ```

> `红黑树`即满足如下条件的二叉查找树:
>
> - 每个节点的颜色非红即黑
> - 根节点为黑色
> - 红色节点不能连续（指父亲和孩子不能同时是红色）
> - 每个节点向下到叶子节点的任何路径，所含有的黑色节点个数相同

> `后继节点`（树中大于指定节点的所有节点中最小的那个），寻找方式：
>
> - 指定节点的右子树不为空，则后继为右子树总最小的那个元素
> - 指定节点的右子树为空，则后继为其第一个向左走的祖先

```java
//(TODO) 红黑树的结构和颜色调节
```

### WeakHashMap

> 适用于需要缓存的场景:
>
> 对象缓存命中可以提高系统效率，缓存MISS也不会造成错误，因为可以通过计算重新得到

> 垃圾回收时不考虑弱引用（垃圾回收时，判断某个对象是否可以被回收的依据是：是否存在有效的引用指向该对象，而有效引用并不包含弱引用，即仅有弱引用指向的对象仍然会被GC回收）

### HashTable

> 数组 + 链表组成，数组是 `Hashtable` 的主体，链表则是主要为了解决哈希冲突而存在的





