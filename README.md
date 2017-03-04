# apache commons-pool2 配置详解(基于github上最新分支)
* lifo
	* 所有的空闲对象存放在双向阻塞队列中, 当returnObject被调用时, 决定当前对象被放在队列的什么位置, true:放在队列头部, false:放在队列尾部(空闲对象检测线程在运行时也需要根据lifo来遍历队列, lifo:true从队列尾部向队列头部遍历, lifo:false从队列同步向队列尾部遍历, 判断空闲对象是否应该被销毁移除) 	
* maxIdle
	* 当前对象池中允许存在的最大空闲对象数, 当returnObject被调用时, 如果当前存在的空闲对象已经大于或者等于maxIdle, 那么当前返回的对象将不会被放到空闲队列中, 而是被直接销毁
* minIdle
	* 当前对象池中允许存在的最小空闲对象数, 主要用于对象移除, 详情见softMinEvictableIdleTimeMillis的解释
* maxTotal
	* 当前对象池允许获取的最大对象数 
* maxWaitMillis
	* 见blockWhenExhausted解释
* blockWhenExhausted
	* borrowObject调用时, 如果创建的对象总数已经超过了maxTotal, 那么不允许再继续创建对象, 而是从空闲队列中获取, 如果获取不到, 会决定是否再次阻塞获取, blockWhenExhausted:true阻塞获取, 阻塞的时间为maxWaitMillis, blockWhenExhausted:false则直接返回NoSuchElementException
* testOnCreate
	* 新创建的对象是否用PooledObjectFactory#validateObject验证对象的合法性, 如果验证失败, 则直接销毁当前创建的对象 
* testOnBorrow
	* borrowObject获取的对象(空闲对象或者新创建的对象)在返回前是否用PooledObjectFactory#validateObject验证对象的合法性, 如果验证失败, 则直接销毁当前创建的对象 
* testOnReturn
	* 对象在返回对象池的时候是否用PooledObjectFactory#validateObject验证对象的合法性, 如果验证失败, 则直接销毁当前创建的对象
* testWhileIdle
	* 在运行空闲对象检测线程(检测哪些空闲对象可以被销毁)的时候是否用PooledObjectFactory#validateObject对不符合销毁条件的对象再次验证其合法性(如果对象已经符合销毁条件, 那么将不再进行这步检查), 如果验证失败, 则直接销毁当前创建的对象
* numTestsPerEvictionRun
	* 空闲对象检测线程每次运行的时候检测的空闲对象数, 如果numTestsPerEvictionRun大于当时空闲对象数, 那么以当时的空闲对象数为准
* minEvictableIdleTimeMillis
	*  当空闲对象的空闲时间超过minEvictableIdleTimeMillis时, 那说明该空闲对象应该被销毁和移除
* timeBetweenEvictionRunsMillis
	*  空闲对象检测线程运行的时间频率
* softMinEvictableIdleTimeMillis
	* 作用和minEvictableIdleTimeMillis类似, 只不过还要再多一个条件, 那就是当前的空闲对象数大于minIdle的时候, 当前对象才会被销毁和移除
* evictionPolicyClassName
	* 对象是否应该销毁和移除的判读策略, 默认为DefaultEvictionPolicy, 上面 minEvictableIdleTimeMillis和softMinEvictableIdleTimeMillis的解释都是基于DefaultEvictionPolicy而言的