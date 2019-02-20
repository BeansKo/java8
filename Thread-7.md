#### 第七章--容器
##### 1 同步容器与并发容器

		同步容器
			Vector、HashTable -- JDK提供的同步容器类
			Collections.synchronizedXXX 本质是对相应的容器进行包装

			同步容器类的缺点
				在单独使用里面的方法的时候，可以保证线程安全，但是，复合操作需要额外加锁来保证线程安全
			使用Iterator迭代容器或使用使用for-each遍历容器，在迭代过程中修改容器会抛出ConcurrentModificationException异常。想要避免出现ConcurrentModificationException，就必须在迭代过程持有容器的锁。但是若容器较大，则迭代的时间也会较长。那么需要访问该容器的其他线程将会长时间等待。从而会极大降低性能。
			若不希望在迭代期间对容器加锁，可以使用"克隆"容器的方式。使用线程封闭，由于其他线程不会对容器进行修改，可以避免ConcurrentModificationException。但是在创建副本的时候，存在较大性能开销。
			toString，hashCode，equalse，containsAll，removeAll，retainAll等方法都会隐式的Iterate，也即可能抛出ConcurrentModificationException。

		并发容器
			CopyOnWrite、Concurrent、BlockingQueue
			根据具体场景进行设计，尽量避免使用锁，提高容器的并发访问性。
			ConcurrentBlockingQueue：基于queue实现的FIFO的队列。队列为空，取操作会被阻塞
			ConcurrentLinkedQueue，队列为空，取得时候就直接返回空
			
##### 2 LinkedBlockingQueue的使用及其源码探秘
		在并发编程中，LinkedBlockingQueue使用的非常频繁。因其可以作为生产者消费者的中间商

		add  实际上调用的是offer，区别是在队列满的时候，add会报异常
		offer  对列如果满了，直接入队失败
		put("111"); 在队列满的时候，会进入阻塞的状态

        	remove(); 直接调用poll，唯一的区别即使remove会抛出异常，而poll在队列为空的时候直接返回null
        	poll(); 在队列为空的时候直接返回null
        	take(); 在队列为空的时候，会进入等待的状态
