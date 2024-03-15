# synchronized

> synchronized修饰的方法，无论方法正常执行完毕还是抛出异常，都会释放锁

> synchronized是非公平锁

> 在能选择的情况下，既不要用Lock也不要用synchronized关键字，用java.util.concurrent包中的各种各样的类，如果不用该包下的类，在满足业务的情况下，可以使用synchronized关键，因为代码量少，避免出错

> 锁对象不能为空，因为锁的信息都保存在对象头里

#### 1. 对象锁

包括方法锁（默认锁对象为this，当前实例对象）和同步代码块锁（自己指定锁对象）

**代码块形式**

```java
 public void run() {
    // 同步代码块形式——锁为this,两个线程使用的锁是一样的,线程1必须要等到线程0释放了该锁后，才能执行
    synchronized (this) {
        System.out.println("我是线程" + Thread.currentThread().getName());
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + "结束");
    }
}
```

**方法锁形式**：synchronized修饰普通方法，锁对象默认为this

```java
public synchronized void method() {
    System.out.println("我是线程" + Thread.currentThread().getName());
    try {
        Thread.sleep(3000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    System.out.println(Thread.currentThread().getName() + "结束");
}
```

#### 2. 类锁

指的是修饰**静态方法**或指定锁对象为**Class对象**

```java
// synchronized用在静态方法上，默认的锁就是当前所在的Class类，所以无论是哪个线程访问它，需要的锁都只有一把
public static synchronized void method() {
    System.out.println("我是线程" + Thread.currentThread().getName());
    try {
        Thread.sleep(3000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    System.out.println(Thread.currentThread().getName() + "结束");
}
```

```java
@Override
public void run() {
    // 所有线程需要的锁都是同一把
    synchronized(类名.class){
        System.out.println("我是线程" + Thread.currentThread().getName());
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + "结束");
    }
}
```

### synchronized加锁与释放锁的原理

每个对象拥有一个monitor计数器，使用指令控制锁计数器加1或者减1，每一个对象在同一时间只与一个monitor(锁)相关联，而一个monitor在同一时间只能被一个线程获得，一个对象在尝试获得与这个对象相关联的Monitor锁的所有权的时候有三种情况：

- monitor计数器为0，意味着目前还没有被获得，那这个线程就会立刻获得然后把锁计数器+1，一旦+1，别的线程再想获取，就需要等待

- 如果这个monitor已经拿到了这个锁的所有权，又重入了这把锁，那锁计数器就会累加，变成2，并且随着重入的次数，会一直累加（退出时， 当计数器为0时，锁才会被释放）

- 这把锁已经被别的线程获取了，等待锁释放



### synchronized缺陷

- 效率低： 锁的释放情况较少，只有代码执行完毕或者异常结束才会释放锁；试图获取锁的时候不能设置超时，不能中断一个正在使用锁的线程，相对而言，Lock可以中断和设置超时。

- 不够灵活：加锁和释放锁的时机单一，每个锁仅有一个单一的条件，相对而言，读写锁更加灵活

- 无法知道是否成功获得锁：Lock可以拿到状态


Lock解决相应问题：

- `lock()`: 加锁
- `unlock()`: 解锁
- `tryLock()`: 尝试获取锁，返回一个boolean值
- `tryLock(long,TimeUtil)`: 尝试获取锁，可以设置超时

Synchronized加锁只与一个条件(是否获取锁)相关联，不灵活，后来`Condition与Lock的结合`解决了这个问题。

多线程竞争一个锁时，其余未得到锁的线程只能不停的尝试获得锁，而不能中断。高并发的情况下会导致性能下降。ReentrantLock的lockInterruptibly()方法可以优先考虑响应中断。 一个线程等待时间过长，它可以中断自己，然后ReentrantLock响应这个中断，不再让这个线程继续等待。有了这个机制，使用ReentrantLock时就不会像synchronized那样产生死锁了。

> `ReentrantLock`为常用类，它是一个可重入的互斥锁 Lock，它具有与使用 synchronized 方法和语句所访问的隐式监视器锁相同的一些基本行为和语义，但功能更强大。详细分析请看: [JUC锁: ReentrantLock详解]()



synchronized是通过软件(JVM)实现的，简单易用，即使在JDK5之后有了Lock，仍然被广泛的使用。

- **使用Synchronized有哪些要注意的？**
  - 锁对象不能为空，因为锁的信息都保存在对象头里
  - 作用域不宜过大，影响程序执行的速度，控制范围过大，编写代码也容易出错
  - 避免死锁
  - 在能选择的情况下，既不要用Lock也不要用synchronized关键字，用java.util.concurrent包中的各种各样的类，如果不用该包下的类，在满足业务的情况下，可以使用synchronized关键，因为代码量少，避免出错
- **synchronized是公平锁吗？**

synchronized实际上是非公平的，新来的线程有可能立即获得监视器，而在等待区中等候已久的线程可能再次等待，这样有利于提高性能，但是也可能会导致饥饿现象。