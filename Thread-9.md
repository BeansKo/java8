#### 第九章—线程池及Executor框架
##### 1 为什么要使用线程池？
		诸如 Web 服务器、数据库服务器、文件服务器或邮件服务器之类的许多服务器应用程序都面向处理来自某些远程来源的大量短小的任务。请求以某种方式到达服务器，这种方式可能是通过网络协议（例如 HTTP、FTP ）、通过 JMS队列或者可能通过轮询数据库。	不管请求如何到达，服务器应用程序中经常出现的情况是：单个任务处理的时间很短而请求的数目却是巨大的。每当一个请求到达就创建一个新线程，然后在新线程中为请求服务，但是频繁的创建线程，销毁线程所带来的系统开销其实是非常大的。

		线程池为线程生命周期开销问题和资源不足问题提供了解决方案。通过对多个任务重用线程，线程创建的开销被分摊到了多个任务上。其好处是，因为在请求到达时线程已经存在，所以无意中也消除了线程创建所带来的延迟。这样，就可以立即为请求服务，使应用程序响应更快。而且，通过适当地调整线程池中的线程数目，也就是当请求的数目超过某个阈值时，就强制其它任何新到的请求一直等待，直到获得一个线程来处理为止，从而可以防止资源不足。

		风险与机遇
			用线程池构建的应用程序容易遭受任何其它多线程应用程序容易遭受的所有并发风险，
			诸如同步错误和死锁，它还容易遭受特定于线程池的少数其它风险，诸如与池有关的死锁、资源不足和线程泄漏。

##### 2 创建线程池及其使用
		详见视频



##### 3 Future与Callable、FutureTask
		Callable与Runable功能相似，Callable的call有返回值，可以返回给客户端，而Runable没有返回值，一般情况下，Callable与FutureTask一起使用，或者通过线程池的submit方法返回相应的Future

		Future就是对于具体的Runnable或者Callable任务的执行结果进行取消、查询是否完成、获取结果、设置结果操作。get方法会阻塞，直到任务返回结果

		FutureTask则是一个RunnableFuture，而RunnableFuture实现了Runnbale又实现了Futrue这两个接口




##### 4 线程池的核心组成部分及其运行机制
		corePoolSize：核心线程池大小 cSize
		maximumPoolSize：线程池最大容量  mSize
		keepAliveTime：当线程数量大于核心时，多余的空闲线程在终止之前等待新任务的最大时间。
		unit：时间单位
		workQueue:工作队列 nWorks
		ThreadFactory：线程工厂
		handler：拒绝策略

		运行机制
			通过new创建线程池时，除非调用prestartAllCoreThreads方法初始化核心线程，否则此时线程池中有0个线程，即使工作队列中存在多个任务，同样不会执行

			任务数X
			x <= cSize  只启动x个线程

			x >= cSize && x < nWorks + cSize  会启动 <= cSize 个线程 其他的任务就放到工作队列里

			x > cSize && x > nWorks + cSize
				x-(nWorks) <= mSize  会启动x-(nWorks)个线程
				x-(nWorks) > mSize   会启动mSize个线程来执行任务，其余的执行相应的拒绝策略

##### 5 线程池拒绝策略
		AbortPolicy：该策略直接抛出异常，阻止系统正常工作
		CallerRunsPolicy：只要线程池没有关闭，该策略直接在调用者线程中，执行当前被丢弃的任务（叫老板帮你干活）
		DiscardPolicy：直接啥事都不干，直接把任务丢弃
		DiscardOldestPolicy：丢弃最老的一个请求（任务队列里面的第一个），再尝试提交任务




##### 6 Executor框架
		通过相应的方法，能创建出6种线程池
		ExecutorService executorService = Executors.newCachedThreadPool();
		ExecutorService executorService1 = Executors.newFixedThreadPool(2);
		ScheduledExecutorService scheduledExecutorService = Executors.newScheduledThreadPool(1);
		ExecutorService executorService2 = Executors.newWorkStealingPool();
		ExecutorService executorService3 = Executors.newSingleThreadExecutor();
		ScheduledExecutorService scheduledExecutorService1 = Executors.newSingleThreadScheduledExecutor();

		上面的方法最终都创建了ThreadPoolExecutor
		newCachedThreadPool：创建一个可以根据需要创建新线程的线程池，如果有空闲线程，优先使用空闲的线程
		newFixedThreadPool：创建一个固定大小的线程池，在任何时候，最多只有N个线程在处理任务
		newScheduledThreadPool：能延迟执行、定时执行的线程池
		newWorkStealingPool：工作窃取，使用多个队列来减少竞争
		newSingleThreadExecutor：单一线程的线程次，只会使用唯一一个线程来执行任务，即使提交再多的任务，也都是会放到等待队列里进行等待
		newSingleThreadScheduledExecutor：单线程能延迟执行、定时执行的线程池


##### 7 线程池的使用建议
    		尽量避免使用Executor框架创建线程池
    		newFixedThreadPool  newSingleThreadExecutor
    		允许的请求队列长度为 Integer.MAX_VALUE，可能会堆积大量的请求，从而导致 OOM。

    		newCachedThreadPool newScheduledThreadPool
    		允许的创建线程数量为 Integer.MAX_VALUE，可能会创建大量的线程，从而导致 OOM

    		为什么第二个例子，在限定了堆的内存之后，还会把整个电脑的内存撑爆
    			创建线程时用的内存并不是我们制定jvm堆内存，而是系统的剩余内存。（电脑内存-系统其它程序占用的内存-已预留的jvm内存）

		创建线程池时，核心线程数不要过大

		相应的逻辑，发生异常时要处理

		submit 如果发生异常，不会立即抛出，而是在get的时候，再抛出异常
		execute 直接抛出异常

