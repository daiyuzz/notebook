# Java 之 synchronized详解
## 概念
synchronized是java中的关键字，是利用锁的机制来实现同步的。

锁机制有如下两种特征：
- 互斥性：即在同一时间只允许一个线程持有某个对象锁，通过这种特性来实现多线程的协调机制，这样在同一时间只有一个线程对需同步的代码块（符合操作）进行访问。互斥性我们也往往称为操作的原子性。
- 可见性：必须确保在锁被释放之前，对共享变量所做的修改，对于随后获得的该锁的另一个线程是可见的（即在获得锁时应获得最新共享的值），否则另一个线程可能是在本地缓存的某个副本上继续操作从而引起不一致。


## 对象锁和类锁

### 对象锁
在java中，每一个对象都会有一个 monitor 对象，这个对象其实就是java对象的锁，通常被称为`内置锁`或`对象锁`。类的对象可以有多个，所以每个对象都有其独立的对象锁，互不干扰。


### 类锁
在java中，针对每个类也有一个锁，可以称为`类锁`，类锁实际上是通过对象锁实现的，即类的Class对象锁，每个类只有一个 Class 对象，所以每个类只有一个锁

## synchronized 的用法分类

synchronized 的用法可以从两个维度上面分类：

### 根据修饰对象分类
synchronized 可以修饰方法和代码块
- 修饰代码块
    - synchronized(this|object){}
    - synchronized(类.class){}
- 修饰方法
    - 修饰非静态方法
    - 修饰静态方法

### 根据获取的锁分类
- 获取对象锁
    - synchronized(this|object){}
    - 修饰非静态方法
- 获取类锁
    -  synchronized(类.class){}
    - 修饰静态方法

## synchronized 的用法详解
这里根据获取的锁分类来分析 synchronized 的用法

### 获取对象锁

#### 对于同一对象

同步线程类

```java
package com.suo.java;

import java.text.SimpleDateFormat;
import java.util.Date;

public class SyncThread implements Runnable {
    @Override
    public void run() {
        String threadName = Thread.currentThread().getName();
        if (threadName.startsWith("A")) {
            async();
        } else if (threadName.startsWith("B")) {
            sync1();
        } else if (threadName.startsWith("C")) {
            sync2();
        }
    }

    /**
     * 异步方法
     */

    /**
     * sychronized 修饰非静态方法
     */
    private synchronized void sync2() {
        System.out.println(Thread.currentThread().getName() + "_Sync2:" + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        try {
            System.out.println(Thread.currentThread().getName() + "_Async2_Start:" + new SimpleDateFormat("HH:mm:ss").format(new Date()));
            Thread.sleep(2000);
            System.out.println(Thread.currentThread().getName() + "Async2_End:" + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    /**
     * 方法中有 synchronized(this|object){}同步代码块
     */
    private void sync1() {
        System.out.println(Thread.currentThread().getName() + "_Sync1:" + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        synchronized (this) {
            try {
                System.out.println(Thread.currentThread().getName() + "_Sync1_Start:" + new SimpleDateFormat("HH:mm:ss").format(new Date()));
                Thread.sleep(2000);
                System.out.println(Thread.currentThread().getName() + "Async1_End:" + new SimpleDateFormat("HH:mm:ss").format(new Date()));
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    private void async() {
        try {
            System.out.println(Thread.currentThread().getName() + "_Async_Start:" + new SimpleDateFormat("HH:mm:ss").format(new Date()));
            Thread.sleep(2000);
            System.out.println(Thread.currentThread().getName() + "Async_End:" + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

```
测试代码
```java
package com.suo.java;

public class SyncTest {
    public static void main(String... args) {
        SyncThread syncThread = new SyncThread();
        Thread A_thread1 = new Thread(syncThread, "A_thread1");
        Thread A_thread2 = new Thread(syncThread, "A_thread2");
        Thread B_thread1 = new Thread(syncThread, "B_thread1");
        Thread B_thread2 = new Thread(syncThread, "B_thread2");
        Thread C_thread1 = new Thread(syncThread, "C_thread1");
        Thread C_thread2 = new Thread(syncThread, "C_thread2");
        A_thread1.start();
        A_thread2.start();
        B_thread1.start();
        B_thread2.start();
        C_thread1.start();
        C_thread2.start();
    }
}
```

运行结果：
```
B_thread2_Sync1:16:44:12
A_thread2_Async_Start:16:44:12
A_thread1_Async_Start:16:44:12
B_thread1_Sync1:16:44:12
C_thread1_Sync2:16:44:12
C_thread1_Async2_Start:16:44:12
A_thread1Async_End:16:44:14
A_thread2Async_End:16:44:14
C_thread1Async2_End:16:44:14
B_thread1_Sync1_Start:16:44:14
B_thread1Async1_End:16:44:16
B_thread2_Sync1_Start:16:44:16
B_thread2Async1_End:16:44:18
C_thread2_Sync2:16:44:18
C_thread2_Async2_Start:16:44:18
C_thread2Async2_End:16:44:20
```

结果分析：
- A类线程访问中没有同步代码块，A类线程是异步的，所有线程访问对象的同步代码块时，另外的线程可以访问该对象的非同步代码块
```
A_thread1_Async_Start:16:44:12
A_thread1_Async_Start:16:44:12
A_thread1Async_End:16:44:14
A_thread2Async_End:16:44:14
```
- B类线程访问的方法中有同步代码块，B类线程是同步的，一个线程在访问对象的同步代码块，另一个访问对象的同步代码块的线程会被阻塞：
```
B_thread1_Sync1_Start:16:44:14
B_thread1Async1_End:16:44:16
B_thread2_Sync1_Start:16:44:16
B_thread2Async1_End:16:44:18
```

- synchronized(this|object){} 代码块 {} 之外的代码依然是异步的：

```
B_thread2_Sync1:16:44:12
B_thread1_Sync1:16:44:12
```

- C类线程访问的是 synchronized 修饰的非静态方法，C 类线程是同步的，一个线程在访问对象的同步方法，另一个访问对象的同步方法的线程会被阻塞。
```
C_thread1_Async2_Start:16:44:12
C_thread1Async2_End:16:44:14
C_thread2_Async2_Start:16:44:18
C_thread2Async2_End:16:44:20
```

- synchronized 修饰非静态方法，作用范围是整个方法，所以方法中所有的代码都是同步的：
```
C_thread1_Sync2:16:44:12
C_thread2_Sync2:16:44:18
```
- 由结果可知 B 类和C类线程顺序执行，`类中 synchronized(this|object){} 代码块和 synchronized 修饰非静态方法获取的锁是同一把锁，即该类的对象的对象锁。`所以B类线程和C类线程也是同步的。
```
C_thread1_Async2_Start:16:44:12
C_thread1Async2_End:16:44:14
B_thread1_Sync1_Start:16:44:14
B_thread1Async1_End:16:44:16
B_thread2_Sync1_Start:16:44:16
B_thread2Async1_End:16:44:18
C_thread2_Async2_Start:16:44:18
C_thread2Async2_End:16:44:20
```

#### 对于不同对象
修改测试代码为：
```java
package com.suo.java;

public class SyncTest2 {
    public static void main(String... args) {
        Thread A_thread1 = new Thread(new SyncThread(), "A_thread1");
        Thread A_thread2 = new Thread(new SyncThread(), "A_thread2");
        Thread B_thread1 = new Thread(new SyncThread(), "B_thread1");
        Thread B_thread2 = new Thread(new SyncThread(), "B_thread2");
        Thread C_thread1 = new Thread(new SyncThread(), "C_thread1");
        Thread C_thread2 = new Thread(new SyncThread(), "C_thread2");
        A_thread1.start();
        A_thread2.start();
        B_thread1.start();
        B_thread2.start();
        C_thread1.start();
        C_thread2.start();
    }
}

```
运行结果：
```
C_thread2_Sync2:17:12:23
A_thread1_Async_Start:17:12:23
B_thread2_Sync1:17:12:23
C_thread2_Async2_Start:17:12:23
A_thread2_Async_Start:17:12:23
B_thread2_Sync1_Start:17:12:23
B_thread1_Sync1:17:12:23
C_thread1_Sync2:17:12:23
B_thread1_Sync1_Start:17:12:23
C_thread1_Async2_Start:17:12:23
A_thread1Async_End:17:12:25
C_thread2Async2_End:17:12:25
A_thread2Async_End:17:12:25
C_thread1Async2_End:17:12:25
B_thread2Async1_End:17:12:25
B_thread1Async1_End:17:12:25
```

结果分析：
- 两个线程访问不同对象的 synchronized(this|object){}代码块和synchronized 修饰非静态方法是异步的，同一个类的不同对象的对象锁互不干扰

### 获取类锁

#### 对于同一对象

同步线程类

```java
package com.Threadclass.java;

import java.text.SimpleDateFormat;
import java.util.Date;

public class SyncThread implements Runnable {
    @Override
    public void run() {
        String threadName = Thread.currentThread().getName();
        if (threadName.startsWith("A")) {
            async();
        } else if (threadName.startsWith("B")) {
            asyc1();
        } else if (threadName.startsWith("C")) {
            asyc2();
        }
    }


    /**
     * 异步方法
     */
    private void async() {
        try {
            System.out.println(Thread.currentThread().getName() + "_Async_Start:" + new SimpleDateFormat("HH:mm:ss").format(new Date()));
            Thread.sleep(2000);
            System.out.println(Thread.currentThread().getName() + "_Async_End:" + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    /**
     * 方法中有 synchronized(类.class){} 同步代码
     */
    private void asyc1() {
        System.out.println(Thread.currentThread().getName() + "_Sync1:" + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        synchronized (SyncThread.class) {
            try {
                System.out.println(Thread.currentThread().getName() + "_Sync1_Start:" + new SimpleDateFormat("HH:mm:ss").format(new Date()));
                Thread.sleep(2000);
                System.out.println(Thread.currentThread().getName() + "_Sync1_End:" + new SimpleDateFormat("HH:mm:ss").format(new Date()));

            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }


    /**
     * synchronized 修饰静态方法
     */
    private synchronized static void asyc2() {
        System.out.println(Thread.currentThread().getName() + "_Sync2:" + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        try{
            System.out.println(Thread.currentThread().getName() + "_Sync2_Start:" + new SimpleDateFormat("HH:mm:ss").format(new Date()));
            Thread.sleep(2000);
            System.out.println(Thread.currentThread().getName() + "_Sync2_End:" + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        }catch (InterruptedException e){
            e.printStackTrace();
        }
    }
}



```
测试代码
```java
package com.Threadclass.java;

import com.sun.corba.se.impl.orbutil.concurrent.Sync;

public class SyncTest {
    public static void main(String... args) {
        SyncThread syncThread = new SyncThread();

        Thread A_thread1 = new Thread(syncThread, "A_thread1");
        Thread A_thread2 = new Thread(syncThread, "A_thread2");
        Thread B_thread1 = new Thread(syncThread, "B_thread1");
        Thread B_thread2 = new Thread(syncThread, "B_thread2");
        Thread C_thread1 = new Thread(syncThread, "C_thread1");
        Thread C_thread2 = new Thread(syncThread, "C_thread2");
        A_thread1.start();
        A_thread2.start();
        B_thread1.start();
        B_thread2.start();
        C_thread1.start();
        C_thread2.start();

    }
}

```
运行结果：
```
B_thread1_Sync1:19:27:57
A_thread2_Async_Start:19:27:57
C_thread1_Sync2:19:27:57
B_thread2_Sync1:19:27:57
A_thread1_Async_Start:19:27:57
C_thread1_Sync2_Start:19:27:57
A_thread2_Async_End:19:27:59
A_thread1_Async_End:19:27:59
C_thread1_Sync2_End:19:27:59
B_thread2_Sync1_Start:19:27:59
B_thread2_Sync1_End:19:28:01
B_thread1_Sync1_Start:19:28:01
B_thread1_Sync1_End:19:28:03
C_thread2_Sync2:19:28:03
C_thread2_Sync2_Start:19:28:03
C_thread2_Sync2_End:19:28:05
```

结果分析：
- 有结果可以看出，在同一对象情况下，synchronized(类.class){}代码块或synchronized修饰静态方法