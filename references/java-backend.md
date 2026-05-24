# Java 后端面试八股文库

## 一、Java基础

### HashMap底层原理与扩容机制 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：HashMap基于数组+链表+红黑树（JDK8）实现，通过hash算法定位桶位置，当链表长度≥8且数组长度≥64时链表转红黑树，扩容时容量翻倍并重新分配元素位置。

**常见面试问法**：
- "请说说HashMap的底层原理？put过程是怎样的？"
- "HashMap什么时候扩容？扩容时元素怎么迁移？"

**回答框架**：
1. 先说数据结构：JDK7数组+链表，JDK8数组+链表+红黑树
2. 展开put流程：计算hash→定位桶→判断是否为空→插入/遍历链表/遍历红黑树→判断是否需要树化
3. 扩容机制：默认容量16、负载因子0.75、threshold=容量×负载因子，扩容为2倍，元素重新hash分配（高位为0原位置，高位为1原位置+oldCap）
4. 线程不安全场景：JDK7头插法死循环、JDK8数据覆盖问题

**延伸考点**：ConcurrentHashMap → Hashtable（已淘汰）

---

### ConcurrentHashMap演进与原理 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：JDK7采用Segment分段锁，每个Segment继承ReentrantLock；JDK8改为CAS+synchronized，锁粒度细化到桶级别，同样引入红黑树优化查询。

**常见面试问法**：
- "ConcurrentHashMap在JDK7和JDK8中的实现有什么区别？"
- "ConcurrentHashMap是怎么保证线程安全的？"

**回答框架**：
1. JDK7实现：Segment数组+HashEntry，Segment继承ReentrantLock，默认16个Segment，并发度16
2. JDK8实现：Node数组+链表+红黑树，CAS写入空桶，synchronized锁定非空桶头节点
3. 对比：JDK8锁粒度更细、查询效率更高（红黑树）、Segment锁改为synchronized（JVM层面优化）
4. 关键操作：put时tabAt/casTabAt保证可见性和原子性，size使用baseCount+CounterCell累加

**延伸考点**：HashMap线程不安全 → CAS原理 → synchronized锁升级

---

### ArrayList vs LinkedList [频率: ⭐⭐⭐⭐]

**核心要点**：ArrayList基于动态数组，随机访问O(1)、尾部插入O(1)均摊、中间插入删除O(n)；LinkedList基于双向链表，随机访问O(n)、头尾操作O(1)、中间插入删除需先遍历定位。

**常见面试问法**：
- "ArrayList和LinkedList的区别？各自适用场景？"
- "ArrayList的扩容机制是怎样的？"

**回答框架**：
1. 底层数据结构：ArrayList是Object[]数组，LinkedList是双向链表
2. 性能对比：随机访问ArrayList远优，头插LinkedList远优，尾插ArrayList均摊O(1)更优
3. ArrayList扩容：默认容量10，扩容为1.5倍（oldCapacity + oldCapacity >> 1），Arrays.copyOf迁移
4. 内存占用：LinkedList每个节点额外存储prev/next两个指针，内存开销更大
5. 选用建议：绝大多数场景选ArrayList，频繁头插或不需要随机访问时考虑LinkedList

**延伸考点**：HashMap → CopyOnWriteArrayList

---

### synchronized锁升级机制 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：synchronized锁状态存储在对象头Mark Word中，经历无锁→偏向锁→轻量级锁（自旋）→重量级锁的升级过程，升级不可逆（GC可降级），偏向锁在JDK15后默认关闭。

**常见面试问法**：
- "synchronized的锁升级过程是怎样的？"
- "偏向锁和轻量级锁有什么区别？什么时候会升级？"

**回答框架**：
1. 对象头Mark Word存储锁状态：无锁(01)、偏向锁(01)、轻量级锁(00)、重量级锁(10)、GC标记(11)
2. 偏向锁：第一个线程访问时CAS将线程ID写入Mark Word，后续同线程无需同步；有竞争时撤销偏向锁（全局安全点STW）
3. 轻量级锁：撤销偏向后，线程栈帧中创建Lock Record，CAS尝试将Mark Word指向Lock Record，失败则自旋
4. 重量级锁：自旋超过阈值（自适应自旋），膨胀为重量级锁，线程阻塞进入等待队列（由操作系统mutex实现）
5. 锁升级触发条件：单线程重复进入→偏向锁，两个线程交替进入→轻量级锁，竞争激烈→重量级锁

**延伸考点**：volatile → AQS → Lock与synchronized对比

---

### volatile三特性 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：volatile保证可见性（修改后立即刷回主内存，其他线程读取时从主内存加载）、禁止指令重排序（内存屏障）、但不保证原子性（如i++操作仍不安全）。

**常见面试问法**：
- "volatile能保证线程安全吗？它有哪些特性？"
- "volatile是怎么实现可见性和禁止指令重排的？"

**回答框架**：
1. 可见性：基于JMM主内存-工作内存模型，写操作后插入StoreLoad屏障，读操作前插入LoadLoad屏障，触发缓存一致性协议（MESI）使其他CPU缓存行失效
2. 禁止指令重排：编译器和处理器层面的内存屏障——写前插入StoreStore、写后插入StoreLoad、读前插入LoadLoad、读后插入LoadStore
3. 不保证原子性：举例i++实际是read→load→use→assign→store→write六步操作，多线程下仍会丢失更新
4. 典型应用：DCL单例模式中防止对象初始化半初始化状态被其他线程看到、状态标志位
5. 解决原子性方案：AtomicInteger（CAS）或synchronized/Lock

**延伸考点**：JMM内存模型 → CAS → synchronized锁升级

---

### AQS原理 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：AQS（AbstractQueuedSynchronizer）是并发锁的底层框架，核心是volatile int state状态位和CLH变体双向等待队列，提供独占/共享两种获取模式，子类重写tryAcquire/tryRelease实现不同语义。

**常见面试问法**：
- "请说说AQS的原理？ReentrantLock是怎么基于AQS实现的？"
- "AQS的公平锁和非公平锁有什么区别？"

**回答框架**：
1. 核心数据结构：volatile int state（0未占用/1占用/可重入累加）+ CLH双向FIFO队列（Node节点存线程引用和等待状态）
2. 独占模式获取：tryAcquire尝试获取→失败则addWaiter入队→acquireQueued自旋/阻塞→前驱节点释放时unparkSuccessor唤醒
3. 共享模式获取：tryAcquireShared→成功传播唤醒后续共享节点
4. ReentrantLock实现：公平锁tryAcquire检查队列是否有前驱等待；非公平锁直接CAS抢锁，抢不到再入队
5. 可中断/超时：lockInterruptibly和tryLock支持响应中断和超时返回

**延伸考点**：synchronized锁升级 → CountDownLatch/Semaphore原理 → ReentrantReadWriteLock

---

### 线程池7大参数与执行流程 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：ThreadPoolExecutor核心7参数为corePoolSize、maximumPoolSize、keepAliveTime、unit、workQueue、threadFactory、handler；任务提交遵循核心线程→队列→非核心线程→拒绝策略的执行顺序。

**常见面试问法**：
- "线程池的7个参数分别是什么？执行流程是怎样的？"
- "线程池的拒绝策略有哪些？怎么选择？"

**回答框架**：
1. 7大参数：corePoolSize核心线程数、maximumPoolSize最大线程数、keepAliveTime非核心线程空闲存活时间、unit时间单位、workQueue任务队列、threadFactory线程工厂、handler拒绝策略
2. 执行流程：提交任务→核心线程未满则创建核心线程执行→核心线程满则入队列→队列满则创建非核心线程→非核心线程满则执行拒绝策略
3. 4种拒绝策略：AbortPolicy抛异常（默认）、CallerRunsPolicy调用者线程执行、DiscardPolicy丢弃、DiscardOldestPolicy丢弃队列最老任务
4. 常见队列选择：无界队列LinkedBlockingQueue（FixedThreadPool）、同步队列SynchronousQueue（CachedThreadPool）、有界队列ArrayBlockingQueue（推荐）
5. 最佳实践：线程数配置CPU密集型=CPU核数+1、IO密集型=CPU核数×2或CPU核数/(1-阻塞系数)，必须使用有界队列

**延伸考点**：ThreadLocal → Future/CompletableFuture → ForkJoinPool

---

### ThreadLocal内存泄漏 [频率: ⭐⭐⭐⭐]

**核心要点**：ThreadLocalMap的Key是ThreadLocal的弱引用，GC时Key被回收变为null，但Value是强引用无法回收，线程复用时Value对象无法释放导致内存泄漏；应在使用后调用remove()清理。

**常见面试问法**：
- "ThreadLocal为什么会内存泄漏？怎么解决？"
- "ThreadLocal的原理是什么？ThreadLocalMap的结构？"

**回答框架**：
1. 基本原理：每个Thread持有ThreadLocalMap，Key为ThreadLocal实例弱引用，Value为存储值强引用
2. 泄漏原因：ThreadLocal外部引用置空→GC回收Key→Entry.key=null但Value仍被强引用→线程复用（线程池）时Value永远无法回收
3. 设计上的缓解：ThreadLocalMap的get/set/remove方法会清理key==null的Entry（expungeStaleEntry），但依赖主动调用不可靠
4. 解决方案：务必在finally块中调用threadLocal.remove()，尤其在线程池场景
5. 扩展：InheritableThreadLocal可继承父线程值但有线程池问题→TransmittableThreadLocal（阿里开源）解决线程池传递

**延伸考点**：线程池 → 强软弱虚引用 → 内存泄漏排查

---

### CAS与ABA问题 [频率: ⭐⭐⭐⭐]

**核心要点**：CAS（Compare And Swap）是无锁并发的核心原语，比较当前值与期望值是否相等，相等则原子更新为新值；ABA问题是值从A变B再变回A，CAS无法感知中间变化，解决方案是AtomicStampedReference加版本号。

**常见面试问法**：
- "什么是CAS？有什么问题？怎么解决ABA问题？"
- "Unsafe类是做什么的？为什么CAS效率高？"

**回答框架**：
1. CAS原理：CPU指令级别原子操作（x86 cmpxchg），Java通过Unsafe类调用native方法实现
2. 三大问题：ABA问题（加版本号AtomicStampedReference）、自旋开销（长时间CAS失败CPU空转→自适应策略）、只能保证单变量原子性（AtomicReference封装多变量）
3. ABA解决：AtomicStampedReference每次更新stamp+1，比较时同时比较值和版本号；AtomicMarkableReference只关心是否被修改过
4. 应用场景：AtomicInteger/AtomicLong计数、自旋锁、ConcurrentHashMap的tabAt/casTabAt
5. 与synchronized对比：CAS乐观锁适合低冲突场景，synchronized悲观锁适合高冲突场景

**延伸考点**：volatile → AQS → Atomic原子类全家桶

---

### JVM内存模型5大区域 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：JVM内存分为堆（对象实例、GC主要区域）、方法区/元空间（类信息、常量池）、虚拟机栈（栈帧、局部变量表）、本地方法栈（Native方法）、程序计数器（当前执行行号），其中堆和方法区线程共享，其余线程私有。

**常见面试问法**：
- "请说说JVM的内存模型？各区域存什么？"
- "堆和栈有什么区别？哪些区域会OOM？"

**回答框架**：
1. 程序计数器：当前线程执行的字节码行号，唯一不会OOM的区域
2. 虚拟机栈：每个方法创建栈帧（局部变量表、操作数栈、动态链接、方法返回地址），StackOverflowError（递归过深）或OOM
3. 本地方法栈：为Native方法服务，HotSpot与虚拟机栈合二为一
4. 堆：所有对象实例和数组分配区域，GC分代（新生代Eden+S0+S1、老年代），-Xms/-Xmx配置
5. 方法区/元空间：JDK7永久代（-XX:MaxPermSize）→JDK8元空间（-XX:MaxMetaspaceSize，使用本地内存），存放类元信息、运行时常量池（JDK7后字符串常量池移至堆）

**延伸考点**：垃圾回收算法 → OOM排查 → 类加载机制

---

### 垃圾回收算法与收集器 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：基础算法有标记-清除（碎片）、标记-整理（移动开销）、复制算法（空间浪费）；现代收集器CMS（低延迟、标记-清除有碎片）、G1（Region分代、可预测停顿）、ZGC（着色指针、读屏障、亚毫秒停顿）各有适用场景。

**常见面试问法**：
- "CMS和G1收集器有什么区别？G1是怎么做到可预测停顿的？"
- "ZGC为什么能做到这么低的停顿？"

**回答框架**：
1. 三大基础算法：标记-清除（产生碎片）、复制算法（新生代Eden+S0+S1，空间换时间）、标记-整理（老年代，移动对象消除碎片）
2. CMS收集器：初始标记(STW)→并发标记→重新标记(STW)→并发清除；缺点是碎片问题（可配CMSFullGC压缩）、浮动垃圾、并发阶段占CPU
3. G1收集器：将堆划分为等大Region（Eden/Survivor/Old/Humongous），维护优先级回收价值最大的Region（Mixed GC），-XX:MaxGCPauseMillis设定目标停顿时间
4. ZGC收集器：着色指针（Color Pointers存标记信息）+读屏障（Load Barrier处理并发引用变更），JDK15前仅Linux，JDK16+全平台，停顿时间<1ms且不随堆增大而增加
5. 选择建议：JDK8默认Parallel+CMS，JDK9+默认G1，大堆/低延迟需求选ZGC

**延伸考点**：JVM内存模型 → GC调优参数 → OOM排查

---

### 类加载与双亲委派 [频率: ⭐⭐⭐⭐]

**核心要点**：类加载过程为加载→验证→准备→解析→初始化；双亲委派指子加载器先委托父加载器加载，避免核心类被篡改；打破双亲委派的场景有SPI机制（线程上下文类加载器）、Tomcat（WebAppClassLoader隔离）、OSGi（模块化网状加载）。

**常见面试问法**：
- "什么是双亲委派？为什么要打破？怎么打破？"
- "类加载的过程是怎样的？"

**回答框架**：
1. 类加载5个阶段：加载（二进制字节流→Class对象）→验证（格式/元数据/字节码/符号引用）→准备（静态变量赋零值，final赋真实值）→解析（符号引用→直接引用）→初始化（clinit执行赋值和static块）
2. 双亲委派：Bootstrap→Extension→Application，自底向上检查是否已加载，自顶向下尝试加载
3. 打破原因：核心类需要调用外部实现（SPI）、应用隔离（Tomcat多应用）、模块化热部署
4. 打破方式：自定义ClassLoader重写loadClass（而非findClass）、线程上下文类加载器（JDBC的DriverManager用Bootstrap加载，通过Thread.currentThread().getContextClassLoader()获取AppClassLoader加载驱动）
5. 注意：JDK9模块化系统对类加载机制做了调整，平台类加载器替代扩展类加载器

**延伸考点**：JVM内存模型（方法区存类信息）→ Spring IOC容器初始化 → 热部署原理

---

### OOM排查 [频率: ⭐⭐⭐⭐]

**核心要点**：OOM常见类型有堆溢出（java.lang.OutOfMemoryError: Java heap space）、元空间溢出（Metaspace）、GC开销超限（GC overhead limit exceeded）；排查核心是导出堆dump（-XX:+HeapDumpOnOutOfMemoryError）用MAT/VisualVM分析大对象和引用链。

**常见面试问法**：
- "线上OOM了怎么排查？"
- "怎么定位内存泄漏？"

**回答框架**：
1. 保留现场：-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/dump.hprof 自动dump
2. 紧急处理：重启服务恢复，调大堆内存临时缓解，保留日志和dump文件
3. 分析dump：MAT打开→Dominator Tree查看大对象→Leak Suspects自动分析→GC Roots引用链定位泄漏源
4. 常见原因：ThreadLocal未remove、静态集合持续增长、缓存无淘汰策略、大查询结果集未释放、元空间类加载器泄漏
5. 在线排查：jmap -histo:live <pid> 查看存活对象统计、jstat -gcutil 监控GC情况、Arthas dashboard实时监控

**延伸考点**：JVM内存模型 → 垃圾回收 → 线上问题排查工具链

---

### Java8 Stream与Lambda [频率: ⭐⭐⭐⭐]

**核心要点**：Lambda是函数式编程的语法糖，实现函数式接口（仅一个抽象方法）的匿名内部类；Stream是数据管道操作，支持filter/map/reduce/collect等链式操作，分为中间操作（惰性）和终端操作（触发执行），支持并行流parallelStream。

**常见面试问法**：
- "Stream和for循环有什么区别？Stream的性能如何？"
- "Lambda表达式的原理是什么？"

**回答框架**：
1. Lambda原理：编译时生成私有静态方法+invokedynamic指令+运行时LambdaMetafactory生成实现类，无状态Lambda捕获this时不会创建额外对象
2. Stream特性：中间操作惰性求值（filter/map/sorted）、终端操作触发执行（collect/forEach/reduce）、短路操作（findAny/anyMatch）
3. 并行流：parallelStream基于ForkJoinPool.commonPool，适合CPU密集型大数据量场景，注意线程安全和顺序问题
4. 性能对比：简单遍历for > Stream；复杂操作（过滤+映射+分组）Stream更优雅且并行流可提升性能；Stream有装箱开销，优先使用IntStream等原始类型流
5. 注意事项：Stream只能消费一次、操作顺序影响性能（filter在前减少后续数据量）、避免在forEach中修改外部状态

**延伸考点**：函数式接口 → Optional → 反射与MethodHandle

---

### Java新特性（Java11/17/21） [频率: ⭐⭐⭐]

**核心要点**：Java11新增var局部变量类型推断、HTTP Client标准化、单文件源码执行；Java17为LTS版本，新增sealed classes、pattern matching for switch（预览）、record类正式版；Java21为LTS版本，虚拟线程（Virtual Threads）正式发布，是轻量级线程，由JVM调度而非OS。

**常见面试问法**：
- "虚拟线程和平台线程有什么区别？适用什么场景？"
- "Java17和Java21有哪些重要新特性？"

**回答框架**：
1. Java11：var局部变量推断（编译期推断不可用于方法参数/返回值）、HTTP Client（支持HTTP/2和WebSocket）、String新方法（isBlank/lines/strip）、单文件执行java Hello.java
2. Java17 LTS：sealed classes限制继承（permits子类）、record不可变数据载体（自动生成equals/hashCode/toString）、pattern matching for instanceof正式版
3. Java21 LTS虚拟线程：Thread.ofVirtual()创建，百万级轻量线程，由JVM调度挂载到平台线程（ForkJoinPool），IO阻塞时自动卸载不占OS线程；适合IO密集型高并发场景，不适合CPU密集型或使用synchronized长期持有锁的场景（pinning问题，建议用ReentrantLock）
4. 虚拟线程原理：Continuation实现协程语义，IO操作时自动yield释放载体线程，IO完成时恢复执行
5. 升级建议：Java8→11门槛低（移除JavaEE模块需调整），11→17需注意密封类和强封装JDK内部API，21虚拟线程是最大卖点

**延伸考点**：线程池 → 协程（Kotlin/Golang对比） → 响应式编程

---

## 二、框架

### Spring IoC容器初始化流程 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：IoC容器初始化核心流程为资源定位→BeanDefinition加载解析→BeanDefinition注册到BeanFactory→实例化和依赖注入；ApplicationContext在refresh()中完成全部初始化，包括BeanFactory创建、BeanPostProcessor注册、单例Bean实例化等12个步骤。

**常见面试问法**：
- "Spring IoC的初始化流程是怎样的？refresh()做了什么？"
- "BeanFactory和ApplicationContext有什么区别？"

**回答框架**：
1. BeanFactory vs ApplicationContext：BeanFactory懒加载，ApplicationContext预加载且支持事件、国际化、AOP自动注册
2. refresh()12步核心：prepareRefresh→obtainFreshBeanFactory（创建BeanFactory、加载BeanDefinition）→prepareBeanFactory→postProcessBeanFactory→invokeBeanFactoryPostProcessors（处理@Configuration/@ComponentScan等）→registerBeanPostProcessors→initMessageSource→initApplicationEventMulticaster→onRefresh→registerListeners→finishBeanFactoryInitialization（实例化所有非懒加载单例）→finishRefresh
3. BeanDefinition来源：XML/注解扫描/@Import/@Bean，最终统一为BeanDefinition对象
4. BeanPostProcessor扩展点：初始化前postProcessBeforeInitialization（@PostConstruct）、初始化后postProcessAfterInitialization（AOP代理）

**延伸考点**：Bean生命周期 → 循环依赖 → AOP代理时机

---

### Spring Bean生命周期 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：Bean生命周期为实例化→属性赋值→初始化→销毁四个阶段，其中初始化阶段穿插多个Aware接口回调和BeanPostProcessor扩展点，AOP代理在初始化后由AbstractAutoProxyCreator创建。

**常见面试问法**：
- "请详细说说Spring Bean的完整生命周期？"
- "BeanPostProcessor和BeanFactoryPostProcessor有什么区别？"

**回答框架**：
1. 实例化：Constructor或工厂方法创建对象
2. 属性赋值：populateBean注入依赖（@Autowired/@Value）
3. Aware回调：BeanNameAware→BeanFactoryAware→ApplicationContextAware
4. 初始化前：BeanPostProcessor.postProcessBeforeInitialization（CommonAnnotationBeanPostProcessor处理@PostConstruct）
5. 初始化：InitializingBean.afterPropertiesSet→自定义init-method
6. 初始化后：BeanPostProcessor.postProcessAfterInitialization（AbstractAutoProxyCreator创建AOP代理）
7. 销毁：@PreDestroy→DisposableBean.destroy→自定义destroy-method
8. BeanFactoryPostProcessor在Bean实例化之前修改BeanDefinition（如PropertyPlaceholderConfigurer），BeanPostProcessor在Bean初始化前后增强Bean实例

**延伸考点**：IoC初始化流程 → 循环依赖 → AOP代理创建时机

---

### Spring循环依赖解决 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：Spring通过三级缓存解决单例Bean的setter注入循环依赖：singletonObjects（成品）、earlySingletonObjects（半成品）、singletonFactories（对象工厂）；构造器注入无法解决，AOP代理通过singletonFactory提前暴露代理对象保证代理一致性。

**常见面试问法**：
- "Spring是怎么解决循环依赖的？三级缓存各自的作用？"
- "构造器注入的循环依赖能解决吗？为什么？"

**回答框架**：
1. 三级缓存：一级singletonObjects（完全初始化的Bean）、二级earlySingletonObjects（提前暴露的早期引用）、三级singletonFactories（ObjectFactory，调用时可能返回代理对象）
2. 解决流程：A实例化→将A的ObjectFactory放入三级缓存→A属性注入发现依赖B→B实例化→B属性注入发现依赖A→从三级缓存获取A的ObjectFactory.getObject()→A的早期引用放入二级缓存→B初始化完成放入一级缓存→A继续初始化完成放入一级缓存
3. 为什么需要三级而非二级：AOP场景下，如果只有二级缓存，每个Bean都需要提前创建代理；三级缓存的ObjectFactory延迟到被依赖时才决定是否创建代理，保证代理只创建一次
4. 无法解决的场景：构造器注入循环依赖（实例都创建不出来）、原型Bean循环依赖、@Async标注的Bean（代理创建时机不同）
5. 解决构造器循环依赖：@Lazy延迟注入、改用setter注入、重构设计消除循环

**延伸考点**：Bean生命周期 → AOP代理 → @Lazy原理

---

### Spring AOP动态代理 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：Spring AOP基于动态代理实现，JDK动态代理基于接口（Proxy.newProxyInstance），CGLIB基于子类继承（Enhancer生成子类字节码）；Spring默认策略是目标类实现接口用JDK代理否则用CGLIB，SpringBoot2.x默认全部使用CGLIB。

**常见面试问法**：
- "JDK动态代理和CGLIB有什么区别？各有什么限制？"
- "Spring AOP和AspectJ有什么区别？"

**回答框架**：
1. JDK动态代理：基于java.lang.reflect.Proxy，实现InvocationHandler接口，只能代理接口方法，目标类必须实现接口；生成代理类实现相同接口，方法调用转发到InvocationHandler.invoke()
2. CGLIB代理：基于ASM字节码生成，创建目标类子类，final类/方法无法代理；MethodInterceptor.intercept()拦截调用；性能略低于JDK代理（JDK8+优化后差距缩小）
3. Spring选择策略：ProxyFactoryBean根据targetClass是否实现接口判断；SpringBoot2.x默认CGLIB（spring.aop.proxy-target-class=true）
4. 代理失效场景：类内部方法调用（this.method()不走代理）、private/final/static方法、非Spring管理对象
5. AspectJ：编译期/加载期织入，功能更强（字段拦截、构造器拦截），但需要ajc编译器或agent

**延伸考点**：Bean生命周期（代理创建时机） → 循环依赖（代理提前暴露） → 事务失效场景

---

### Spring事务传播机制与失效场景 [频率: ⭐⭐⭐⭐]

**核心要点**：Spring事务通过AOP代理实现，7种传播行为中最常用REQUIRED（默认，加入当前事务）和REQUIRES_NEW（新建事务挂起当前）；事务失效常见于自调用（绕过代理）、非public方法、异常被吞、异步线程等场景。

**常见面试问法**：
- "Spring事务的传播机制有哪些？REQUIRES_NEW是什么意思？"
- "Spring事务在什么情况下会失效？"

**回答框架**：
1. 7种传播行为：REQUIRED（默认，有事务加入无事务新建）、SUPPORTS、MANDATORY、REQUIRES_NEW（始终新建，挂起外层）、NOT_SUPPORTED、NEVER、NESTED（嵌套事务，savepoint回滚到保存点）
2. 实现原理：TransactionInterceptor→PlatformTransactionManager→DataSource层面connection.setAutoCommit(false)
3. 失效场景：同类方法自调用（this.方法()不走代理）、方法非public（AOP限制）、异常被catch未抛出、rollbackFor未配置（默认只回滚RuntimeException和Error）、异步线程（不同SqlSession/Connection）、数据库引擎不支持事务（MyISAM）
4. 解决自调用：注入自身（@Lazy避免循环依赖）、AopContext.currentProxy()（需开启exposeProxy=true）、拆分到不同Service

**延伸考点**：AOP代理 → 分布式事务 → MySQL事务隔离级别

---

### SpringBoot自动配置原理 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：SpringBoot自动配置的核心是@SpringBootApplication注解中的@EnableAutoConfiguration，通过SpringFactoriesLoader加载META-INF/spring.factories（2.7+改为META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports）中的配置类，配合@Conditional系列条件注解按需装配。

**常见面试问法**：
- "SpringBoot自动配置的原理是什么？"
- "如何自定义一个starter？"

**回答框架**：
1. 入口：@SpringBootApplication = @SpringBootConfiguration + @EnableAutoConfiguration + @ComponentScan
2. @EnableAutoConfiguration：@Import(AutoConfigurationImportSelector.class)→SpringFactoriesLoader加载自动配置类→去重→排除（exclude/excludeName）→过滤（@Conditional条件）→生效
3. @Conditional条件注解：@ConditionalOnClass（类路径存在）、@ConditionalOnMissingBean（容器中无该Bean）、@ConditionalOnProperty（配置属性匹配）等
4. 自定义starter：创建xxx-spring-boot-starter（依赖管理）+xxx-spring-boot-autoconfigure（配置类+Properties+Bean定义）→META-INF/spring.factories注册AutoConfiguration类→spring-configuration-metadata.json生成配置提示
5. 配置加载优先级：命令行参数 > 系统属性 > application-{profile}.yml > application.yml

**延伸考点**：Spring IoC → @Conditional扩展 → 外部化配置

---

### SpringBoot Starter机制 [频率: ⭐⭐⭐⭐]

**核心要点**：Starter是一组依赖和自动配置的封装，遵循命名规范spring-boot-starter-xxx（官方）和xxx-spring-boot-starter（第三方），通过引入starter即可零配置使用对应功能。

**常见面试问法**：
- "SpringBoot的starter原理是什么？怎么自定义starter？"
- "starter和自动配置是什么关系？"

**回答框架**：
1. Starter本质：pom依赖聚合+自动配置类的打包分发，降低引入成本
2. 官方命名：spring-boot-starter-web、spring-boot-starter-data-redis；第三方命名：mybatis-spring-boot-starter
3. 自定义步骤：①创建autoconfigure模块（@Configuration+@ConditionalOnClass+@EnableConfigurationProperties+Bean定义）②创建starter模块（仅pom依赖autoconfigure）③META-INF/spring.factories注册④编写ConfigurationProperties绑定配置项
4. 关键注解：@ConfigurationProperties(prefix="xxx")绑定yml配置、@EnableConfigurationProperties启用配置绑定
5. 最佳实践：提供默认值（@ConditionalOnMissingBean允许用户覆盖）、文档化配置项（spring-configuration-metadata.json）

**延伸考点**：自动配置原理 → @Conditional条件装配 → 外部化配置

---

### MyBatis核心原理 [频率: ⭐⭐⭐⭐]

**核心要点**：MyBatis核心是SqlSession→Executor→StatementHandler→ParameterHandler→ResultSetHandler的执行链路；#{}预编译防SQL注入、${}字符串替换有注入风险；一级缓存SqlSession级别默认开启，二级缓存Mapper级别需手动开启且要求实体序列化。

**常见面试问法**：
- "#{}和${}有什么区别？为什么#{}能防SQL注入？"
- "MyBatis的一级缓存和二级缓存有什么区别？有什么问题？"

**回答框架**：
1. #{} vs ${}：#{}使用PreparedStatement占位符?，参数预编译设置，防SQL注入；${}直接字符串拼接，存在注入风险，仅用于动态表名/列名等不能占位符的场景
2. 一级缓存：SqlSession级别，默认开启，同一SqlSession相同查询走缓存；增删改操作清空该SqlSession缓存；Spring整合时每次请求新建SqlSession所以一级缓存基本失效
3. 二级缓存：Mapper namespace级别，需在XML中加<cache/>，实体类实现Serializable；事务提交后才写入二级缓存；多表关联查询存在脏读问题（表A的缓存不会因关联表B的更新而失效）
4. 执行流程：SqlSession.getMapper→MapperProxy动态代理→MapperMethod.execute→SqlSession.selectList→Executor.query（缓存链路：二级缓存→一级缓存→数据库）→SimpleExecutor.doQuery→StatementHandler→ParameterHandler设置参数→ResultSetHandler映射结果
5. 插件机制：Interceptor基于JDK动态代理，可拦截Executor/StatementHandler/ParameterHandler/ResultSetHandler四大对象，PageHelper、MyBatis-Plus均基于此

**延伸考点**：Spring事务（SqlSession管理） → 分页插件原理 → MyBatis-Plus

---

## 三、中间件

### Redis数据类型与底层结构 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：Redis有5种基本数据类型（String/Hash/List/Set/ZSet）和3种扩展类型（HyperLogLog/Bitmap/GeoStream），底层编码根据数据量和大小自动转换以节省内存，如String在整数值时用int编码、短字符串用embstr、长字符串用raw。

**常见面试问法**：
- "Redis有哪些数据类型？底层是怎么实现的？"
- "ZSet的底层数据结构是什么？跳跃表是怎么工作的？"

**回答框架**：
1. String：int（整数≤long范围）→embstr（≤44字节）→raw（SDS）；应用：缓存、计数器、分布式锁
2. Hash：ziplist（元素数≤128且值≤64字节）→hashtable；应用：对象存储
3. List：quicklist（ziplist+双向链表）；应用：消息队列、最新列表
4. Set：intset（全是整数且元素数≤512）→hashtable；应用：标签、共同关注
5. ZSet：ziplist（元素数≤128且值≤64字节）→skiplist+hashtable（hashtable存分值O(1)查询，skiplist支持范围查询O(logN)）；应用：排行榜、延迟队列
6. 跳跃表：多层索引的有序链表，随机层数（概率1/4递增），查询/插入O(logN)，比平衡树实现简单且范围查询更友好

**延伸考点**：Redis持久化 → 缓存穿透击穿雪崩 → 分布式锁

---

### Redis持久化RDB与AOF [频率: ⭐⭐⭐⭐⭐]

**核心要点**：RDB是某一时刻的全量快照，fork子进程写磁盘，恢复快但可能丢失两次快照间数据；AOF记录每次写命令，实时性高但文件大恢复慢；Redis4.0+混合持久化结合两者优势，AOF重写时前半段RDB格式后半段AOF增量命令。

**常见面试问法**：
- "RDB和AOF有什么区别？怎么选择？"
- "AOF重写是什么？混合持久化是什么？"

**回答框架**：
1. RDB：bgsave fork子进程→COW（Copy-On-Write）机制，子进程写临时文件→替换旧RDB文件；save命令阻塞主线程不推荐；配置save 900 1等触发条件
2. AOF：每次写命令追加到aof_buf→同步策略always（每条fsync，最安全最慢）、everysec（每秒fsync，推荐）、no（OS决定）；AOF文件可读性好但体积大
3. AOF重写：bgrewriteaof fork子进程，基于当前内存状态生成最简命令集（如多次INCR合并为SET），期间新命令同时写入旧AOF和重写缓冲区，重写完成后追加新命令替换旧文件
4. 混合持久化：aof-use-rdb-preamble yes，AOF重写时前半段RDB二进制格式（快+紧凑）+后半段AOF增量命令（实时），兼顾恢复速度和数据安全
5. 选择建议：对数据安全要求高用AOF+everysec；可接受分钟级丢失用RDB；生产推荐混合持久化

**延伸考点**：Redis集群 → 主从复制 → 数据恢复流程

---

### Redis集群模式 [频率: ⭐⭐⭐⭐]

**核心要点**：Redis集群方案有主从复制（读写分离）、Sentinel哨兵（自动故障转移）、Cluster集群（数据分片）；Cluster采用16384个哈希槽分配到不同节点，客户端通过CRC16(key)%16384定位节点，支持在线扩缩容。

**常见面试问法**：
- "Redis集群方案有哪些？各有什么优缺点？"
- "Redis Cluster的哈希槽是怎么分配的？为什么是16384个？"

**回答框架**：
1. 主从复制：全量同步（从库首次连接/断开过久→PSYNC?→主库bgsave发送RDB+缓冲区增量）+增量同步（repl_backlog环形缓冲区，offset差距过大退化为全量）
2. Sentinel哨兵：监控（定期ping）、故障判定（主观下线sdown→客观下线odown，需quorum个哨兵同意）、自动转移（选举新主库→从库复制→通知客户端）；哨兵自身通过Raft选举leader执行转移
3. Cluster集群：无中心架构，16384槽位分配到节点，每个节点负责一部分槽；客户端缓存槽位映射表（MOVED重定向更新）；节点间Gossip协议通信
4. 16384原因：槽位信息通过心跳包传播，16384个槽只需2KB（8192需1KB但不够分，65536需8KB网络开销大）；集群节点数建议≤1000
5. 限制：不支持多key跨槽操作（可用Hash Tag {tag}保证同槽）、不支持SELECT切换库（只有db0）、从库默认不处理请求

**延伸考点**：Redis数据类型 → 分布式锁 → 缓存一致性

---

### Redis分布式锁 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：Redis分布式锁基于SET key value NX EX原子命令实现，value使用唯一标识防止误删；锁续期通过看门狗机制（Redisson）；RedLock算法解决单点故障但存在争议，生产环境更推荐Redisson+业务层幂等。

**常见面试问法**：
- "Redis分布式锁怎么实现？有什么问题？"
- "Redisson的看门狗机制是什么？RedLock了解吗？"

**回答框架**：
1. 基本实现：SET lock unique_value NX EX 30（原子操作，NX不存在才设置，EX过期时间）；释放锁用Lua脚本（GET判断value==unique_value→DEL，保证原子性）
2. 过期时间问题：太短业务未完成锁就释放→看门狗续期；太长宕机后锁长时间不释放→合理设置+看门狗
3. Redisson看门狗：默认锁30s，每10s（1/3租约时间）检查锁是否还被持有，持有则续期到30s；主动释放或客户端宕机则停止续期
4. RedLock：向N个独立Redis实例加锁，超过半数(N/2+1)加锁成功且总耗时未超锁过期时间才算成功；争议点在于时钟漂移和GC暂停可能导致不安全
5. 最佳实践：锁value用UUID防误删、Lua脚本保证原子性、合理过期时间+看门狗、业务层幂等兜底、考虑Zookeeper/etcd实现强一致性锁

**延伸考点**：Redis集群 → ZooKeeper → 分布式事务

---

### 缓存穿透、击穿、雪崩 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：穿透是查询不存在的数据绕过缓存直达数据库（布隆过滤器/空值缓存）；击穿是热点key过期瞬间大量请求打到数据库（互斥锁/永不过期+异步刷新）；雪崩是大量key同时过期或Redis宕机（过期时间加随机值/高可用架构/限流降级）。

**常见面试问法**：
- "缓存穿透、击穿、雪崩分别是什么？怎么解决？"
- "布隆过滤器的原理是什么？有什么问题？"

**回答框架**：
1. 穿透：查询数据库和缓存都不存在的数据→每次都穿透到数据库。解决：①布隆过滤器（位数组+多次hash，判断不存在则一定不存在，判断存在可能误判）②空值缓存设短过期时间③接口参数校验④用户鉴权限流
2. 击穿：热点key过期→大量并发请求同时打到数据库。解决：①互斥锁（SETNX抢锁重建缓存，未抢到线程sleep重试）②逻辑过期（缓存永不过期，值中存逻辑过期时间，过期后异步线程更新，其他线程返回旧数据）③热点key预热
3. 雪崩：大量key同一时刻过期或Redis宕机→请求全打到数据库。解决：①过期时间加随机值打散②Redis Sentinel/Cluster高可用③多级缓存（本地Caffeine+Redis）④限流降级熔断⑤持久化+快速恢复
4. 布隆过滤器：位数组+多个hash函数，添加时hash对应位置1，查询时所有位都为1则可能存在；不支持删除（Counting Bloom Filter可删除）、有误判率、空间效率高
5. 三者区别总结：穿透是数据不存在、击穿是单点热点过期、雪崩是大面积失效

**延伸考点**：Redis数据类型 → 限流降级 → 多级缓存架构

---

### Kafka架构原理 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：Kafka是分布式流处理平台，核心架构为Producer→Broker（Topic→Partition→Replica）→Consumer Group；通过分区实现并行度、副本实现高可用、Consumer Group实现消费负载均衡和广播。

**常见面试问法**：
- "Kafka的架构是怎样的？怎么保证高可用？"
- "Kafka和RabbitMQ有什么区别？怎么选型？"

**回答框架**：
1. 核心概念：Broker（Kafka节点）、Topic（逻辑分类）、Partition（物理分片，有序）、Replica（副本，Leader读写+Follower同步）、Consumer Group（组内竞争消费、组间广播）
2. 高可用：每个Partition多个Replica分布在不同Broker；Leader处理读写，Follower从Leader同步（ISR列表）；Leader宕机从ISR选举新Leader（Controller协调）
3. 生产者：指定分区策略（轮询/按key hash/自定义）；acks=0不等响应/acks=1等Leader写入/acks=all等ISR全部写入（最安全）；消息追加到CommitLog顺序写磁盘
4. 消费者：offset由消费者自己维护（__consumer_offsets内部Topic）；Rebalance当组成员变化或分区数变化时触发（可能导致重复消费和消费暂停）
5. Kafka vs RabbitMQ：Kafka吞吐量百万级、顺序写磁盘+零拷贝、适合日志/大数据流；RabbitMQ万级、路由灵活（Exchange类型丰富）、适合业务消息/延迟队列

**延伸考点**：消息丢失/重复/顺序 → 分库分表 → 分布式事务

---

### 消息丢失、重复、顺序问题 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：消息丢失通过生产者acks=all+消费者手动提交offset+Broker同步复制解决；消息重复通过消费者幂等性（唯一ID+去重表）解决；消息顺序通过相同key路由到同一Partition+单线程消费或内存队列保证。

**常见面试问法**：
- "Kafka怎么保证消息不丢失？"
- "怎么保证消息的顺序性？消息重复消费怎么处理？"

**回答框架**：
1. 消息丢失三环节：
   - 生产者：acks=all（ISR全部确认）+ retries重试 + 幂等Producer（PID+SequenceNumber去重）
   - Broker：min.insync.replicas≥2 + replication.factor≥3 + unclean.leader.election.enable=false
   - 消费者：enable.auto.commit=false手动提交offset，业务处理完成后再提交
2. 消息重复：网络抖动导致生产者重发或消费者Rebalance重复消费。解决：消费者幂等——唯一消息ID（业务ID/Kafka的key）+去重表/Redis Set/数据库唯一约束
3. 消息顺序：同一业务key路由到同一Partition（ProducerRecord指定key）→Partition内消息有序→单线程消费或Consumer端内存队列（按key hash到内存队列，每个队列单线程处理）
4. 精确一次语义：Kafka事务（Producer设置transactional.id，Consumer设置isolation.level=read_committed）适合消费-生产链路
5. RabbitMQ对比：RabbitMQ通过消息确认机制（publisher confirm + consumer ack）+ 消息持久化保证不丢失；顺序性需单Queue单Consumer

**延伸考点**：Kafka架构 → 分布式事务 → 幂等设计

---

### MySQL索引优化 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：InnoDB索引结构为B+树（非叶子节点只存键值、叶子节点链表连接，3层可存2000万+行）；索引优化核心是最左前缀原则、覆盖索引减少回表、避免索引失效场景（函数/隐式转换/like左模糊/OR条件）。

**常见面试问法**：
- "为什么MySQL用B+树而不是B树/红黑树？"
- "什么情况下索引会失效？"

**回答框架**：
1. B+树优势：非叶子节点不存数据→扇出大→树矮（3层约2000万行）→磁盘IO少；叶子链表→范围查询高效；对比B树（每个节点存数据扇出小树高）、红黑树（二叉树高IO多）、Hash（不支持范围查询）
2. 聚簇索引 vs 二级索引：聚簇索引叶子存完整行数据（主键索引，一张表只有一个）；二级索引叶子存主键值，查询非索引列需回表
3. 最左前缀原则：联合索引(a,b,c)相当于a、(a,b)、(a,b,c)三个索引；遇到范围查询(>/</between/like)后索引列失效；优化器可能调整顺序但必须包含最左列
4. 覆盖索引：查询列全部在索引中，无需回表，Explain中Extra显示Using index
5. 索引失效场景：对索引列使用函数/计算、隐式类型转换（varchar列用int查）、like '%xx'左模糊、OR条件一侧无索引、NOT IN/NOT EXISTS、索引列用!=（部分场景）

**延伸考点**：Explain分析 → 分库分表 → 事务隔离级别

---

### MySQL Explain执行计划 [频率: ⭐⭐⭐⭐]

**核心要点**：Explain是SQL调优的核心工具，重点关注type（访问类型从好到差：system>const>eq_ref>ref>range>index>ALL）、key（实际使用索引）、rows（预估扫描行数）、Extra（Using index覆盖索引/Using filesort额外排序/Using temporary临时表）。

**常见面试问法**：
- "Explain的各个字段代表什么？type有哪些值？"
- "Using filesort和Using temporary是什么意思？怎么优化？"

**回答框架**：
1. 关键字段：id（执行顺序，子查询id递增）、select_type、table、type（重要）、possible_keys、key、key_len、ref、rows、filtered、Extra
2. type从优到差：system（单行系统表）→const（主键/唯一索引等值查询）→eq_ref（关联查询主键/唯一索引）→ref（非唯一索引等值查询）→range（索引范围扫描）→index（全索引扫描）→ALL（全表扫描，必须优化）
3. Extra关键值：Using index（覆盖索引，好）、Using where（Server层过滤）、Using index condition（ICP下推）、Using filesort（额外排序，需优化→加合适索引）、Using temporary（临时表，需优化→GROUP BY列加索引）
4. key_len计算：varchar(n) utf8mb4 = n×4+2+1（可为null加1）；int = 4；联合索引key_len可判断使用了哪些列
5. 优化思路：ALL→加索引、filesort→ORDER BY列加索引、temporary→GROUP BY列加索引、rows大→优化查询条件

**延伸考点**：索引优化 → 慢查询日志 → 分库分表

---

### MySQL分库分表 [频率: ⭐⭐⭐⭐]

**核心要点**：分库分表解决单库单表数据量过大导致的性能瓶颈，水平分表按规则（Hash/Range）将数据拆分到多张表，水平分库进一步分散到不同数据库实例；常用中间件ShardingSphere/MyCat，分片键选择和跨分片查询是核心难点。

**常见面试问法**：
- "什么时候需要分库分表？怎么分？"
- "分库分表后有哪些问题？怎么解决？"

**回答框架**：
1. 分表时机：单表数据超过500万~2000万行或单表数据文件超过10GB（非绝对，与字段数和查询复杂度有关）
2. 分片策略：Hash分片（取模，数据均匀但扩容需迁移）、Range分片（按时间/ID范围，扩容容易但可能热点）、一致性Hash（减少迁移量）
3. 分库分表后问题：①跨分片JOIN→应用层组装或冗余字段②跨分片排序分页→各分片查询后归并③分布式主键→雪花算法④分布式事务→Seata⑤数据迁移→双写+增量同步
6. 中间件：ShardingSphere（Proxy+JDBC双模式，社区活跃）、MyCat（数据库代理，维护较少）
7. 替代方案：TiDB/NewSQL（兼容MySQL协议，原生分布式）、MySQL Partition（分区表，单实例内，有限制）

**延伸考点**：分布式ID → 分布式事务 → MySQL主从复制

---

### MySQL主从复制 [频率: ⭐⭐⭐⭐]

**核心要点**：MySQL主从复制基于Binlog实现，主库写Binlog→从库IO Thread拉取写Relay Log→从库SQL Thread重放；异步复制可能丢数据、半同步复制至少一个从库确认、组复制（MGR）基于Paxos实现强一致。

**常见面试问法**：
- "MySQL主从复制的原理是什么？有哪些复制模式？"
- "主从延迟怎么解决？"

**回答框架**：
1. 复制流程：主库事务提交→写Binlog（statement/row/mixed格式）→从库IO Thread连接主库请求Binlog→写入本地Relay Log→SQL Thread读取Relay Log重放SQL
2. 三种同步模式：异步复制（主库不等从库，可能丢数据）、半同步复制（至少一个从库确认收到Binlog，rpl_semi_sync_master_wait_point控制）、全同步（所有从库确认，性能差）
3. 主从延迟原因：从库单线程重放（MySQL5.7+多线程并行复制基于组提交）、大事务、从库机器性能差、网络延迟
4. 解决延迟：①开启并行复制（slave_parallel_workers）②业务层读写分离时读主库（关键业务）③半同步复制④从库读时判断seconds_behind_master⑤中间件路由（ShardingSphere强制主库路由）
5. Binlog格式：statement（SQL语句，主从可能不一致如NOW()）、row（行变更，数据量大但一致性好，推荐）、mixed（混合）

**延伸考点**：分库分表 → 分布式事务 → 数据一致性

---

## 四、分布式

### CAP与BASE理论 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：CAP定理指出分布式系统无法同时满足一致性(C)、可用性(A)、分区容错性(P)，网络分区必然存在所以只能在CP和AP之间选择；BASE理论是对CAP的实践补充，追求基本可用(B)、软状态(S)、最终一致性(E)。

**常见面试问法**：
- "CAP理论是什么？为什么不能同时满足？"
- "CP和AP系统分别有哪些？怎么选择？"

**回答框架**：
1. CAP三要素：Consistency（线性一致性，读操作返回最近写入值）、Availability（每个请求得到非错误响应）、Partition tolerance（网络分区时系统继续运行）
2. 为什么不可兼得：网络分区时，要么拒绝部分请求保证一致性(CP)，要么接受所有请求但可能返回旧数据(AP)
3. CP系统：ZooKeeper（Leader选举期间不可用）、etcd、HBase；AP系统：Eureka（不保证强一致）、Cassandra、DynamoDB
4. BASE：Basically Available（基本可用，允许响应时间增加或功能降级）、Soft State（软状态，允许中间状态）、Eventually Consistent（最终一致性，一段时间后数据一致）
5. 实际选择：金融场景选CP、互联网高并发场景选AP、核心交易CP+非核心AP混合

**延伸考点**：分布式事务 → 分布式一致性算法（Raft/Paxos） → 注册中心选型

---

### 分布式事务 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：分布式事务解决跨服务/跨库的数据一致性问题，2PC强一致但阻塞、TCC柔性事务性能好但开发复杂、Saga长事务编排适合业务流程、Seata框架支持AT/TCC/Saga/XA四种模式。

**常见面试问法**：
- "分布式事务有哪些解决方案？各自优缺点？"
- "Seata的AT模式原理是什么？"

**回答框架**：
1. 2PC（两阶段提交）：协调者→Phase1所有参与者准备(锁定资源)→Phase2全部成功则提交/任一失败则回滚；问题：同步阻塞、单点故障（协调者宕机）、数据不一致（网络分区部分提交部分未收到）
2. TCC（Try-Confirm-Cancel）：业务层面实现，Try预留资源→Confirm确认→Cancel补偿；优点无锁高性能；缺点开发量大（每个操作需3个方法）、空回滚和幂等处理
3. Saga：长事务拆分为多个本地事务，每个事务有对应补偿操作，正向执行失败则反向补偿；适合业务流程长的场景；缺乏隔离性（脏读问题）
4. Seata AT模式：一阶段：业务SQL+undo_log（前后镜像）→二阶段：自动提交或根据undo_log回滚；对业务无侵入，只需加@GlobalTransactional注解
5. 选择建议：强一致要求高→2PC/XA、性能要求高→TCC、流程长→Saga、快速接入→Seata AT

**延伸考点**：CAP/BASE → 消息最终一致性 → 幂等设计

---

### 分布式ID生成 [频率: ⭐⭐⭐⭐]

**核心要点**：分布式ID要求全局唯一、趋势递增、高性能、高可用；雪花算法（Snowflake）是最常用方案，64位=1位符号+41位时间戳+10位机器ID+12位序列号，单机每秒可生成400万+ID；时钟回拨是核心风险。

**常见面试问法**：
- "分布式ID有哪些方案？雪花算法的原理？"
- "雪花算法时钟回拨怎么解决？"

**回答框架**：
1. 方案对比：UUID（无序、无业务含义、索引性能差）、数据库自增（单点瓶颈）、号段模式（预分配号段，数据库压力小但可能不连续）、Redis INCR（依赖Redis）、雪花算法（主流）
2. 雪花算法结构：0(1位) + 时间戳(41位，69年) + 机器ID(10位，1024台) + 序列号(12位，4096/ms)；时间戳为当前时间-纪元时间的差值
3. 时钟回拨问题：NTP同步导致时间倒退→可能生成重复ID。解决：①回拨小（<5ms）等待追回②回拨大则拒绝服务或使用历史最大时间③百度UidGenerator使用RingBuffer④美团Leaf引入Zookeeper
4. 美团Leaf：Leaf-segment号段模式（数据库预分配）+ Leaf-snowflake（Zookeeper分配workerId，弱依赖ZK）
5. 其他方案：百度UidGenerator（DefaultUidGenerator/Delta-Loop模式）、MongoDB ObjectId（12字节含时间+机器+进程+计数器）

**延伸考点**：分库分表（分片键选择） → 时钟同步 → ZooKeeper

---

### 限流、降级、熔断 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：限流控制请求速率（计数器/滑动窗口/令牌桶/漏桶），降级在系统压力过大时牺牲非核心功能保核心，熔断在下游服务异常时快速失败避免级联故障；Sentinel是阿里开源的流量防护组件，支持流控/熔断/热点限流/系统保护。

**常见面试问法**：
- "限流算法有哪些？令牌桶和漏桶有什么区别？"
- "熔断器的工作原理？Sentinel和Hystrix有什么区别？"

**回答框架**：
1. 限流算法：①固定窗口计数器（临界点突刺问题）②滑动窗口计数器（细化时间粒度）③漏桶算法（恒定速率处理，平滑但无法应对突发）④令牌桶算法（恒定速率放令牌，允许突发流量，推荐）
2. 降级策略：读降级（返回缓存/默认值）、写降级（异步写/延迟写）、功能降级（关闭非核心功能如推荐）；降级可配置开关（配置中心/注解）
3. 熔断器模式：Closed(正常)→失败率超阈值→Open(快速失败)→超时后→Half-Open(放少量请求探测)→成功则Closed/失败则Open；Hystrix和Sentinel均实现
4. Sentinel vs Hystrix：Sentinel支持QPS/线程数限流、慢调用比例/异常比例/异常数熔断、热点参数限流、系统自适应保护、控制台动态配置；Hystrix基于线程池隔离、已停止维护
5. 实践要点：限流维度（接口/用户/IP/集群）、降级预案分级、熔断粒度（服务级/接口级/实例级）、监控告警闭环

**延伸考点**：缓存雪崩 → 微服务架构 → 高可用设计

---

## 五、项目高频追问

### 系统架构设计追问 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：面试官通过架构设计追问考察技术选型能力和全局视野，需从业务场景出发，说清楚为什么这样设计、有哪些权衡取舍。

**常见面试问法**：
- "系统架构怎么设计的？为什么这样设计？"
- "如果QPS翻倍，你的架构能扛住吗？怎么扩展？"

**回答框架**：
1. 先描述业务背景和核心挑战（如高并发、高可用、数据一致性）
2. 画出整体架构图（网关→服务→缓存→MQ→数据库），说清每一层的作用
3. 说明技术选型理由（为什么选Redis而不是本地缓存、为什么选Kafka而不是RabbitMQ）
4. 指出架构的瓶颈和扩展方案（水平扩展、分库分表、异步化、缓存）
5. 量化指标支撑（QPS、RT、可用性）

**延伸考点**：微服务拆分原则 → 领域驱动设计 → 技术选型方法论

---

### 性能指标与压测追问 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：需说清楚QPS/TPS/RT/并发数等核心指标的含义和实际数据，压测工具（JMeter/wrk/k6）、压测方法（阶梯加压/恒压）、性能瓶颈定位和优化过程。

**常见面试问法**：
- "QPS多少？怎么压测的？"
- "压测发现了什么瓶颈？怎么优化的？"

**回答框架**：
1. 核心指标定义：QPS（每秒请求数）、TPS（每秒事务数）、RT（响应时间P50/P95/P99）、并发数（同时执行请求数）、吞吐量
2. 压测方法：阶梯加压（逐步增加线程数找到系统拐点）、恒压测试（固定并发量持续压测观察稳定性）
3. 压测工具：JMeter（功能全面GUI）、wrk（轻量高性能）、k6（脚本化云原生）
4. 瓶颈定位：CPU（top/火焰图）、内存（jmap/MAT）、IO（iostat）、网络（带宽/连接数）、数据库（慢查询/锁等待）
5. 优化案例：接口RT从500ms→50ms（加索引/加缓存/异步化/批量化/连接池调优）

**延伸考点**：JVM调优 → 数据库优化 → 全链路压测

---

### 数据一致性追问 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：分布式系统中数据一致性是核心难题，需说清楚项目中哪些场景涉及一致性问题、选择了什么方案、为什么这样选、如何保证最终一致性。

**常见面试问法**：
- "怎么保证数据一致性？"
- "缓存和数据库不一致怎么办？"

**回答框架**：
1. 场景识别：缓存与数据库一致性、跨服务数据一致性、主从数据一致性
2. 缓存一致性方案：Cache Aside Pattern（先更新数据库再删除缓存+延迟双删）、Canal监听Binlog异步更新缓存、设置合理过期时间兜底
3. 跨服务一致性：强一致→2PC/Seata AT、最终一致→本地消息表+MQ、TCC适合资金场景
4. 方案选择依据：一致性要求（强/最终）、性能要求、开发复杂度、业务容忍度
5. 兜底机制：对账补偿、定时任务修复、人工干预流程

**延伸考点**：分布式事务 → Redis缓存策略 → 数据库主从复制

---

### 分布式事务处理追问 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：面试官关注你在实际项目中如何落地分布式事务，需要结合具体业务场景说明选型理由、实现细节、遇到的问题和解决方案。

**常见面试问法**：
- "分布式事务怎么处理的？为什么选这个方案？"
- "遇到过分布式事务的问题吗？怎么解决的？"

**回答框架**：
1. 描述业务场景（如订单创建→扣库存→扣余额→扣积分）
2. 方案选型及理由（如选Seata AT因为对业务侵入小、选TCC因为资金场景要求强一致）
3. 实现细节（Seata AT的undo_log、全局锁机制、@GlobalTransactional注解）
4. 遇到的问题（全局锁竞争→优化为读未提交、脏回滚→增加前置校验、性能问题→异步确保+本地消息表）
5. 降级方案（分布式事务失败→人工补偿/对账修复）

**延伸考点**：CAP/BASE → Seata原理 → 消息最终一致性

---

### 性能优化追问 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：性能优化需遵循"度量先行"原则，先定位瓶颈再针对性优化，优化后必须有量化数据验证效果；常见优化方向包括数据库、缓存、代码、JVM、架构层面。

**常见面试问法**：
- "有没有做过性能优化？具体怎么做的？"
- "接口响应慢怎么排查？"

**回答框架**：
1. 排查思路：监控告警→定位慢接口→链路追踪（SkyWalking/Zipkin）→定位瓶颈点（DB/Redis/外部服务/自身代码）
2. 数据库优化：慢查询分析→Explain执行计划→加索引/优化SQL/分库分表/读写分离
3. 缓存优化：加Redis缓存→缓存预热→防穿透/击穿/雪崩→多级缓存（Caffeine+Redis）
4. 代码优化：批量操作替代循环单条、异步化（CompletableFuture/MQ）、连接池/线程池调优、对象复用减少GC
5. JVM优化：选择合适GC（G1/ZGC）、堆大小配置、GC日志分析、OOM排查
6. 架构优化：服务拆分→异步解耦→CDN→网关限流→弹性伸缩
7. 量化效果：QPS从X提升到Y、RT从Xms降低到Yms、GC停顿从Xms降低到Yms

**延伸考点**：JVM调优 → 索引优化 → 全链路监控
