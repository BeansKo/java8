#### 第五章--线程间的通信
##### 01 wait、notify、notifyAll

	何时使用
		在多线程环境下，有时候一个线程的执行，依赖于另外一个线程的某种状态的改变，这个时候，我们就可以使用wait与notify或者notifyAll

	wait跟sleep的区别
		wait会释放持有的锁，而sleep不会，sleep只是让线程在指定的时间内，不去抢占cpu的资源

	注意点
		wait notify必须放在同步代码块中, 且必须拥有当前对象的锁，即不能取得A对象的锁，而调用B对象的wait
		哪个对象wait，就得调哪个对象的notify

	notify跟notifyAll的区别
		nofity随机唤醒一个等待的线程
		notifyAll唤醒所有在该对象上等待的线程

##### 02 等待通知经典模型之生产者消费者
 	生产者消费者模型一般包括：生产者、消费者、中间商

##### 03 使用管道流进行通信
	以内存为媒介，用于线程之间的数据传输。
	主要有面向字节：【PipedOutputStream、PipedInputStream】、面向字符【PipedReader、PipedWriter】

##### 04 Thread.join通信及其源码浅析
	使用场景：线程A执行到一半，需要一个数据，这个数据需要线程B去执行修改，只有B修改完成之后，A才能继续操作
	线程A的run方法里面，调用线程B的join方法，这个时候，线程A会等待线程B运行完成之后，再接着运行

##### 05 ThreadLocal的使用
 	线程变量，是一个以ThreadLocal对象为键、任意对象为值的存储结构。为每个线程单独存放一份变量副本，也就是说一个线程可以根据一个ThreadLocal对象查询到绑定在这个线程上的一个值。
 	只要线程处于活动状态并且ThreadLocal实例可访问，那么每个线程都拥有对其本地线程副本的隐式引用变量一个线程消失后，它的所有副本线程局部实例受垃圾回收（除非其他存在对这些副本的引用）

	一般用的比较多的是
	1、ThreadLocal.get: 获取ThreadLocal中当前线程共享变量的值。
	2、ThreadLocal.set: 设置ThreadLocal中当前线程共享变量的值。
	3、ThreadLocal.remove: 移除ThreadLocal中当前线程共享变量的值。
	4、ThreadLocal.initialValue: ThreadLocal没有被当前线程赋值时或当前线程刚调用remove方法后调用get方法，返回此方法值。


##### 06 Condition的使用
	可以在一个锁里面，存在多种等待条件
	主要的方法
		await
		signal
		signalAll
