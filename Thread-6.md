#### 第六章--原子类

##### 1 什么是原子类
		一度认为原子是不可分割的最小单位，故原子类可以认为其操作都是不可分割

		为什么要有原子类？

			对多线程访问同一个变量，我们需要加锁，而锁是比较消耗性能的，JDk1.5之后，
			新增的原子操作类提供了一种用法简单、性能高效、线程安全地更新一个变量的方式，
			这些类同样位于JUC包下的atomic包下，发展到JDk1.8，该包下共有17个类，
			囊括了原子更新基本类型、原子更新数组、原子更新属性、原子更新引用

		1.8新增的原子类
			DoubleAccumulator、DoubleAdder、LongAccumulator、LongAdder、Striped64、

##### 2 原子更新基本类型
		发展至JDk1.8，基本类型原子类有以下几个：
			AtomicBoolean、AtomicInteger、AtomicLong、DoubleAccumulator、DoubleAdder、
			LongAccumulator、LongAdder
		大致可以归为3类
			AtomicBoolean、AtomicInteger、AtomicLong  元老级的原子更新，方法几乎一模一样
			DoubleAdder、LongAdder  对Double、Long的原子更新性能进行优化提升
			DoubleAccumulator、LongAccumulator  支持自定义运算

##### 3 原子更新数组类型
		AtomicIntegerArray、AtomicLongArray、AtomicReferenceArray


##### 4 原子地更新属性

		原子地更新某个类里的某个字段时，就需要使用原子更新字段类，Atomic包提供了以下4个类进行原子字段更新
		AtomicIntegerFieldUpdater、AtomicLongFieldUpdater、AtomicStampedReference、AtomicReferenceFieldUpdater

		使用上述类的时候，必须遵循以下原则
		字段必须是volatile类型的，在线程之间共享变量时保证立即可见
		字段的描述类型是与调用者与操作对象字段的关系一致。
		也就是说调用者能够直接操作对象字段，那么就可以反射进行原子操作。
		对于父类的字段，子类是不能直接操作的，尽管子类可以访问父类的字段。
		只能是实例变量，不能是类变量，也就是说不能加static关键字。
		只能是可修改变量，不能使final变量，因为final的语义就是不可修改。
		对于AtomicIntegerFieldUpdater和AtomicLongFieldUpdater只能修改int/long类型的字段，不能修改其包装类型（Integer/Long）。
		如果要修改包装类型就需要使用AtomicReferenceFieldUpdater。


##### 5 原子更新引用
		AtomicReference：用于对引用的原子更新
		AtomicMarkableReference：带版本戳的原子引用类型，版本戳为boolean类型。
		AtomicStampedReference：带版本戳的原子引用类型，版本戳为int类型。
