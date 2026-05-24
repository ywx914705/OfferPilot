# 移动端面试八股文库

## 一、Android

### Kotlin协程 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：协程是轻量级线程，通过挂起函数实现非阻塞异步，基于状态机编译转换，比RxJava更简洁、比Handler更安全。
**常见面试问法**：
- "协程的原理是什么？挂起函数怎么实现的？"
- "协程和RxJava有什么区别？"
**回答框架**：
1. 协程本质：编译器将挂起函数转换为状态机，通过Continuation传递恢复点，在挂起点保存状态、恢复时从保存点继续
2. 挂起函数：suspend关键字修饰，只能在协程或另一个挂起函数中调用，挂起时不阻塞线程
3. 协程vs RxJava：协程代码线性可读、学习成本低；RxJava操作符丰富、错误处理成熟、适合复杂流
4. 协程vs Handler：协程结构化并发、自动取消、无回调地狱；Handler易泄漏、回调嵌套
**延伸考点**：协程作用域 → Jetpack ViewModel

### Activity生命周期与启动模式 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Activity生命周期从onCreate到onDestroy，启动模式决定实例创建和任务栈行为，standard/singleTop/singleTask/singleInstance各有适用场景。
**常见面试问法**：
- "Activity的生命周期有哪些？什么时候走哪个回调？"
- "singleTask和singleInstance有什么区别？"
**回答框架**：
1. 完整生命周期：onCreate→onStart→onResume→onPause→onStop→onDestroy
2. 可见性变化：onStart/onStop控制可见性，onResume/onPause控制前台交互
3. 启动模式：standard（每次新建）、singleTop（栈顶复用）、singleTask（栈内复用并清上方）、singleInstance（独占新栈）
4. 典型场景：首页用singleTask、推送跳转用singleTop、来电用singleInstance
**延伸考点**：Fragment生命周期 → 任务栈管理

### Fragment生命周期与通信 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Fragment生命周期与宿主Activity联动，通信推荐使用ViewModel+LiveData或Fragment Result API，避免直接引用。
**常见面试问法**：
- "Fragment的生命周期和Activity有什么关系？"
- "Fragment之间怎么通信？"
**回答框架**：
1. 生命周期：onAttach→onCreate→onCreateView→onViewCreated→onStart→onResume→onPause→onStop→onDestroyView→onDestroy→onDetach
2. 与Activity联动：Activity的onResume后Fragment才onResume，Fragment的onPause先于Activity
3. 通信方式：ViewModel+LiveData（推荐）、Fragment Result API（1.3+）、接口回调（传统）、EventBus（不推荐）
4. 注意事项：避免直接引用其他Fragment、onDestroyView中清理View引用防止泄漏
**延伸考点**：Activity启动模式 → Jetpack Navigation

### Service [频率: ⭐⭐⭐⭐]
**核心要点**：Service分前台/后台/绑定三种使用方式，前台Service需显示通知，后台Service受系统限制，绑定Service与组件生命周期联动。
**常见面试问法**：
- "前台Service和后台Service有什么区别？"
- "Service运行在哪个线程？怎么执行耗时操作？"
**回答框架**：
1. 前台Service：startForeground()显示通知，用户可见，不会被系统杀死，用于音乐播放/下载等
2. 后台Service：无通知，Android 8.0+后台限制严格，可能被系统杀死
3. 绑定Service：bindService()，组件销毁时自动解绑，用于组件间交互
4. 线程：Service默认运行在主线程，耗时操作必须开子线程（IntentService/WorkManager/协程）
**延伸考点**：BroadcastReceiver → 后台任务调度

### View绘制流程 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：View绘制经历measure→layout→draw三阶段，measure确定大小、layout确定位置、draw绘制内容，理解绘制流程是自定义View的基础。
**常见面试问法**：
- "View的绘制流程是什么？"
- "measure过程中MeasureSpec怎么理解的？"
**回答框架**：
1. 入口：ViewRootImpl.performTraversals()→performMeasure→performLayout→performDraw
2. measure：MeasureSpec（EXACTLY/AT_MOST/UNSPECIFIED）决定测量模式，父View约束子View
3. layout：根据measure结果确定View的四个坐标（left/top/right/bottom）
4. draw：绘制背景→绘制自身内容（onDraw）→绘制子View（dispatchDraw）→绘制前景/装饰
5. 优化：避免onDraw中创建对象、减少布局层级、使用ViewStub延迟加载
**延伸考点**：事件分发机制 → 自定义View

### 事件分发机制 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：事件分发遵循Activity→ViewGroup→View的传递链，三个核心方法dispatchTouchEvent/onInterceptTouchEvent/onTouchEvent决定事件走向。
**常见面试问法**：
- "事件分发的流程是什么？"
- "怎么解决滑动冲突？"
**回答框架**：
1. 传递链：Activity.dispatchTouchEvent→ViewGroup.dispatchTouchEvent→View.dispatchTouchEvent
2. 三个方法：dispatchTouchEvent（分发）、onInterceptTouchEvent（拦截，仅ViewGroup有）、onTouchEvent（处理）
3. 处理规则：先传后处理，未被处理的事件回传给父View的onTouchEvent
4. 滑动冲突：外部拦截法（父View的onInterceptTouchEvent中判断）、内部拦截法（子View的dispatchTouchEvent中请求父View不拦截）
**延伸考点**：View绘制流程 → RecyclerView缓存

### RecyclerView优化与缓存机制 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：RecyclerView四级缓存（Scrap→Cache→ViewCacheExtension→RecycledViewPool），复用机制大幅提升列表性能。
**常见面试问法**：
- "RecyclerView的缓存机制是什么？"
- "RecyclerView怎么优化？"
**回答框架**：
1. 四级缓存：Scrap（屏幕内缓存，不参与回收）、Cache（屏幕外缓存，默认2个）、ViewCacheExtension（自定义缓存）、RecycledViewPool（按viewType缓存，默认5个）
2. 复用流程：先查Scrap→再查Cache→再查Extension→最后查Pool，都没有则createViewHolder
3. 优化手段：setHasFixedSize(true)、DiffUtil增量更新、预加载、共享RecycledViewPool、ViewHolder轻量化
4. 与ListView对比：RecyclerView强制ViewHolder、支持多布局、动画完善、布局管理器解耦
**延伸考点**：事件分发机制 → 自定义View

### 自定义View [频率: ⭐⭐⭐⭐]
**核心要点**：自定义View需重写onMeasure/onLayout/onDraw，自定义ViewGroup还需管理子View布局，关键是理解绘制流程和触摸事件。
**常见面试问法**：
- "自定义View的步骤？"
- "自定义View和自定义ViewGroup有什么区别？"
**回答框架**：
1. 自定义View：继承View→重写onMeasure（处理wrap_content）→重写onDraw（Canvas/Paint绘制）→自定义属性（attrs.xml）
2. 自定义ViewGroup：继承ViewGroup→重写onMeasure（测量子View）→重写onLayout（摆放子View位置）
3. 注意事项：处理padding/wrap_content、避免onDraw中创建对象、postInvalidate()触发重绘
4. 常见场景：圆形头像、仪表盘、流式布局、侧滑删除
**延伸考点**：View绘制流程 → 事件分发机制

### SharedPreferences/Room/DataStore [频率: ⭐⭐⭐⭐]
**核心要点**：SP是轻量KV存储但同步IO易ANR，Room是ORM框架类型安全，DataStore是SP的替代品支持协程和协议缓冲区。
**常见面试问法**：
- "SharedPreferences有什么问题？DataStore怎么解决的？"
- "Room和原生SQLite有什么区别？"
**回答框架**：
1. SP问题：apply()可能ANR（Activity.onPause等待写入）、全量写入、不支持多进程、类型不安全
2. DataStore：Preferences DataStore（KV，替代SP）+ Proto DataStore（类型安全），协程异步、增量写入
3. Room：SQLite的ORM封装，编译期检查SQL、支持LiveData/Flow观察、Migration机制、类型安全
4. 选型：简单KV用DataStore、结构化数据用Room、跨进程用ContentProvider/MMKV
**延伸考点**：SQLite优化 → 数据持久化策略

### 内存泄漏检测与OOM [频率: ⭐⭐⭐⭐⭐]
**核心要点**：内存泄漏是对象被持有无法回收，OOM是内存不足崩溃，LeakCanary检测泄漏，MAT分析堆dump定位根因。
**常见面试问法**：
- "怎么检测和定位内存泄漏？"
- "OOM怎么排查？"
**回答框架**：
1. 常见泄漏：静态持有Activity/View、内部类/匿名类持有外部类、Handler延迟消息、未注销监听器、Bitmap未回收
2. 检测工具：LeakCanary（自动检测泄漏引用链）、Android Profiler（实时内存监控）、MAT（分析堆dump）
3. OOM排查：Profiler抓取堆dump→MAT分析Dominator Tree→找大对象/泄漏对象→定位代码
4. 预防：弱引用替代强引用、静态内部类+弱引用、生命周期结束时清理引用、大图采样加载
**延伸考点**：ANR排查 → 启动优化

### ANR原因与排查 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：ANR是主线程超时导致的弹框，Input超时5秒/Broadcast超时10秒/Service超时20秒，排查关键是找到主线程阻塞点。
**常见面试问法**：
- "ANR是什么？怎么排查？"
- "怎么避免ANR？"
**回答框架**：
1. ANR类型：InputDispatch(5s)、BroadcastQueue(10s前台/60s后台)、Service(20s)、ContentProvider(10s)
2. 排查步骤：adb pull /data/anr/traces.txt→找主线程堆栈→定位阻塞方法
3. 常见原因：主线程IO操作、数据库锁、死锁、耗时计算、Binder超时
4. 预防：耗时操作放子线程、使用StrictMode检测主线程IO、减少锁竞争、优化布局减少过度绘制
**延伸考点**：内存泄漏 → 卡顿优化

### 启动优化与卡顿优化 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：启动优化核心是减少主线程工作量（异步初始化/延迟加载），卡顿优化核心是减少每帧耗时（<16.6ms/60fps）。
**常见面试问法**：
- "App启动怎么优化？"
- "怎么检测和优化卡顿？"
**回答框架**：
1. 启动类型：冷启动（进程不存在，最慢）→温启动（Activity重建）→热启动（Activity在后台）
2. 启动优化：异步初始化（线程池/协程）、延迟加载（首屏后再初始化）、闪屏主题减少白屏、启动器框架管理依赖
3. 卡顿检测：Choreographer帧回调、BlockCanary监控主线程消息耗时、Systrace/Perfetto系统级追踪
4. 卡顿优化：减少布局层级、避免过度绘制、RecyclerView优化、减少主线程IO、协程替代回调
**延伸考点**：ANR排查 → 布局优化

### MVC/MVP/MVVM [频率: ⭐⭐⭐⭐⭐]
**核心要点**：MVC中Controller臃肿，MVP通过接口解耦View和Presenter，MVVM通过数据绑定自动更新UI，Jetpack推荐MVVM。
**常见面试问法**：
- "MVP和MVVM有什么区别？"
- "你们项目用的什么架构？为什么选MVVM？"
**回答框架**：
1. MVC：View→Controller→Model，Controller承担过多逻辑，Activity既是View又是Controller
2. MVP：View和Presenter通过接口通信，Presenter处理逻辑，View只负责UI，但接口过多
3. MVVM：ViewModel持有UI状态，View通过DataBinding/LiveData观察ViewModel，自动更新
4. 选型：MVVM+Jetpack是当前主流，ViewModel生命周期安全、LiveData自动更新、数据驱动UI
**延伸考点**：Jetpack组件 → 依赖注入

### Jetpack核心组件 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Jetpack是一组Android官方库，ViewModel管理UI状态、LiveData观察数据变化、Navigation管理导航、Room持久化数据。
**常见面试问法**：
- "ViewModel为什么能在配置变更时保留数据？"
- "LiveData和Flow有什么区别？"
**回答框架**：
1. ViewModel：存储在ViewModelStore中，配置变更时Activity重建但ViewModelStore保留，onDestroy非配置变更时才清除
2. LiveData：生命周期感知的可观察数据容器，只在活跃状态通知更新，自动解绑避免泄漏
3. LiveData vs Flow：LiveData简单但功能有限（不支持背压/操作符），Flow功能强大（操作符/背压/协程集成）
4. Navigation：Fragment导航框架，Safe Args传参类型安全，Deep Link支持，可视化编辑导航图
**延伸考点**：MVVM架构 → 依赖注入

---

## 二、iOS

### Swift Optional与闭包 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Optional是Swift类型安全的空值处理机制，闭包是自包含的函数代码块，两者是Swift最核心的语言特性。
**常见面试问法**：
- "Optional的本质是什么？解包方式有哪些？"
- "闭包的逃逸和非逃逸有什么区别？"
**回答框架**：
1. Optional本质：枚举enum Optional<T> { case none; case some(T) }，编译器语法糖
2. 解包方式：if let（安全解包）、guard let（提前退出）、强制解包!（可能崩溃）、可选链?.、nil合并??
3. 闭包：自包含代码块，可捕获和存储上下文中的常量和变量
4. 逃逸闭包@escaping：闭包在函数返回后才执行（网络回调），非逃逸闭包在函数内执行，编译器可优化
**延伸考点**：值类型与引用类型 → ARC内存管理

### 值类型 vs 引用类型（struct vs class） [频率: ⭐⭐⭐⭐⭐]
**核心要点**：struct是值类型拷贝语义线程安全，class是引用类型共享语义需ARC管理，Swift优先使用struct。
**常见面试问法**：
- "struct和class有什么区别？"
- "什么时候用struct什么时候用class？"
**回答框架**：
1. 值类型（struct/enum）：赋值时深拷贝、线程安全、栈分配（小对象）、无继承、无需ARC
2. 引用类型（class）：赋值时引用传递、需ARC管理、堆分配、支持继承和多态
3. Copy-on-Write：Swift对Array/Dictionary/String等值类型优化，只在修改时才真正拷贝
4. 选型：数据模型用struct、需要共享状态或继承用class、UI组件用class
**延伸考点**：Swift Optional → ARC内存管理

### ARC内存管理 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：ARC通过编译期插入retain/release自动管理引用计数，强引用循环是内存泄漏主因，用weak/unowned打破循环。
**常见面试问法**：
- "ARC的原理是什么？"
- "怎么解决循环引用？"
**回答框架**：
1. ARC原理：编译器在编译期自动插入retain/release/autorelease，引用计数为0时释放对象
2. 强引用循环：A强引用B，B强引用A，两者都无法释放。常见于闭包捕获self、delegate双向引用
3. 解决方案：weak（引用对象释放后自动置nil，可选类型）、unowned（引用对象释放后不置nil，非可选，访问已释放对象会崩溃）
4. 闭包循环引用：lazy闭包中用[weak self]或[unowned self]打破循环
**延伸考点**：值类型与引用类型 → 内存泄漏检测

### UIViewController生命周期 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：UIViewController生命周期从init→loadView→viewDidLoad→viewWillAppear→viewDidAppear→viewWillDisappear→viewDidDisappear→dealloc，理解每个回调的职责。
**常见面试问法**：
- "UIViewController的生命周期方法有哪些？各做什么？"
- "viewDidLoad和viewWillAppear的区别？"
**回答框架**：
1. init：初始化，不适合做UI相关操作
2. loadView：创建view，默认从nib/storyboard加载，自定义可重写
3. viewDidLoad：view加载完成，适合一次性初始化（添加子视图、网络请求）
4. viewWillAppear：view即将显示，适合每次显示都要更新的操作
5. viewDidAppear：view已显示，适合动画、通知注册
6. viewWillDisappear/viewDidDisappear：适合清理、通知注销
7. dealloc：控制器销毁，清理资源
**延伸考点**：UITableView复用 → Auto Layout

### UITableView复用机制 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：UITableView通过Cell复用池避免频繁创建销毁Cell，复用标识符是关键，数据源和代理分离是核心设计。
**常见面试问法**：
- "UITableView的复用机制是什么？"
- "Cell复用有什么问题？怎么解决？"
**回答框架**：
1. 复用原理：滑出屏幕的Cell进入复用池，新Cell先从复用池取，取到则复用，取不到则创建
2. 复用标识符：dequeueReusableCell(withIdentifier:)根据标识符查找可复用Cell
3. 复用问题：Cell内容残留（重用了带旧数据的Cell），解决：在cellForRowAt中完整设置所有UI
4. 优化：预渲染行高、异步绘制、减少子视图层级、避免动态增删子视图
**延伸考点**：UIViewController生命周期 → 自定义Cell

### 事件响应链 [频率: ⭐⭐⭐⭐]
**核心要点**：iOS事件响应遵循Hit-Testing查找第一响应者→Responder Chain传递事件的机制，与Android事件分发类似但方向相反。
**常见面试问法**：
- "iOS的事件响应链是什么？"
- "hitTest和pointInside的作用？"
**回答框架**：
1. Hit-Testing：从最上层View开始递归调用hitTest:withEvent:和pointInside:withEvent:，找到触摸点最深的View作为第一响应者
2. Responder Chain：第一响应者→父View→...→UIViewController→UIWindow→UIApplication→AppDelegate
3. 事件传递：触摸事件从第一响应者沿Responder Chain向上传递，直到有对象处理
4. 应用：重写hitTest扩大点击区域、重写pointInside穿透点击、手势冲突处理
**延伸考点**：UITableView复用 → Auto Layout

### 离屏渲染与卡顿检测 [频率: ⭐⭐⭐⭐]
**核心要点**：离屏渲染是GPU在当前屏幕缓冲区外额外渲染，触发场景包括圆角+裁剪、阴影、mask等，是iOS卡顿的常见原因。
**常见面试问法**：
- "什么是离屏渲染？为什么会卡顿？"
- "怎么检测和优化离屏渲染？"
**回答框架**：
1. 离屏渲染：GPU需要在离屏缓冲区渲染后再拷贝到帧缓冲区，额外开销导致掉帧
2. 触发场景：cornerRadius+clipsToBounds同时设置、shadow、mask、group opacity、drawRect
3. 检测：Instruments的Core Animation工具勾选Color Offscreen-Rendered Yellow
4. 优化：圆角用CAShapeLayer或预渲染图片、阴影指定shadowPath、避免同时设置圆角和裁剪
**延伸考点**：启动优化 → 性能调优

### iOS启动优化 [频率: ⭐⭐⭐⭐]
**核心要点**：iOS启动分pre-main（dyld加载→runtime初始化→+load）和post-main（main→didFinishLaunchingWithOptions），优化需分别处理。
**常见面试问法**：
- "iOS启动流程是什么？怎么优化？"
- "+load和+initialize有什么区别？"
**回答框架**：
1. pre-main阶段：dyld加载Mach-O→递归加载依赖库→runtime初始化（映射类/分类/协议）→调用+load方法
2. post-main阶段：main()→UIApplicationMain→AppDelegate初始化→首帧渲染
3. pre-main优化：减少动态库数量（<6个）、合并Category、+load延迟到+initialize或dispatch_once
4. post-main优化：延迟初始化非首屏三方SDK、异步加载首屏数据、减少首屏视图层级
**延伸考点**：离屏渲染 → 性能优化体系

---

## 三、跨平台

### Flutter Widget/Element/RenderObject [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Widget是不可变配置描述，Element是Widget的实例化管理生命周期，RenderObject负责布局和绘制，三者构成Flutter渲染管线。
**常见面试问法**：
- "Widget、Element、RenderObject三者什么关系？"
- "Flutter的渲染流程是什么？"
**回答框架**：
1. Widget：不可变配置描述，轻量可频繁重建，是Element的蓝图
2. Element：Widget的实例，管理Widget树和RenderObject树的关联，处理diff更新
3. RenderObject：负责布局（performLayout）和绘制（paint），计算大小和位置
4. 渲染流程：Widget树→Element树→RenderObject树→布局→绘制→合成→显示
**延伸考点**：Flutter状态管理 → Dart语言特性

### Flutter状态管理 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Provider是官方推荐的基础方案，Riverpod是Provider的改进版，Bloc基于事件驱动适合复杂场景，选择取决于项目复杂度。
**常见面试问法**：
- "Provider和Bloc有什么区别？"
- "你们项目用的什么状态管理？为什么？"
**回答框架**：
1. Provider：InheritedWidget封装，简单易用，适合中小项目，缺点是运行时依赖
2. Riverpod：Provider改进版，编译期安全、无需BuildContext、支持自动销毁
3. Bloc/Cubit：事件驱动，状态转换可追踪可测试，适合复杂业务，Cubit是简化版
4. 选型：简单项目用Provider/Riverpod、复杂项目用Bloc、GetX适合快速开发但不推荐大型项目
**延伸考点**：Widget/Element/RenderObject → Platform Channel

### Platform Channel [频率: ⭐⭐⭐⭐]
**核心要点**：Platform Channel是Flutter与原生平台通信的桥梁，支持MethodChannel（方法调用）、EventChannel（事件流）、BasicMessageChannel（消息传递）。
**常见面试问法**：
- "Flutter怎么调用原生功能？"
- "MethodChannel的原理是什么？"
**回答框架**：
1. MethodChannel：双向方法调用，适合一次性操作（拍照/蓝牙/支付）
2. EventChannel：原生向Flutter推送事件流，适合持续数据（传感器/定位）
3. BasicMessageChannel：双向消息传递，适合自定义编解码
4. 原理：Flutter Engine通过C++层桥接Dart和平台代码，消息异步传递，数据需序列化
**延伸考点**：Flutter状态管理 → 混合开发架构

### React Native Bridge/Fabric架构 [频率: ⭐⭐⭐⭐]
**核心要点**：旧架构Bridge通过JSON序列化异步通信性能瓶颈，新架构Fabric通过JSI同步调用+TurboModule+Fabric渲染器大幅提升性能。
**常见面试问法**：
- "RN的Bridge架构有什么问题？"
- "Fabric架构怎么解决Bridge的性能问题？"
**回答框架**：
1. Bridge架构：JS线程→Bridge（JSON序列化）→Native线程，异步通信、序列化开销大、无法同步调用
2. Fabric架构：JSI（JS直接持有C++对象引用，同步调用）、TurboModule（按需加载原生模块）、Fabric渲染器（同步布局/优先级渲染）
3. 性能提升：JSI消除序列化开销、同步调用减少延迟、Fabric渲染更接近原生流畅度
4. 迁移：新架构向后兼容，逐步迁移TurboModule和Fabric组件
**延伸考点**：Platform Channel → 跨平台选型

---

## 四、高频项目方向

### 即时通讯App [频率: ⭐⭐⭐⭐⭐]
**核心要点**：IM核心是WebSocket长连接+消息协议+本地存储+消息列表优化，难点在于连接保活、消息有序性和列表性能。
**常见面试问法**：
- "IM项目怎么设计的？"
- "怎么保证消息不丢失？"
**回答框架**：
1. 架构：WebSocket长连接→消息解析→本地数据库存储→UI刷新
2. 消息可靠性：发送端ACK确认+重传、服务端消息ID递增保证有序、接收端消息去重
3. 连接保活：心跳机制（30s）、断线重连（指数退避）、前台Service/后台任务
4. 列表优化：差量更新、预加载、消息分页加载、图片懒加载
**延伸考点**：WebSocket → 数据持久化

### 短视频App [频率: ⭐⭐⭐⭐]
**核心要点**：短视频核心是播放器+列表预加载+缓存策略，难点在于流畅播放、内存控制和列表滑动体验。
**常见面试问法**：
- "短视频列表怎么实现流畅播放？"
- "视频缓存策略是什么？"
**回答框架**：
1. 播放方案：ExoPlayer（Android）/AVPlayer（iOS），硬解码优先
2. 列表预加载：当前+前后各1个视频预加载，滑动时回收远离的播放器
3. 缓存策略：LRU磁盘缓存+内存缓存，预加载前N秒数据，边下边播
4. 内存优化：复用播放器实例、及时释放解码器、Bitmap采样加载封面
**延伸考点**：RecyclerView优化 → 内存管理

### 跨平台App项目 [频率: ⭐⭐⭐⭐]
**核心要点**：Flutter/RN跨平台项目核心是架构分层（UI层+业务层+平台层），通过Platform Channel桥接原生能力。
**常见面试问法**：
- "跨平台项目怎么设计的？"
- "遇到什么坑？怎么解决的？"
**回答框架**：
1. 架构分层：UI层（Flutter/RN）→业务层（Dart/JS）→平台层（原生Plugin）
2. 原生桥接：通用功能封装Plugin、复杂功能用原生实现+Channel调用
3. 常见坑：热更新（Flutter不支持/RN支持）、长列表性能、平台差异适配
4. 混合栈管理：FlutterEngine复用、RN与原生页面导航统一管理
**延伸考点**：Platform Channel → 混合开发

---

## 五、项目高频追问

### 性能优化追问 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：面试官通过追问验证优化是否真实做过，重点考察指标量化、优化手段和效果验证。
**常见面试问法**：
- "性能怎么优化的？指标提升多少？"
- "优化过程中遇到什么困难？"
**回答框架**：
1. 先说指标：启动时间从X秒降到Y秒、FPS从45提升到58、内存占用减少Z%
2. 再说手段：具体做了什么（异步初始化/布局优化/图片采样/缓存策略）
3. 验证方式：Systrace/Instruments对比优化前后数据、线上APM监控验证
4. 踩坑经验：优化引入新问题（如异步初始化导致时序问题）、权衡取舍
**延伸考点**：启动优化 → 卡顿优化

### 内存泄漏排查追问 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：追问重点考察你对泄漏根因的理解和系统性排查能力，而非仅会用工具。
**常见面试问法**：
- "内存泄漏怎么排查的？"
- "发现过什么隐蔽的内存泄漏？"
**回答框架**：
1. 检测工具：LeakCanary/Instruments→发现泄漏对象→分析引用链→定位代码
2. 常见泄漏：Handler/Runnable持有Activity、单例持有Context、监听器未注销、Bitmap未回收
3. 隐蔽案例：WebView泄漏（需独立进程）、匿名内部类泄漏、静态Drawable持有View
4. 预防体系：代码Review检查生命周期引用、CI集成LeakCanary、定期Profiler巡检
**延伸考点**：内存泄漏检测 → ARC/引用计数

### 网络请求封装追问 [频率: ⭐⭐⭐⭐]
**核心要点**：追问考察你对网络层的整体设计能力，包括错误处理、重试策略、缓存和安全性。
**常见面试问法**：
- "网络请求怎么封装的？"
- "怎么处理网络异常？"
**回答框架**：
1. 封装层次：底层OkHttp/URLSession→中间层Retrofit/Alamofire→上层Repository
2. 统一处理：拦截器添加Token/签名、统一错误码映射、日志拦截器
3. 错误处理：网络错误重试（指数退避）、业务错误统一Toast、Token过期自动刷新
4. 缓存策略：网络优先+本地缓存兜底、ETag/Last-Modified协商缓存
**延伸考点**：HTTP协议 → 数据持久化

### 图片加载/缓存策略追问 [频率: ⭐⭐⭐⭐]
**核心要点**：图片加载框架（Glide/Coil/SDWebImage）的核心是三级缓存+采样+复用，追问考察对缓存策略的理解深度。
**常见面试问法**：
- "图片加载怎么做的？缓存策略是什么？"
- "大图加载怎么优化？"
**回答框架**：
1. 三级缓存：内存缓存（LruCache）→磁盘缓存→网络请求
2. 采样加载：inSampleSize根据ImageView大小采样，避免加载原图到内存
3. Glide特点：生命周期感知（自动暂停/恢复）、GIF支持、图片转换（圆角/模糊）
4. 大图优化：区域解码（BitmapRegionDecoder）、渐进式JPEG、WebP格式、硬件加速解码
**延伸考点**：RecyclerView优化 → 内存管理

### App稳定性保障追问 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：稳定性保障是体系化工程，包括崩溃监控、ANR检测、性能监控、灰度发布和热修复，追问考察全局视野。
**常见面试问法**：
- "怎么保证App稳定性？"
- "线上崩溃怎么处理？"
**回答框架**：
1. 崩溃监控：Bugly/Crashlytics采集崩溃堆栈→符号化→分类归因→修复发版
2. ANR监控：Watchdog线程检测主线程卡顿→上传ANR堆栈→分析优化
3. 性能监控：APM平台监控启动/帧率/内存/网络→设置阈值告警→自动归因
4. 发布策略：灰度发布（5%→20%→50%→100%）→监控核心指标→全量发布
5. 热修复：Tinker/Fix线上紧急Bug，补丁下发→加载→生效
**延伸考点**：ANR排查 → 性能优化体系
