# ThreadPoolExecutor

```java
    public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler) {
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
```

**corePoolSize**：核心池的大小，在创建了线程池后，线程池中的线程数为0，当有任务来之后，就会创建一个线程去执行任务，当线程池中的线程数目达到corePoolSize后，就会把到达的任务放到缓存队列当中;

**maximumPoolSize**：线程池最大线程数，它表示在线程池中最多能创建多少个线程；这个参数是跟后面的阻塞队列联系紧密的；只有当阻塞队列满了，如果还有任务添加到线程池的话，会尝试new 一个Thread的进行救急处理，立马执行对应的runnable任务；如果继续添加任务到线程池，且线程池中的线程数已经达到了maximumPoolSize，那么线程就会就会执行reject操作（这里后面会提及到）

**keepAliveTime**：表示线程没有任务执行时最多保持多久时间会终止；默认情况下，只有当线程池中的线程数大于corePoolSize时，keepAliveTime才会起作用；即当线程池中的线程数大于corePoolSize时，如果一个线程空闲的时间达到keepAliveTime，则会终止，直到线程池中的线程数不超过corePoolSize。但是如果调用了allowCoreThreadTimeOut(boolean)方法并设置了参数为true，在线程池中的线程数不大于corePoolSize时，keepAliveTime参数也会起作用，直到线程池中的阻塞队列大小为0；（这部分通过查看ThreadPoolExecutor的源码分析--getTask()部分）；

**unit**：参数keepAliveTime的时间单位，有7种取值，在TimeUnit类中有7种静态属性（时间单位）

**workQueue**：一个阻塞队列，用来存储等待执行的任务，这个参数的选择也很重要，会对线程池的运行过程产生重大影响，一般来说，这里的阻塞队列有以下几种选择　　
　　ArrayBlockingQueue;
　　LinkedBlockingQueue;
　　SynchronousQueue;
　　ArrayBlockingQueue和PriorityBlockingQueue使用较少，一般使用LinkedBlockingQueue和Synchronous。线程池的排队策略与BlockingQueue有关。

**threadFactory**：线程工厂，主要用来创建线程：默认值 DefaultThreadFactory；

**handler**：表示当拒绝处理任务时的策略，就是上面提及的reject操作；有以下四种取值：
　　ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。（默认handle）
　　ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。
　　ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
　　ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务

通俗解释：如果把线程池比作一个公司的话，公司创建时会根据业务预计公司有多少个职员。corePoolSize就表示正式员工数量，线程就可以表示一个员工。当公司新接到一个项目的时候，如果公司发现正式工还没招满，公司就会通知HR招聘正式工来完成这个项目。随着公司的发展口碑越做越好项目越来越多，即使正式工全部招满，项目还是干不完。那么公司只能按照项目接收先后顺序将它们找个地方存放起来，这个地方就是workQueue（老板存放项目的书柜），等正式员工完成了手上的工作，就到这里来取新的项目。如果不巧，公司的销售人员项目签单越来越多，导致workQueue已经没有空位置放新的项目，于是单位决定招点临时工吧。临时工也不是想招多少就招多少，老板想赚钱，招聘多了工资发放成本太大，通过maximumPoolSize确定了公司的人数的最大值，换句话说最多招maximumPoolSize–corePoolSize个临时工。当然，在线程池中，谁是正式工，谁是临时工是没有区别。

为了解释keepAliveTime的作用，我们在上述情况下做一种假设。假设线程池这个公司已经招了些临时工，但新项目没有继续增加，所以随着每个员工忙完手头的项目，都来workQueue领取新的项目。随着各个员工齐心协力，项目越来越少，员工数没变，那么就必定有闲着没事干的员工。这样的话老板不乐意啦，但是又不能轻易开除没事干的员工，因为随时可能有新项目来，招聘成本也很高，于是老板想了个办法，设定了keepAliveTime，当空闲的员工在keepAliveTime这段时间还没有找到事情干，就被辞退啦，毕竟资本家不愿意养活一个不干活的员工！当然辞退到corePoolSize个员工时就不再辞退了！

为了解释handler的作用，我们在上述情况下做另一种假设。假设线程池这个公司招满临时工，但新项目依然继续增加，线程池从上到下，从里到外真心忙的不可开交，阻塞队列也满了，只好拒绝客户，不再接新的项目。怎么拒绝是门艺术，handler一般可以采取以下四种取值。
1、ThreadPoolExecutor.AbortPolicy() 抛出RejectedExecutionException异常
2、ThreadPoolExecutor.CallerRunsPolicy() 由向线程池提交任务的线程来执行该任务
3、ThreadPoolExecutor.DiscardOldestPolicy() 抛弃最旧的任务（最先提交而没有得到执行的任务）
4、ThreadPoolExecutor.DiscardPolicy() 抛弃当前的任务

线程池按以下行为执行任务：
  1. 当线程数小于核心线程数时，创建线程。
  2. 当线程数大于等于核心线程数，且任务队列未满时，将任务放入任务队列。
  3. 当线程数大于等于核心线程数，且任务队列已满
      1. 若线程数小于最大线程数，创建线程
      2. 若线程数等于最大线程数，抛出异常，拒绝任务

例1：基本使用
```java
package threadpool;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.io.IOException;
import java.util.concurrent.*;
import java.util.concurrent.atomic.AtomicInteger;

public class ThreadPoolTest1 {
    private static final Logger logger = LoggerFactory.getLogger(ThreadPoolTest1.class);

    public static void main(String[] args) throws InterruptedException, IOException {
        int corePoolSize = 2;
        int maximumPoolSize = 4;
        long keepAliveTime = 10;
        TimeUnit unit = TimeUnit.SECONDS;
        BlockingQueue<Runnable> workQueue = new ArrayBlockingQueue<>(50);
        ThreadFactory threadFactory = new NameTreadFactory();
        RejectedExecutionHandler handler = new MyIgnorePolicy();
        ThreadPoolExecutor executor = new ThreadPoolExecutor(corePoolSize, maximumPoolSize,
                keepAliveTime, unit,
                workQueue, threadFactory, handler);
        executor.prestartAllCoreThreads(); // 预启动所有核心线程

        for (int i = 1; i <= 100; i++) {
            MyTask task = new MyTask(String.valueOf(i));
            executor.execute(task);
//            final int ii = i;
//            executor.execute(new Runnable() {
//                @Override
//                public void run() {
//                    System.out.println("-----" + ii);
//                }
//            });
        }

//        System.in.read(); //阻塞主线程
        executor.shutdown();
    }

    static class NameTreadFactory implements ThreadFactory {

        private final AtomicInteger mThreadNum = new AtomicInteger(1);

        @Override
        public Thread newThread(Runnable r) {
            Thread t = new Thread(r, "my-thread-" + mThreadNum.getAndIncrement());
//            System.out.println(t.getName() + " has been created");
            logger.info(t.getName() + " has been created");
            return t;
        }
    }

    public static class MyIgnorePolicy implements RejectedExecutionHandler {

        public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
            doLog(r, e);
        }

        private void doLog(Runnable r, ThreadPoolExecutor e) {
            // 可做日志记录等
//            System.err.println( r.toString() + " rejected");
            logger.error( r.toString() + " rejected");
//          System.out.println("completedTaskCount: " + e.getCompletedTaskCount());
        }
    }

    static class MyTask implements Runnable {
        private String name;

        public MyTask(String name) {
            this.name = name;
        }

        @Override
        public void run() {
            try {
//                System.out.println(this.toString() + " is running!");
                logger.info(this.toString() + " is running!");
                double x = Math.random() * 1000;
                Long y = 1000 + new Double(x).longValue();
                Thread.sleep(y); //让任务执行慢点
//                System.out.println("----------------------[name=" + this.name + "] cost=" + y);
                logger.info("----------------------[name=" + this.name + "] cost=" + y);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        public String getName() {
            return name;
        }

        @Override
        public String toString() {
            return "MyTask [name=" + name + "]";
        }
    }
}

```

例2：优雅shutdown
```java
package threadpool;

import java.util.*;
import java.util.concurrent.*;

public class ThreadPoolTest3 {
    public static void main(String[] args) {
        List<String> data = getData();
        System.out.println("-----------" + data.size());
        for (String s : data) {
            System.out.println(s);
        }
    }

    private static List<String> getData() {
        int corePoolSize = 4;
        int maximumPoolSize = 4;
        long keepAliveTime = 10;
        TimeUnit unit = TimeUnit.SECONDS;
        BlockingQueue<Runnable> workQueue = new ArrayBlockingQueue<>(200);
        ThreadPoolExecutor executor = new ThreadPoolExecutor(corePoolSize, maximumPoolSize,
                keepAliveTime, unit,
                workQueue,
                new RejectedExecutionHandlerImpl());
        executor.prestartAllCoreThreads();


        List<String> data = Collections.synchronizedList(new ArrayList<>());
//        Vector<String> data = new Vector<>();
//        List<String> data = new ArrayList<>();
        for (int i = 1; i <= 10; i++) {
            final int ii = i;
            executor.execute(() -> {
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                List<String> list = new ArrayList<>();
                for (int j = 1; j <=10;j++) {
                    list.add(ii + "-" + j);
                }
                data.addAll(list);
//                    System.out.println("-----" + ii);
            });
        }

        final long awaitTime = 5 * 1000;
        try {
            // 向学生传达“问题解答完毕后请举手示意！”
            executor.shutdown();
            // 向学生传达“XX分之内解答不完的问题全部带回去作为课后作业！”后老师等待学生答题
            // (所有的任务都结束的时候，返回TRUE)
            if(!executor.awaitTermination(awaitTime, TimeUnit.MILLISECONDS)){
                // 超时的时候向线程池中所有的线程发出中断(interrupted)。
                executor.shutdownNow();
            }
        } catch (InterruptedException e) {
            // awaitTermination方法被中断的时候也中止线程池中全部的线程的执行。
            System.out.println("awaitTermination interrupted: " + e);
            executor.shutdownNow();
        }
        return data;
    }

    private static class RejectedExecutionHandlerImpl implements RejectedExecutionHandler{

        @Override
        public void rejectedExecution(Runnable r, ThreadPoolExecutor executor) {
            try{
                System.out.println("重回队列");
                executor.getQueue().put(r);
            }catch (Exception e){
            }
        }
    }
}

```

例3：变种
```java
package threadpool;

import java.util.concurrent.Executor;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ThreadPoolTest4 {
    public static void main(String[] args) {
        ExecutorService s1 = Executors.newCachedThreadPool();
        ExecutorService s2 = Executors.newFixedThreadPool(3);
        ExecutorService s3 = Executors.newScheduledThreadPool(3);
        ExecutorService s4 = Executors.newSingleThreadExecutor();

        for (int i = 0; i < 10; i++) {
            final int index = i;
            s1.execute(() -> {
                try {
                    System.out.println(index);
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }
    }
}

```