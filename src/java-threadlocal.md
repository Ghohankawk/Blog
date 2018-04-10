---
title: 关于Java中的ThreadLocal用法
---

 
# 今天要分享的内容是java中的ThreadLocal

并发编程中，一个重要的内容是数据共享。当你创建了实现Runnable接口的线程，然后开启使用相同Runnable实例的各种Thread对象，所有 的线程便共享定义在Runnable对象中的属性。也就是说，当你在一个线程中改变任意属性时，所有的线程都会因此受到影响，同时会看到第一个线程修改后的值。有时我们希望如此，比如：多个线程增大或减小同一个计数器变量；但是，有时我们希望确保每个线程，只能工作在它自己的线程实例的拷贝上，同时不会影 响其他线程的数据。
<!-- more --> 

# ThreadLocal是什么
  
  ThreadLocal是一个关于创建线程局部变量的类。
  
  通常情况下，我们创建的变量是可以被任何一个线程访问并修改的。而使用ThreadLocal创建的变量只能被当前线程访问，其他线程则无法访问和修改。

Java中的ThreadLocal类允许我们创建只能被同一个线程读写的变量。因此，如果一段代码含有一个ThreadLocal变量的引用，即使两个线程同时执行这段代码，它们也无法访问到对方的ThreadLocal变量。

- 在当前线程中，任何一个点都可以访问到ThreadLocal的值。
- 该线程的ThreadLocal只能被该线程访问，一般情况下其他线程访问不到。

# 如何创建ThreadLocal变量
  
    private ThreadLocal myThreadLocal = new ThreadLocal();
  通过这段代码实例化了一个ThreadLocal对象。我们只需要实例化对象一次，并且也不需要知道它是被哪个线程实例化。虽然所有的线程都能访问到这个ThreadLocal实例，但是每个线程却只能访问到自己通过调用ThreadLocal的set()方法设置的值。即使是两个不同的线程在同一个ThreadLocal对象上设置了不同的值，他们仍然无法访问到对方的值。

# 用法简介
  
  1. 创建，支持泛型
  
    ThreadLocal<String> mStringThreadLocal = new ThreadLocal<>();
  2. set方法
  
    mStringThreadLocal.set("hank");
  3. get方法
    
    mStringThreadLocal.get();
  4. initialValue()：返回当前线程赋予局部线程变量的初始值。
# 具体用例

````angularjs
/**
 * @author hankun
 * @create 2017-06-20 20:33
 */
public class threadlocal {
    public static class MyRunnable implements Runnable {

        private ThreadLocal threadLocal = new ThreadLocal();

        @Override
        public void run() {
            threadLocal.set((int) (Math.random() * 100D));
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {

            }
            System.out.println(Thread.currentThread().getName() + "  ==  " + threadLocal.get());
        }
    }

    public static void main(String[] args) {
        MyRunnable my = new MyRunnable();
        Thread thread1 = new Thread(my);
        Thread thread2 = new Thread(my);
        thread1.start();
        thread2.start();
    }
}
````

# 关于InheritableThreadLocal
InheritableThreadLocal类是ThreadLocal类的子类。ThreadLocal中每个线程拥有它自己的值，与ThreadLocal不同的是，InheritableThreadLocal允许一个线程以及该线程创建的所有子线程都可以访问它保存的值。

【注：所有子线程都会继承父线程保存的ThreadLocal值】


# 总结
局部线程通常使用在这样的情况下，当你有一些对象并不满足线程安全，但是你想避免在使用synchronized关键字、块时产生的同步访问，那么，让每个线程拥有它自己的对象实例。

注意：局部变量是同步或局部线程的一个好的替代，它总是能够保证线程安全。唯一可能限制你这样做的是你的应用设计约束。


这个地方，是因为看到项目中有些地方都有用到，所以自己也研究一下

单从用法而言，还是比较简单的，没啥难度，要是研究原理的问题的话，可以考虑深入看一下源代码

还有就是

思考一下这种方式，变量对象，是放在堆里面吗？还是堆里面？

答案是堆。

