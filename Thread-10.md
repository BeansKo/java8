第十章--jvm与并发

	1 jvm内存模型
		硬件内存模型
			处理器--》高速缓存--》缓存一致性协议--》主存
		java内存模型
			线程《--》工作内存《--》save和load 《---》主存
		
		java内存间的交互操作
			（1）lock(锁定)：作用于主内存的变量，把一个变量标记为一条线程独占状态
			（2）unlock(解锁)：作用于主内存的变量，把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定
			（3）read(读取)：作用于主内存的变量，把一个变量值从主内存传输到线程的工作内存中，以便随后的load动作使用
			（4）load(载入)：作用于工作内存的变量，它把read操作从主内存中得到的变量值放入工作内存的变量副本中
			（5）use(使用)：作用于工作内存的变量，把工作内存中的一个变量值传递给执行引擎
			（6）assign(赋值)：作用于工作内存的变量，它把一个从执行引擎接收到的值赋给工作内存的变量
			（7）store(存储)：作用于工作内存的变量，把工作内存中的一个变量的值传送到主内存中，以便随后的write的操作
			（8）write(写入)：作用于主内存的变量，它把store操作从工作内存中的一个变量的值传送到主内存的变量中

			上面8中操作必须满足以下规则
				1、不允许read和load、store和write操作之一单独出现，即不允许一个变量从主内存读取了但工作内存不接受，或者从工作内存发起回写了但主内存不接受的情况出现。
				2、不允许一个线程丢弃它的最近的assign操作，即变量在工作内存中改变了之后必须把该变化同步回主内存。
				3、不允许一个线程无原因地（没有发生过任何assign操作）把数据从线程的工作内存同步回主内存。
				4、一个新的变量只能在主内存中“诞生”，不允许在工作内存中直接使用一个未被初始化（load或assign）的变量，换句话说，就是对一个变量实施use、store操作之前，必须先执行过了assign和load操作。
				5、一个变量在同一时刻只允许一条线程对其进行lock操作，但lock操作可以被同一条线程重复执行多次，多次执行lock后，只有执行相同次数的unlock操作，变量才会被解锁。
				6、如果对一个变量执行lock操作，那将会清空工作内存中此变量的值，在执行引擎使用这个变量前，需要重新执行load或assign操作初始化变量的值。
				7、如果一个变量事先没有被lock操作锁定，那就不允许对它执行unlock操作，也不允许去unlock一个被其他线程锁定住的变量。
				8、对一个变量执行unlock操作之前，必须先把此变量同步回主内存中（执行store、write操作）。




	2 先行发生原则 happens-before
		判断数据是有有竞争、线程是否安全的主要依据
		1. 程序次序规则：同一个线程内，按照代码出现的顺序，前面的代码先行于后面的代码，准确的说是控制流顺序，因为要考虑到分支和循环结构。

		2. 管程锁定规则：一个unlock操作先行发生于后面（时间上）对同一个锁的lock操作。

		3. volatile变量规则：对一个volatile变量的写操作先行发生于后面（时间上）对这个变量的读操作。

		4. 线程启动规则：Thread的start( )方法先行发生于这个线程的每一个操作。

		5. 线程终止规则：线程的所有操作都先行于此线程的终止检测。可以通过Thread.join( )方法结束、Thread.isAlive( )的返回值等手段检测线程的终止。 

		6. 线程中断规则：对线程interrupt( )方法的调用先行发生于被中断线程的代码检测到中断事件的发生，可以通过Thread.interrupt( )方法检测线程是否中断

		7. 对象终结规则：一个对象的初始化完成先行于发生它的finalize（）方法的开始。

		8. 传递性：如果操作A先行于操作B，操作B先行于操作C，那么操作A先行于操作C。

		为什么要有该原则？
			无论jvm或者cpu，都希望程序运行的更快。如果两个操作不在上面罗列出来的规则里面，那么久可以对他们进行任意的重排序。

			时间先后顺序与先行发生的顺序之间基本没有太大的关系。




	3 指令重排序
		什么是指令重排序？
			重排序是指编译器和处理器为了优化程序性能而对指令序列进行重新排序的一种手段。
		数据依赖性
			编译器和处理器在重排序时，会遵守数据依赖性，编译器和处理器不会改变存在数据依赖关系的两个操作的执行顺序。（仅针对单个处理器中执行的指令序列和单个线程中执行的操作，不同处理器之间和不同线程之间的数据依赖性不被编译器和处理器考虑。）

			两操作访问同一个变量，其两个操作中有至少一个写操作，此时就存在依赖性
			写后读 a=0 b=a
			读后写 a=b b=1
			写后写 a=1 a=2

			a=1,b=1
			写后读 a=0 b=a  正确b=0 错误b=1
		as-if-serial原则
			不管怎么重排序（编译器和处理器为了提高并行度），（单线程）程序的执行结果不能被改变。

		x=0，y=1
		x=1, y=0
		x=1, y=1
		x=0, y=0
