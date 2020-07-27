# Java 多线程

#### 1. 线程实现的 6 种方式

方式1: 继承 Thread 类，重写 run 方法。
方式2: 实现 Runnable 接口，实现 run 方法。
方式3: 匿名内部类。
方式4: 定时器 Timer 定时任务。
方式5: 实现 Callable 接口，实现 call 方法，带有异常和返回值。
方式6: 线程池。

```java
// 方式1: 继承 Thread 类，重写 run 方法。
public MyThread extends Thread {
    @Override
    void run() { }
}
```

```java
// 方式2: 实现 Runnable 接口，实现 run 方法。
// 实现了线程任务与线程控制分离。一个任务可以构造多个线程对象
public Mythread implements Runnable {
    public void run() { }
}
```

```java
// 方式3: 匿名内部类。
// 适合线程任务就执行一次，以后不用了。
new Thread(){
    @Override
    public void run() { }
}.start()

new Thread(new Runnable {
    public void run() { }
}).start();

new Thread(() -> task()).start();
```

```java
// 方式4: 定时器 Timer 定时任务。
Timer timer = new Timer();
timer.schedule(new TimerTask() {
    @Override
    public void run() { }
}, new Date(), 3000);
```

```java
// 方式5: 实现 Callable 接口，实现 call 方法，
// 带有异常和返回值。
FutureTask<Integer> task = new FutureTask<>(() -> {
    foo();
    return 1;
});
new Thread(task).start();
int res = task.get();
```

```java
// 方式6: 线程池。
// 线程的创建/销毁非常浪费资源。
ExecutorService pool = Executors.newFixedThreadPool(3);
for (int i = 0; i < 10; i ++) {
    pool.execute(() -> foo());
}
pool.shutdown();
```
线程池优点：
1) 降低了资源消耗。
2) 提高响应速度。
3) 提高线程可管理性。

#### 2. java 执行程序最少有 2 个线程。

一个是主线程 Main，一个是JVM垃圾回收线程。

#### 3. 并发与并行

并行：多个任务时间**同一时刻**进行。每个核心执行一个任务。
并发：多个任务**同一时间段**内进行。单核CPU不停的切换，看起来像同时进行。

