title: 关于java中的synchronized的用法
---

# 今天要分享的内容是synchronized
这个地方也是，说起来知道同步的用法，但是并不是多么的清楚，和上面的那篇mysql的子查询一样，看到代码中很多这样写的。
就顺便研究总结一下： 
<!-- more --> 

# 修饰对象有几种(能出现在什么地方)
- 修饰一个类，其作用的范围是synchronized后面括号括起来的部分， 作用的对象是这个类的所有对象。
- 修饰一个方法，被修饰的方法称为同步方法，其作用的范围是整个方法， 作用的对象是调用这个方法的对象；
- 修改一个静态的方法，其作用的范围是整个静态方法， 作用的对象是这个类的所有对象；
- 修饰一个代码块，被修饰的代码块称为同步语句块，其作用的范围是大括号{}括起来的代码， 作用的对象是调用这个代码块的对象；

# 2个锁
上面的4种情况，核心也就是，2个锁

1. 对象锁，修饰一个类的成员方法和代码块，就是这种情况，他强调的是创建对象，调用对象本身，比如，a对象和b对象完全不同
2. 类锁（其实没有，类比概念，好理解），也就是修饰类，和静态的方法，这两种的同步是一样的，他们强调的是类class这个对象的锁，因此a对象和b对象拥有同样的锁，所以是相同的

# 废话不多说，直接上例子吧

```angularjs
/**
 * @author hankun
 * @create 2017-07-05 19:52
 */
public class synchronizedTest {


    /**
     * 同步线程,修饰方法块
     */
    static class SyncMehtodBlock implements Runnable {
        private static int count;

        public SyncMehtodBlock() {
            count = 0;
        }

        public void run() {
            synchronized (this) {
                System.out.println(Thread.currentThread().getName() + "开始..SyncMehtodBlock");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + "结束..SyncMehtodBlock");
            }
        }

    }


    /**
     * 同步线程,修饰方法
     */
    static class SyncMehtod extends Thread {
        private synchronizedTest syn;

        public SyncMehtod(synchronizedTest synchronizedTest) {
            this.syn = synchronizedTest;
        }

        public void run() {
            syn.test();
        }

    }

    public synchronized void test() {
        System.out.println(Thread.currentThread().getName() + "开始..SyncMehtod");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + "结束..SyncMehtod");
    }


    /**
     * 同步线程,修饰静态方法
     */
    static class SyncStaticMehtod extends Thread {

        public synchronized static void staticTest() {
            System.out.println(Thread.currentThread().getName() + "开始..SyncStaticMehtod");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "结束..SyncStaticMehtod");
        }

        public void run() {
            staticTest();
        }

    }

    /**
     * 同步线程,修饰类
     */
    static class SyncClass extends Thread {

        public void classTest() {
            synchronized (synchronizedTest.class) {
                System.out.println(Thread.currentThread().getName() + "开始..SyncClass");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + "结束..SyncClass");
            }
        }

        public void run() {
            classTest();
        }

    }


    public static void main(String[] args) {
        //情况1，修饰方法块
        SyncMehtodBlock syncMehtodBlock = new SyncMehtodBlock();
        Thread thread1 = new Thread(syncMehtodBlock, "syncMehtodBlock1");
        Thread thread2 = new Thread(syncMehtodBlock, "syncMehtodBlock2");
        thread1.start();
        thread2.start();


        //情况2，修饰方法
        synchronizedTest syn = new synchronizedTest();
        SyncMehtod syncMehtod3 = new SyncMehtod(syn);
        SyncMehtod syncMehtod4 = new SyncMehtod(syn);
        syncMehtod3.start();
        syncMehtod4.start();


        //情况3，修饰静态方法
        SyncStaticMehtod a = new SyncStaticMehtod();
        SyncStaticMehtod b = new SyncStaticMehtod();
        Thread thread5 = new Thread(a, "SyncStaticMehtod1");
        Thread thread6 = new Thread(b, "SyncStaticMehtod2");
        thread5.start();
        thread6.start();


        //情况4，修饰类
        SyncClass c = new SyncClass();
        SyncClass d = new SyncClass();
        Thread thread7 = new Thread(c, "SyncClass1");
        Thread thread8 = new Thread(d, "SyncClass2");
        thread7.start();
        thread8.start();

    }
}

```
我写了一个，模拟4种情况的例子，报每个类型都包含进去了

请注意观察里面的synchronized的关键位置，出现的情况，以及每个测试例子，是如何新建的，区分不同

所有的例子，都是具有同步的，因为竞争，比如，a先开始，那么a先结束，然后才能轮到b开始，b结束

好好，体会里面的内容

# 总结

1. 无论synchronized关键字加在方法上还是对象上，如果它作用的对象是非静态的，则它取得的锁是对象；如果synchronized作用的对象是一个静态方法或一个类，则它取得的锁是对类，该类所有的对象同一把锁。
2. 每个对象只有一个锁（lock）与之相关联，谁拿到这个锁谁就可以运行它所控制的那段代码。
3. 实现同步是要很大的系统开销作为代价的，甚至可能造成死锁，所以尽量避免无谓的同步控制。

我觉得别人说的，这三句话，已经说的很明白不过，我也就不加自己的体会了
