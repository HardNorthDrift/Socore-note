# 集合（collection）

使用线程安全的集合可以使用colllections. 下面的synchronized的集合(list，map，set)

## 方法：

​	remove

​		collection方法和使用迭代器(iterator)

​			1：性能方面

​					collection的remove方法，删除的时候需要找出要删除的元素的位置，进行删除。采用的是单链表的形式，			效率慢。而iterator的reovce()结合next()方法进行使用。如果多个元素进行删除(例如隔一个或者多个进行删除)时，			效率比较快

​			2：容错方面

​				collection：在遍历的同时进行删除，会出现ConcurrentModificationException异常，因为集合中对象发生改变而			iterator的对象个数没有发生改变，导致不一致而出现异常。使用iterator不会出现，使用他们保持一致

​	ietrator:

​	1：fail-fast:属性和当前集合共同起作用，所以他不受集合中任何改动影响。java.util(除了并发包)包中的所有集合都时该属性,多线程访问删除的情况下会出现ConcurrentModificationException异常

​	2:fail-safe：java.util.concurrent包中的集合（例如:ConcurrentHashMap和CopyOnWriteArrayList），不会出现异常

## list(有序)

### 	ArrayList：数组

​		优点：查询快

​		缺点：插入删除慢

### 	linkedList：链表

​		优点：插入删除快，只需要添加输出，删除的直接删除，上线两个元素建立连接就好

​		缺点：查询慢

### 	CopyOnWriteArrayList(线程安全的list集合)

## set(无序)

### 	hashSet：速度快,底层数据结构就是hashmap

hashset add是一个值，但是那个是调用底层hashmap的put时充当key值，value时PRESENT 的一个object常量

### 	TreeSet：有序的结合，可以自定义排序顺序，也可以使用默认的

### 	LinkedList：要找插入的顺序j进行保存

## Map(key-value)

### hashMap(线程不安全，可以为空值，但是key值只允许一个空)

原理：存储对象时，将k-v传递给put方法，调用hashcode进行hash计算，找到bucket进行存储，再通过equals()方法确认键值对。添加数据时如果通过Entry的键值找到对应的值，则替换，否则在链表的尾部进行插入一个新的entry，虽有map对象相等hashcode一定相等，但是hashcode相等，但是对象不一定相等。

1：jdk1.7的版本之前都是数组+链表的形式保存，1.8之后(包含1.8),数组+链表(红黑树)结构模式，每个链表超过8就采用红黑树的模式，但是不是所有的，假如一开始数组是16个，他会扩容到64，超过8的重新计算，放到扩容后的上面，扩容到64之后，如果还是超过了8个就会使用红黑树

2：数组的长度一般为2的n次方，一般默认为16。因为数组的下标按照"(n-1)&hash"进行计算 2的幂次方即每一位都是1，每一个位都和1进行&运算，不同位置上不会产生碰撞。否则，有很多或者全部都在0上进行&运算，产生大量的碰撞

3：hashmap扩容：条件：1：hashMap存在的值大于等于阈值2：当前加入的数据是否发生了hash冲突 默认数组大小为16阈值为16*0.75=12

扩容：创建原来的entry2倍的entry，将原来entry数组元素拷贝到新的entry数组里。1.8进行了优化，要不在原来的位置，要不移动2的n次幂的位置

并发情况下进行扩容，会出现死循环。就是多线程情况下导致两个entry进行相互指向，造成了死循环

### ConcurrentHashMap:

采用分段技术，减少锁的颗粒度，效率高  （大小怎么计算，存在什么问题自己先百度，不整理了）

### SynchronizedMap

Collections.SynchronizedMap(map),采用synchronized方法上加锁，使用阻塞同步，效率低，但是可以允许key和value为空

### hashTable(线程安全的不允许空值)

采用了synchronized方法上加锁，他的get，put，remove方法都是通过同步的，所以任何时刻都是一个线程操作，所以效率低



## JUC多线程和高并发

### voliate关键字

1：java虚拟机提供的一种轻量级的同步机制

2：具有的特性：

​	2.1：保证原子可见性 

​	2.2：禁止指令重排 (遵循happens-before规则)

​	2.3：不能保证原子性		

JMM:(Java Memory Model) 不真实存在，只是一个规则和规范

​	JMM的规定：

​		1：线程解锁前，必须把共享变量刷新到主内存

​		2：线程枷锁前，必须读取主内存到自己的工作内存

​		3：加锁解锁，使用同一个锁

原子性：可以使用atomic原子类，synchronized

指令重排

​	源代码----》编译器优化重排(就是代码的执行顺序)---》指令并行重排---》内存系统重排---最终执行命令

重排遵循规则：指令之间的数据依赖，就是变量参数依赖

单线程能确保代码顺序和最终执行效果一致，但是多线程下不能保证

内存屏障（Memory Barrier）,是一个cpu指令

编译器和处理器都可以执行重排，在指令间插入一条内存屏障，告诉编译器和cpu，不管什么指令都不能和这条内存屏障进行指令重排序，即：通过插入内存屏障禁止在内存屏障前后进行指令重排序

内存屏障还前置刷出各种cpu的缓存数据，cpu上所有的线程都能读取到最新数据

对voliate变量进行写时，会在前后添加一个 store屏障，从工作内存刷新到主内存。读取时前后添加load屏障，从主内存中读取共享变量

**经典案例**：单例模式使用voliate关键字加双重判断实现或者内部类的方式

## cas（compare and swap）比较并交换

原理：java无法访问底层系统，通过本地native方法来访问，cas操作执行依赖于Unsafe类的方法，unsafe类的方法都是通过native修饰的，判断某个位置的值是否预期值，整个过程是原子的。执行过程中必须连续，不允许中断，所以不会再成数据不一致性的存在

unsafe 类;sun.misc（属于rt.jar）

CAS：比较类似于自旋锁，缺点：1：循环时间长，开销大 2：只能保证一个共享变量的原子操作 3：会出现ABA问题

ABA问题存在的原因：就是T1线程取出来的值为A，但是T2线程将A变成B又修改为A，T1发现值还是A，继续执行

AtomicRerence()原子引用：进行比较并修改

ABA问题解决：

AtomicStampedRerence()引用  加时间戳，哪怕值发生了改变又改回来，但是有时间戳属性，也相当于不相等

## 锁  lock锁可以加多次，但是要对应相对应次数的unlock

### 	公平锁：（锁按照顺序执行）

​		多个线程按照申请锁的顺序获取锁。获取锁的时候先看队列是否有，没有获取锁，有添加到队列中，FIFO规则执行

### 	非公平锁：（吞吐量比较大）

​		多个线程不按照申请锁的顺序执行，有可能后申请的先获取到锁(Synchronized,ReentrantLock(false)默认false），或造成有的线程一直获取到锁。

​		一开始就尝试获取锁，如果获取失败才采用类似于公平锁的原理去执行

### 	可重入锁(递归锁)：

​		Synchronized,ReentrantLock经典的可重入锁	

​		外层同一个线程获取到锁时，内层方法可以自动获取到锁。即线程可以进入一个它以拥有的锁所有的同步的代码块

​	优点：可以避免死锁

### 	自旋锁：

​	尝试获取锁的线程不会立即阻塞，而是采用循环的方式去尝试获取锁，mylock尝试获取锁

优点：减少线程上下文的切换

缺点：消耗cpu

### 独占锁(写锁)：

 该锁只能被一个线程所持有 ，Synchronized,ReentrantLock

### 共享锁(读锁)：

可以被多个线程所持有 ReentrantReadWriteLock

读锁的共享锁可保证并发读是高效的，但是读写，写读，写写之间是互斥的

### 互斥锁

### 读写锁 

写锁：ReentrantReadWriteLock.writeLock().lock()  写锁要加voliate关键字保证可见性和禁止指令重排，不保证原子性

读锁： ReentrantReadWriteLock.readLock().lock()

### CountDownLatch  

线程数递减统计   例如，多个线程执行结束，线程数减到0 ，确定所有线程执行结束，主线程去执行

countDown()不会阻塞线程只是计数器减1，await() 阻塞主线程

### CyclicBarrier

一组线程组到达一个屏障(同步点)时被阻塞，直到最后一个线程到达屏障时，所有被阻塞的线程才继续使用，线程进入屏障通过await()方法。

区别：countDownLatch是减到0，该方法是所有的线程都到达了屏障，就是加统计才执行主线程

### Semaphore(信号量)

目的：1：共享资源互斥 2：并发线程数的控制

acquire()尝试获取   

release()释放资源

## 队列

ArrayBlockingQueue:数组结构的的有界阻塞队列，先进先出的规则

LinkedBlockingQueue：基于链表结构的有界阻塞队列(大小为Integer.max_value)，先进先出原则，吞吐量高于第一个

SynchronousQueue:一个不存元素的阻塞队列，每个插入必须等待另一个线程调用移除操作，否则插入一直处于阻塞

PriorityBlockingQueue：支持优先级排序无界阻塞队列

DelayQueue：使用优先级队列实现的延迟无界限队列

LinkedTransferQueue：由链表结构组成的无界阻塞队列

阻塞队列：阻塞即队列为空时取值，队列满了放值，满足情况阻塞挂起线程，满足条件线程又会自动被唤醒

BlockingQueue：使用优先级队列实现的延迟无界限的阻塞队列

blockingQueue----->queue接口----->collection接口

好处不用去管阻塞和唤醒，他自动处理

队列

​	方法类型                抛出异常                    特殊值                          阻塞                       超时

​	    插入					add(e)						offer(e)						put(e)					offer(e,time,unit)

​		移除					remove					   poll()                            take()       			poll(time,unit)	

​		检查					element()					peek()						不可用					不可用

队列满了，add会出queue full满了的异常   队列空了 remove会出现nosuchElementException异常

特殊值：插入成功为true，否则为false  移除没有返回null

阻塞：插入队列满了put的时候会一直等待直到put或者响应中断 ，移除队列为空为一致阻塞直到有队列可用

超时:队列满了会阻塞一段时间，超时后退出

### Synchronized和lock的区别	

​	1：Synchronized 关键字属于JVM层面（monitorenter进入，monitorexit退出，其实wait和notify依赖于monitor ），lock是具体类，属于api层面

​	2：Synchronized不需要手动释放锁，ReentrantLock需要手动释放锁

​	3：Synchronized 不可中断，除非报错或者异常 	ReentrantLock可中断 1：设置超市时间tryLock(long timeout,TimeUnit unit)2:lockInterruptibly()放在代码块中

​	4：Synchronized非公平 ReentrantLock两者都可以，通过参数boolean，去处理true公平，默认false

​	5：Synchronized不可以绑定多个条件Condition ，ReentrantLock可以多个Condition，即Synchronized随机唤醒一个或者全部唤醒，而ReentrantLock可以指定唤醒。

Synchronized 的wait和notify属于obj类的方法 ，wait释放资源。而sleep属于线程的，不释放资源

Synchronized 

​		1：普通同步方法，所在当前实例对象，一个对象访问会阻塞，两个对象去访问不会

​		2：静态方法，锁是当前class对象，多个对象也会阻塞

​		3：同步方法快，多的是括号里的对象。多个对象访问同一同步方法块也会阻塞



### 线程池

线程池的7个参数含义：

线程池的工作原理：

线程池的4个拒绝策略：

一般线程都自己配置策略啥的，使用jdk自带的那四个，如果队列满了，会出现oom异常

# JVM

## 	JMM内存模型

​		1：方法区 

​		2：堆

​		3：虚拟机栈

​			当我们调用一个方法时，我们可以将这个调用的方法的线程看为一个栈，这个线程会随着栈的运行完成而执行完毕，然后进行销毁，栈是一个数据结构先进后出

​		4：本地方法栈

​			本地方法栈是Java调用本地方法是所使用的，它是与底层的C语言或者CPU，或者操作系统进行的交互

​		5：程序计数器（PC寄存器）

​			程序计数器是一块较小的内存空间，可以看作是当前线程所执行的字节码的行号指示器。分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖这个计数器来完成。 

## oom