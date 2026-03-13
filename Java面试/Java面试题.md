# Java 面试题 

## 2024/12/27   :sunglasses:  

## 1. ==与equal()的区别

### 1.1 == 比较的是值 (引用类型的值是内存地址)

### 1.2 equal(): object类之下的方法, <u>==若没有重写等价于====</u>

### 例子 :

```java
public class Equal {
    public static void main(String[] args) {
        // 1.==比较的是值
        // 2 equal(): object类之下的方法, ==若没有重写等价于==
        String a = new String("abc");
        String b = new String("abc");
        // False 比较的是内存地址
        System.out.println(a == b);
        // True String的equal()被重写过
        System.out.println(a.equals(b));
    }
}
```

## 2. 传值还是传引用 :question:

### 2.1 基本类型传值 例如 int,char,float 

### 2.2 引用类型传引用(其实还是传的值,但是该值是其内存地址) 例如 Integer 

### 2.3 String类型修改值时创建一个新的String类型对象(因为String的值无法直接改变,只能新创建一个对象)

## 3. 浅拷贝 VS 深拷贝 :first_quarter_moon:

### 3.1 浅拷贝: 只拷贝指向某个对象的指针。基本类型拷贝值，==拷贝引用类型拷贝指针即其中一个指针改变了其对象另一个也会受影响==

### 3.2 深拷贝: 复制整个对象。

## 4. Junit

###  4.1 @BeforAll 与 AfterAll VS @BeforEach 与 AfterEach :taco:

#### 4.1.1 All: 所有单元方法前/后==调用且仅调用一次==

#### 4.1.2 Each:每个单元方法前/后==都调用==

###  4.2 单元测试覆盖率功能

 1class Solution {2public:3    ListNode* reverseList(ListNode* head) {4        if (!head || !head->next){5            return head;6        }7        ListNode* tail = reverseList(head->next);8        head->next->next = head;9        head->next = NULL;10        return tail;11    }12};java

###  4.3 @Resource、@SpyBean和@MockBean

#### 4.3.1 @Resource: 真实调用

#### 4.3.2 @SpyBean: 有规则按规则走，没有规则则真实调用

#### 4.3.3 @MockBean: 没有规则返回默认值，对象为null，数字为0，有mock规则按规则走

**<u>规则的制定</u>**：

```java
when(xxxservices.method()).thenReturn("ok");
```

## 5. JUC多线程高并发

###  5.1 用户线程 VS 守护线程  :swimmer:

#### 5.1.1 用户线程: 自定义线程 (new Thread())

#### 5.1.2 守护线程：后台中的特殊线程。比如负责垃圾回收

#### 注意！！！

- 当只有守护线程时jvm结束
- 当还有用户线程时jvm存活
- start()方法开启线程
### 5.2 wait() VS sleep()   :thought_balloon:
#### 5.2.1 从属不同：wait()属于Object类下而sleep属于Thread类下
#### 5.2.2 锁的释放:  wait()会释放锁而sleep()不会释放锁也不会占用锁
#### 5.2.2 使用范围、捕获异常不同:  wait():必须在同步代码块中使用，不需要捕获异常。sleep()可以在任何地方使用，必须要捕获异常。



###  5.3 ==lock与synchronized的区别==  (重点) :lock:

#### 5.3.1 类型不同：lock是个接口下而synchronized是个关键字

#### 5.3.2 能否判断锁的状态: lock可以判断锁的状态而synchronized不能判断锁的状态

#### 5.3.2 能否自动释放锁:lock不能自动释放锁而synchronized能自动释放锁

#### 5.3.3 能否重入、中断、是否公平:lock可重入、可以判断锁、==默认非公平==，可以变为公平。synchronized 可重入、不可以中断、不可以判断锁

#### 5.3.4 <u>Lock适合锁大量同步代码！synchronized 适合锁少量的同步代码</u>

#### 5.3.5 Lock可以提高多个线程进行读操作的效率。

#### ==5.3.6 lock的实现卖票==

```java
class Ticket {
    private int ticketNumber = 30;
    // 创建可重入锁 lock下的ReentrantLock
    private final ReentrantLock lock = new ReentrantLock();

    public void sale() {
        // 上锁
        lock.lock();
        if (ticketNumber > 0) {
            try {
                System.out.println(Thread.currentThread().getName() + " 卖了第" + ticketNumber-- + "张票" + " 剩余" + ticketNumber + "张票");
            } finally {
                // 解锁
                lock.unlock();
            }
        }

    }
}
```

#### 5.3.7 lock与==condition==实现生产者消费者

```java
class Data1 {
    private int num = 0;

    // 定义lock以及条件
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();

    // 0加1 非0等待
    public void increase() throws InterruptedException {
        // 非0等待 用while防止虚假唤醒
        lock.lock();
        try {
            while (num != 0) {
                condition.await();
            }
            // 0加1
            num++;
            System.out.println(Thread.currentThread().getName() + "=>" + num);
            // 进行v操作唤醒其它线程
            condition.signalAll();
        } finally {
            lock.unlock();
        }
    }

    public void decrease() throws InterruptedException {
        // 非1等待 用while防止虚假唤醒
        lock.lock();
        try {
            while (num != 1) {
                condition.await();
            }
            // 1减1
            num--;
            System.out.println(Thread.currentThread().getName() + "=>" + num);
            // 进行v操作唤醒其它线程
            condition.signalAll();
        }finally {
         lock.unlock();
        }
    }
}
```



#### 5.3.8 synchronized 实现生产者消费者

```java
class Data {
    private int num = 0;

    // 0加1 非0等待
    public synchronized void increase() throws InterruptedException {
        // 非0等待 用while防止虚假唤醒
        while (num != 0) {
            this.wait();
        }
        // 0加1
        num++;
        System.out.println(Thread.currentThread().getName() + "=>" + num);
        this.notifyAll();
    }

    public synchronized void decrease() throws InterruptedException {
        // 非1等待 用while防止虚假唤醒
        while (num != 1) {
            this.wait();
        }
        // 1减1
        num--;
        System.out.println(Thread.currentThread().getName() + "=>" + num);
        this.notifyAll();
    }
}
```



## 5.4 线程的定制化通信  :thought_balloon:

### 5.4.1 利用flag实现:flag = 1时A运行,flag = 2时B运行，flag = 3时C运行

![](C:\Users\hasee\AppData\Roaming\Typora\typora-user-images\image-20241230144205641.png)

### 例子 :

```java
class Data {
    // 定义标志位
    private int flag = 1;
    // 创建lock锁
    private Lock lock = new ReentrantLock();

    // 创建三个condition
    private Condition c1 = lock.newCondition();
    private Condition c2 = lock.newCondition();
    private Condition c3 = lock.newCondition();

    // 实现A、B、C 三个线程的顺序执行
    // A执行5次，B执行10次，C执行15次

    // A:打印5次,参数为轮次
    public void print5(int loop) {
        // 上锁
        lock.lock();
        try {
            // 判断
            while (flag != 1) {
                c1.await();
            }
            // 做事
            for (int i = 1; i <= 5; i++) {
                System.out.println(Thread.currentThread().getName() + ": 第" + i  + "次 轮数" + loop);
            }
            // 通知 c2获得锁
            flag = 2;
            c2.signal();
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } finally {

            lock.unlock();
        }
    }// B:打印10次,参数为轮次
    public void print10(int loop) {
        // 上锁
        lock.lock();
        try {
            // 判断
            while (flag != 2) {
                c2.await();
            }
            // 做事
            for (int i = 1; i <= 10; i++) {
                System.out.println(Thread.currentThread().getName() + ": 第" + i + "次 轮数" + loop);
            }
            // 通知
            flag = 3;
            c3.signal();
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } finally {
            lock.unlock();
        }
    }// C:打印15次,参数为轮次
    public void print15(int loop) {
        // 上锁
        lock.lock();
        try {
            // 判断
            while (flag != 3) {
                c3.await();
            }
            // 做事
            for (int i = 1; i <= 15; i++) {
                System.out.println(Thread.currentThread().getName() + ": 第" + i  + "次 轮数" + loop);
            }
            // 通知
            flag = 1;
            c1.signal();
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } finally {
            lock.unlock();
        }
    }
}
```

## 5.5 集合的线程安全  :flashlight:

![image-20241230151027028](C:\Users\hasee\AppData\Roaming\Typora\typora-user-images\image-20241230151027028.png)



## 5.6 多线程锁 :dagger:

### 5.6.1 同步方法，谁先拿到锁谁先执行，同一把锁顺序执行。

### 5.6.2 普通同步方法锁的是 this 当前对象（即方法的调用者)。 

### 5.6.3 静态同步方法锁的是类对象，类只会加载一次，所以静态同步方法永远锁的都是类对象（XXX.class）。

### 例子

```java
 * 八锁，就是关于锁的八个问题
 * 1、标准情况下，两个线程先打印 发短信 还是 打电话？ 1/发短信 2/打电话
 * 2、sendSms()延迟4s情况下，两个线程先打印 发短信 还是 打电话？ 1/发短信 2/打电话
 */
public class Test1 {
    public static void main(String[] args) {
        Phone phone = new Phone();
        //锁的存在
        new Thread(() -> {
            phone.sendSms();
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"A").start();

        new Thread(() -> {
            phone.call();
        },"B").start();
    }
}

class Phone{

    //synchronized 锁的对象是方法的调用者！
    //两个方法用的是同一把锁，谁先拿到谁执行！
    public synchronized void sendSms(){
        try {
            TimeUnit.SECONDS.sleep(4);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("发短信");
    }
    public synchronized void call(){
        System.out.println("打电话");
    }
}

```

## 5.7 非公平/公平锁 :lock:

### 5.7.1 非公平锁: 多个线程去获取锁的时候，会直接去尝试获取，获取不到，再去进入等待队列，如果能获取到，就直接获取到锁。 效率高

```java
 private Lock lock = new ReentrantLock();
```

### 5.7. 2 公平锁: 多个线程按照申请锁的顺序去获得锁。效率相对低。

```java
 private Lock lock = new ReentrantLock(true);
```

## 5.8 死锁 :lock:

<u>**什么是死锁？两个或者两个以上进程在执行过程中，因为争夺资源而造成一种互相等待的现象，如果没有外力干涉，他们无法在执行下去。**</u>

![img](https://i-blog.csdnimg.cn/blog_migrate/a888c6e5944c54d7ff77e867e68a6417.png)

### 5.8.1 死锁的四个必要条件:

- **请求并保持:进程已占用一部分它所需的资源,同时申请新的资源。**
- **互斥条件:资源是独占的且排他使用，进程互斥使用资源。即任意时刻一个资源只能给一个进程使用。**
- **循环等待: 进程互相等待对方手里的资源被释放。**
- **不可剥夺: 进程在占用某个资源时,不允许其余进程对该进程的资源进行剥夺。**
### 5.8.2 产生死锁的原因:

- **系统资源不足**
- **进程运行推进顺序不合适**
- **资源分配不当**

### 5.8.3 死锁的解决的方法:
- **死锁预防:破坏死锁的四个必要条件之一**
- **死锁避免:防止进入不安全状态。可以用银行家算法来判断是否存在安全序列**

### 5.9 创建线程的方式 :woman_teacher:

#### 方式1 继承Thread重写run方法(==不推荐==)

```java
public class ExtendsThread extends Thread{
    public static void main(String[] args) {
        ExtendsThread extendsThread = new ExtendsThread();
        extendsThread.start();
    }

    @Override
    public void run() {
        System.out.println("Hello Thread");
    }
}
```

#### 方式2 实现Runnable(==匿名内部类和lambda方式推荐==)

```java
public class ImplementsRunnable implements Runnable {

    public static void main(String[] args) {
        // 1. 方式1实现Runnable
        ImplementsRunnable implementsRunnable = new ImplementsRunnable();
        implementsRunnable.run();
        // 2,方式二 匿名内部类的方式
        new Thread(new Runnable(){
            public void run(){
                System.out.println("Hello Runnable Thread!!!");
            }
        // 3.方式3 lambda表达式
        Thread thread1 = new Thread(() -> System.out.println("hello"));
        thread1.start();
    }
    @Override
    public void run() {
        System.out.println("Hello Runnable Thread!!!");
    }
}
```

#### 方式3 实现Callable(适用于需要拿到返回结果的情况)

```java
/**
 * @author LinHao 方式三 实现Callable
 */
public class ImplementsCallable implements Callable<String> {
    @Override
    public String call() throws Exception {
        return "Hello Callable!";
    }
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 1. 实例化FutureTask
        FutureTask<String> task = new FutureTask<>(new ImplementsCallable());
        // 2.创建线程对象
        Thread thread = new Thread(task);
        thread.start();
        // 3. 获取任务结果
        String result = task.get();
        System.out.println(result);
    }
}
```

#### 方式4 线程池 见5.12.4


### 5.10 JUC辅助类    :sailboat:

####  5.10.1 减少计数 CountDownLatch：给定初始值,当初始值变为0时await()失效

- countDownLatch(int count):给定计数的初始值

- countDown():计数减一,如果计数为0释放所有等待的线程
- await():当前线程在计数为零前一直等待，除非线程被中断

#### 例子: 6个同学走后班长才锁门

```java
public class CountDownLatchDemo {
    public static void main(String[] args) {
        // 1.定义计数初始值
        CountDownLatch countDownLatch = new CountDownLatch(6);
        for (int i = 1; i <= 6; i++) {
            System.out.println("第" + i + "个同学离开了教室");
            // 2.计数减一
            countDownLatch.countDown();
        }
        // 3.await() 所有人走完才锁门
        try {
            countDownLatch.await();
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        System.out.println("班长锁门了!!!");
    }
}
```

####  5.10.2 循环栅栏 CyclistBarrier:给定初始值,当第NUMBER个线程达到该值时触发事件

- countDownLatch(int count):给定计数的初始值

- countDown():计数减一,如果计数为0释放所有等待的线程
- await():当前线程在计数为零前一直等待，除非线程被中断

#### 例子：集齐七龙珠后触发事件

```java
public class CyclistBarrierDemo {
    // 1.定义初始值
    private static final int NUMBER = 7;

    public static void main(String[] args) {
        // 2.设置触发事件
        CyclicBarrier cyclicBarrier = new CyclicBarrier(NUMBER, new Thread(() -> {
            System.out.println("集齐了七颗龙珠,召唤神龙!!!!");
        }));
        for (int i = 1; i <= 7; i++) {
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "星龙珠已被收集!!!!");
                try {
                    // 3.每个线程触发一次await()当触发第7次时触发事件
                    cyclicBarrier.await();
                } catch (Exception e) {
                    throw new RuntimeException(e);
                }
            }, String.valueOf(i)).start();
        }
    }
}
```

####  5.10.3 信号量 Semaphore:给定资源总数, 实现对资源的同步与互斥访问

- acquire():获取资源,==类似于P操作==
- release():释放资源,==类似于V操作==

#### 例子：6个线程抢三个同类资源

```java
// 6个车子 3个车位
public class SemaphoreDemo {
    public static void main(String[] args) {
        // 1.设置许可
        Semaphore semaphore = new Semaphore(3);

        // 2.创建6个车位
        for (int i = 1; i <= 6 ; i++) {
            new Thread(()->{
                // 3.p操作获得车位
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName() + "抢到了车位");
                    // 设置停止时间
                    TimeUnit.SECONDS.sleep(new Random().nextInt(5));
                    System.out.println(Thread.currentThread().getName() + "离开了车位");
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }finally {
                    // 4.v释放车位
                   semaphore.release();
                }
            },String.valueOf(i)).start();
        }
    }
}

```

### 5.11 读写锁(读者写者问题)    :sailboat:

####  5.11.1 读锁:共享锁。 写锁:独占锁。读写锁:一个资源可以同时被多个读进程访问或一个写进程访问，但是不能同时存在读写线程，读写互斥，读读是共享的。锁降级:将写入锁降级为读锁

####  无锁、synchronized和ReentrantLock的添加锁、读写锁ReentrantReadWriteLock的区别

- 无锁:多线程抢夺资源混乱
- 添加锁: 都是独占的
- 读写锁: 优点:读读不互斥可以共享、提升性能。 缺点：读写互斥造成锁饥饿。

### 5.12 线程池（Thread Pool）是一种并发编程中常用的技术，用于管理和重用线程。为了避免频繁地创建和销毁线程的开销，以及控制并发执行的线程数量，从而提高系统的性能和资源利用率。它由线程池管理器、工作队列和线程池线程组成。 :printer:

####  5.12.1 三大使用方式

- 一池N线程: Executors.newFixedThreadPool(int)
- 一池一线程: Executors.newSingleThreadExecutor()
- 线程池根据需求创建线程，可扩容: Executors.newCachedThreadPool()

####  5.12.2 七大参数

1. int corePoolSize：常驻(核心)线程数量
2. int maximumPoolSize: 最大线程数量
3. long keepAliveTime: 线程存活时间
4. TimeUnit unit, 超时单位
5. BlockingQueue<Runnable> workQueue：阻塞队列， 常驻线程已用完时，来了请求，将这些请求放到阻塞队列
6. ThreadFactory threadFactory: 线程工厂: 用于创建线程
7. RejectedExecutionHandler handler: 拒绝策略，阻塞队列满了之后不接受请求

####  5.12.3 四种拒绝策略

```java
 * 4大拒绝策略：
 * new ThreadPoolExecutor.AbortPolicy() //默认拒绝策略 银行满了，还有人进来，不处理这个人，抛出异常
 * new ThreadPoolExecutor.CallerRunsPolicy() //将任务回推给调用者线程自己执行。
 * new ThreadPoolExecutor.DiscardPolicy() //队列满了，丢掉任务，不会抛出异常
 * new ThreadPoolExecutor.DiscardOldestPolicy() //队列满了，尝试和最早的竞争，也不会抛出异常！
```

####  5.12.4 线程池的状态

- RUNNING: 表示线程池正常运行。能接受新任务，也会正常处理队列中的任务
- SHUTDOWN: 调用shutdown()方法后,线程池进入该状态，表示线程池关闭状态。**<u>不能接受新任务，会继续把队列中的任务处理完</u>**
- STOP: 调用shutdownnow()方法后,线程池进入该状态，表示线程池关闭状态。**<u>不能接受新任务，==不会继续把队列中的任务处理完，且正常运行的线程会被中断==</u>**
- TIDYING: 线程池没有线程运行后，线程池进入该状态，并且会调用terminated(),该方法是空方法，留给程序员去扩展
- TERMINATED: 执行完terminated()方法后，线程池的状态就会变为TERMINATED


####  5.12.4 自定义线程

```java
public class ThreadPoolDemo {
    public static void main(String[] args) {
        ThreadPoolExecutor threadPool1 = new ThreadPoolExecutor(
                2,
                5,
                2L,
                TimeUnit.SECONDS,
                new ArrayBlockingQueue<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy());
        // 10个顾客（线程请求)
        try {
            for (int i = 1; i <= 10; i++) {
                // 执行
                threadPool1.execute(() -> {
                    System.out.println(Thread.currentThread().getName() + " 办理业务");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            threadPool1.shutdown();
        }
    }
}  
```

## 6.基本类型和包装类型的区别？

- **用途**：除了定义一些常量和局部变量之外，我们在其他地方比如方法参数、对象属性中很少会使用基本类型来定义变量。并且，包装类型可用于泛型，而基本类型不可以。

- **存储方式**：基本数据类型的==局部变量==存放在 Java 虚拟机==栈==中的局部变量表中，基本数据类型的==成员变量==（未被 `static` 修饰 ）存放在 Java 虚拟机的==堆==中。==包装类型==属于对象类型，我们知道几乎所有对象实例都存在于==堆==中。

  ```java
  public class Test {
      // 成员变量，存放在堆中
      int a = 10;
      // 被 static 修饰的成员变量，JDK 1.7 及之前位于方法区，1.8 后存放于元空间，均不存放于堆中。
      // 变量属于类，不属于对象。
      static int b = 20;
  
      public void method() {
          // 局部变量，存放在栈中
          int c = 30;
          static int d = 40; // 编译错误，不能在方法中使用 static 修饰局部变量
      }
  }
  ```

  

- **占用空间**：相比于包装类型（对象类型）， 基本数据类型占用的空间往往非常小。

- **默认值**：成员变量包装类型不赋值就是 `null` ，而基本类型有默认值且不是 `null`。

- **比较方式**：对于基本数据类型来说，`==` 比较的是值。对于包装数据类型来说，`==` 比较的是对象的内存地址。所有整型包装类对象之间值的比较，全部使用 `equals()` 方法。

## 7.静态方法为什么不能调用非静态成员?

这个需要结合 JVM 的相关知识，主要原因如下：
1. ==静态方法是属于类==的，在类加载的时候就会分配内存，可以通过类名直接访问。而==非静态成员属于实例对象==，只有在==对象实例化之后才存在==，需要通过类的实例对象去访问。
2. 在类的==非静态成员==不存在的时候静态方法就已经存在了，此时调用在内存中还不存在的非静态成员，属于非法操作。

## 8.重写的注意事项

**方法的重写要遵循“两同两小一大”**

- “两同”即方法名相同、形参列表相同；
- “两小”指的是子类方法返==回值类型应比父类方法返回值类型更小或相等==，子类方法声明抛出的==异常类==应比父类方法声明抛出的异常类==更小或相等==；
- “一大”指的是==子类方法的访问权限==应比父类方法的访==问权限更大或====相等==

## 9.什么是可变长参数?

从 Java5 开始，Java 支持定义可变长参数，==所谓可变长参数就是允许在调用方法时传入不定长度的参数==。就比如下面这个方法就可以接受 0 个或者多个参数。外，==可变参数只能作为函数的最后一个参数==，但其前面可以有也可以没有任何其他参数。

```java
public static void method1(String... args) {
   //......
}
```

## 10. 异常结构图

![Java 异常类层次结构图](https://oss.javaguide.cn/github/javaguide/java/basis/types-of-exceptions-in-java.png)

- ### 10.1 Exception 和 Error 有什么区别？

在 Java 中，所有的异常都有一个共同的祖先 `java.lang` 包中的 `Throwable` 类。`Throwable` 类有两个重要的子类:

- **`Exception`** :程序==本身可以处理的异常，==可以通过 `catch` 来进行捕获。`Exception` 又可以分为 Checked Exception (受检查异常，必须处理) 和 Unchecked Exception (不受检查异常，可以不处理)。

- **`Error`**：`Error` 属于程序无法处理的错误 ，我们没办法通过 `catch` 来进行捕获不建议通过`catch`捕获 。例如 Java 虚拟机运行错误（`Virtual MachineError`）、虚拟机内存不够错误(`OutOfMemoryError`)、类定义错误（`NoClassDefFoundError`）等 。这些异常发生时，Java 虚拟机（JVM）一般会选择线程终止。

- ### 10.2 Checked Exception 和 Unchecked Exception 有什么区别？

  - **Checked Exception** 即 受检查异常 ，Java 代码在编译过程中，如果受检查异常没有被 `catch`或者`throws` 关键字处理的话，就没办法通过编译。如下:
  - <img src="https://oss.javaguide.cn/github/javaguide/java/basis/checked-exception.png" alt="img" style="zoom:80%;" />

- **Unchecked Exception** 即 **不受检查异常** ，Java 代码在编译过程中 ，我们即使不处理不受检查异常也可以正常通过编译。

`RuntimeException` 及其子类都统称为非受检查异常，常见的有（建议记下来，日常开发中会经常用到）：

- `NullPointerException`(空指针错误)
- `IllegalArgumentException`(参数错误比如方法入参类型错误)
- `NumberFormatException`（字符串转换为数字格式错误，`IllegalArgumentException`的子类）
- `ArrayIndexOutOfBoundsException`（数组越界错误）
- `ClassCastException`（类型转换错误）
- `ArithmeticException`（算术错误）
- `SecurityException` （安全错误比如权限不够）
- `UnsupportedOperationException`(不支持的操作错误比如重复创建同一用户)
- …

## 10.3 Throwable 类常用方法有哪些？

- `String getMessage()`: 返回异常发生时的详细信息

- `String toString()`: 返回异常发生时的简要描述

- `String getLocalizedMessage()`: 返回异常对象的本地化信息。使用 `Throwable` 的子类覆盖这个方法，可以生成本地化信息。如果子类没有覆盖该方法，则该方法返回的信息与 `getMessage()`返回的结果相同

- `void printStackTrace()`: 在控制台上打印 `Throwable` 对象封装的异常信息

## 10.4 try-catch-finally语句

- `try`块：用于捕获异常。其后可接零个或多个 `catch` 块，如果没有 `catch` 块，则必须跟一个 `finally` 块。

- `catch`块：用于处理 try 捕获到的异常。

- `finally` 块：无论是否捕获或处理异常，`finally` 块里的语句都会被执行。当在 `try` 块或 `catch` 块中遇到 `return` 语句时，`finally` 语句块将在方法返回之前被执行。

  ```java
  try {
      System.out.println("Try to do something");
      throw new RuntimeException("RuntimeException");
  } catch (Exception e) {
      System.out.println("Catch Exception -> " + e.getMessage());
  } finally {
      System.out.println("Finally");
  }
  ```

  ```java
  Try to do something
  Catch Exception -> RuntimeException
  Finally
  ```

  **注意：不要在 finally 语句块中使用 return!**

  ## 11.反射

  ### 11.1 何为反射?

  反射之所以被称为框架的灵魂，主要是因为它赋予了我们在运行时分析类以及执行类中方法的能力。**通过反射你可以获取任意一个类的所有属性和方法，你还可以调用这些方法和属性。**

### 11.2 反射的优缺点

**优点**：可以让咱们的代码更加灵活、为各种框架提供开箱即用的功能提供了便利

**缺点**：让我们在运行时有了分析操作类的能力，这同样也增加了**安全问题。比如可以无视泛型参数的安全检查**（泛型参数的安全检查发生在编译时）。另外，**反射的性能也要稍差点**，不过，对于框架来说实际是影响不大的。相关阅读：[Java Reflection: Why is it so slow?](https://stackoverflow.com/questions/1392351/java-reflection-why-is-it-so-slow)

### 11.3 反射实战

#### 11.3.1 获取Class对象的四种方式

##### 1. 知道具体类的情况下使用

```java
Class alunbarClass = TargetObject.class;
```

###### 2.通过 `Class.forName()`传入类的全路径获取

```java
Class alunbarClass1 = Class.forName("cn.javaguide.TargetObject");
```

###### 3.通过对象实例`instance.getClass()`获取：

```java
TargetObject o = new TargetObject();
Class alunbarClass2 = o.getClass();
```

##### 4. 通过类加载器`xxxClassLoader.loadClass()`传入类路径获取:
```java
ClassLoader.getSystemClassLoader().loadClass("cn.javaguide.TargetObject");
```
  ## 12.从输入 URL 到页面展示到底发生了什么？（非常重要)

1. 在浏览器输入指定网页的URL

2. 浏览器通过DNS协议，获取域名对应的Ip地址

3. 浏览器根据Ip地址和端口号，向目标服务器发起一个TCP连接请求

4. 浏览器在TCP连接上，向服务器发送一个HTTP请求报文，请求获取网页的内容

5. 服务器收到HTTP请求报文后，处理请求，并返回HTTP响应报文给浏览器

6. 浏览器收到HTTP响应报文后，解析响应体中的HTML代码，渲染网页的结构和样式，同时根据HTML中的其它资源的URL(如图片、CSS、JS等)，再次发送HTTP请求，获取这些资源的内容，直到网页完全加载显示。

7. 浏览器在不需要和服务器通信时，可以主动关闭TCP连接，或等待服务器的关闭请求、

  ##  13.HTTP 和 HTTPS 有什么区别？（重要）

1. 端口号: HTTP:80 HTTPS:443
2. URL前缀: HTTP:http:// HTTPS:https://
3. 安全性和资源消耗: 
4. HTTP 协议运行在 ==TCP== 之上，所有传输的内容都是==明文==，客户端和服务器端都无法验证对方的身份。HTTPS 是运行在 ==SSL/TLS== 之上的 HTTP 协议，SSL/TLS 运行在 TCP 之上。所有传输的内容都经过加密，加密采用==对称加密==，但对称加密的密钥用服务器方的证书进行了非对称加密。所以说，HTTP 安全性没有 HTTPS 高，==但是 HTTPS 比 HTTP 耗费更多服务器资源。==

## 14. TCP与UDP

### 1. TCP与UDP的区别(重要)

   1.1 **是否面向连接：**TCP在传送数据前必须先建立连接，数据传送结束后要释放连接(==面向连接==)。UDP在传送数据前不需要建立连接(==面向无连接==)

   1.2 **是否可靠:** UDP 不需要给出任何确认，并且不保证数据不丢失，不保证是否顺序到达。通过 TCP 连接传输的数据，无差错、不丢失、不重复、并且按序到达。

   1.3 **是否有状态**：TCP有状态，UDP无状态

   1.4 **传输效率:**UDP效率比TCP高得多

   1.5 **传输形式**: TCP面向字节流，UDP面向报文

   1.6 **首部开销**：TCP 首部开销（20 ～ 60 字节）比 UDP 首部开销（8 字节）要大。

   1.7 **是否提供广播或多播服务**：TCP 只支持点对点通信，UDP 支持一对一、一对多、多对一、多对多；

|                        | TCP            | UDP        |
| ---------------------- | -------------- | ---------- |
| 是否面向连接           | 是             | 否         |
| 是否可靠               | 是             | 否         |
| 是否有状态             | 是             | 否         |
| 传输效率               | 较慢           | 较快       |
| 传输形式               | 字节流         | 数据报文段 |
| 首部开销               | 20 ～ 60 bytes | 8 bytes    |
| 是否提供广播或多播服务 | 否             | 是         |

### 2. 什么时候选择 TCP，什么时候选 UDP?
- **UDP 一般用于即时通信**，比如：语音、 视频、直播等等。这些场景对传输数据的准确性要求不是特别高，比如你看视频即使少个一两帧，实际给人的感觉区别也不大。

- **TCP 用于对传输准确性要求特别高的场景**，比如文件传输、发送和接收邮件、远程登录等等。

### 3. HTTP 基于 TCP 还是 UDP？

HTTP/3.0 之前是基于 TCP 协议的，而 HTTP/3.0 将弃用 TCP，改用 **基于 UDP 的 QUIC 协议** 。

### 4. **运行于 TCP 协议之上的协议**：

- HTTP/3.0之前
- HTTPS
- FTP
- SMTP
- POP3
- Telnet
- SSH

### 5.**运行于 UDP 协议之上的协议**：

- HTTP/3.0
- DHCP协议
- DNS

## 15.redis

### 1.常用命令
```sql
#停止
sudo systemctl stop redis-server
#重新加载redis.conf配置文件
sudo systemctl daemon-reload
# 重启
sudo systemctl restart redis-server
# 启动
sudo systemctl start redis-server
# 停止
sudo systemctl stop redis-server
# 查看redis状态
sudo systemctl status redis-server
# 验证是否连接
redis-cli ping  # 应返回 "PONG"
```
### 2.基本知识
1. Redis 默认以 redis 用户身份运行，需**确保**该用户**有权限读取配置文件和目录**。例如/etc/redis/redis.conf配置文件
1. 配置文件路径错误确认 `redis-server.service` 中指定的配置路径正确：

```sql
# 查看服务文件中的 ExecStart 参数
grep ExecStart /lib/systemd/system/redis-server.service

# 示例输出：
# ExecStart=/usr/bin/redis-server /etc/redis/redis.conf
```

