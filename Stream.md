
#### Stream:是处理数组和集合的一组API

#### 特性：
	- 不是数据结构，没有内部存储
	- 不支持索引访问
	- 延迟计算
	- 支持并行
	- 很容易生成数组或集合(List,Set)
	- 支持过滤，查找，转换，汇总，聚合等操作

#### 运行机制
	- Stream分为源source，中间操作，终止操作
	- 流的源可以是一个数组，一个集合，一个生成器方法，一个I/O操作等等。
	- 一个流可以有零个或者多个中间操作，每一个中间操作都会返回一个新的流，供下一个操作使用。
	- 一个流只会有一个终止操作。
	- Stream只有遇到终止操作，他的源才开始执行遍历操作。
	
#### 中间操作
	- 过滤 filter
	- 去重 distinct
	- 排序 sorted
	- 截取 limit、skip
	- 转换 map/flatMap
	- 其他 peek
	
#### 终止操作
	- 循环 forEach
	- 计算 min、max、count、average
	- 匹配 anyMatch、allMatch、noneMatch、findFirst、findAny
	- 汇聚 reduce
	- 收集器 toArray collect
	
#### Stream创建
	- 通过数组
	- 通过集合
	- 通过Stream.generate方法来创建
	- 通过Stream.iterate方法来创建
	- 其他API创建
