# 前端面试八股文库

## 一、JavaScript基础

### 数据类型 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：JavaScript有8种数据类型，分为基本类型（Undefined、Null、Boolean、Number、BigInt、String、Symbol）和引用类型（Object），二者在内存存储和赋值行为上有本质区别。

**常见面试问法**：
- "JavaScript有哪些数据类型？基本类型和引用类型有什么区别？"
- "typeof和instanceof的区别是什么？typeof null为什么是'object'？"

**回答框架**：
1. 列出8种数据类型，明确基本类型存栈内存、引用类型存堆内存
2. 解释typeof的返回值（7种），指出typeof null === 'object'是历史遗留Bug（JS初版用低位标识类型，000开头均为object，null全0被误判）
3. 解释instanceof基于原型链检测，无法判断基本类型，可被Symbol.hasInstance自定义
4. 说明类型转换规则：==隐式转换（ToPrimitive→Number/String）、===严格相等
5. 深浅拷贝区别：浅拷贝只复制一层引用，深拷贝递归复制全部（JSON.parse有局限，structuredClone处理循环引用）

**延伸考点**：闭包与作用域 → 原型与继承

---

### 闭包与作用域 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：闭包是函数与其词法环境的绑定，使得内部函数可以访问外部函数的变量即使外部函数已执行完毕；作用域链在定义时确定（词法作用域），而非调用时。

**常见面试问法**：
- "什么是闭包？能否举一个实际应用场景？闭包会导致内存泄漏吗？"
- "var的变量提升和let的暂时性死区是怎么回事？"

**回答框架**：
1. 定义：闭包 = 函数 + 其创建时的词法环境，本质是作用域链的延伸
2. 原理：执行上下文创建时捕获外层变量对象，形成作用域链，即使外层函数出栈，被引用的变量仍留在堆内存中
3. 应用场景：防抖节流、柯里化、模块模式、私有变量
4. 内存问题：闭包本身不导致泄漏，但未释放的闭包引用会阻止GC回收，需手动置null
5. 变量提升：var声明提升至作用域顶部并初始化为undefined；let/const存在暂时性死区（TDZ），从块级作用域开始到声明语句之间不可访问
6. 词法作用域 vs 动态作用域：JS采用词法作用域，函数的作用域在定义时确定，this是动态绑定

**延伸考点**：数据类型（内存管理） → 异步编程（回调闭包）

---

### 原型与继承 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：每个对象都有`__proto__`指向其构造函数的prototype，形成原型链实现属性查找；`prototype`是函数特有的属性，定义实例共享的方法和属性。

**常见面试问法**：
- "请画出原型链的完整结构，Object.prototype的原型是什么？"
- "ES6的class和ES5的构造函数继承有什么区别？new操作符做了什么？"

**回答框架**：
1. 核心关系：`实例.__proto__ === 构造函数.prototype`，`构造函数.prototype.__proto__ === Object.prototype`，`Object.prototype.__proto__ === null`
2. `__proto__` vs `prototype`：`__proto__`是对象的属性（访问原型），`prototype`是函数的属性（定义原型），`Function.__proto__ === Function.prototype`（鸡生蛋问题）
3. new操作符：创建空对象 → 设置`__proto__`指向构造函数prototype → 执行构造函数绑定this → 返回对象（若构造函数返回对象则用该对象）
4. 继承方式演进：原型链继承（共享引用类型问题）→ 借用构造函数（无法继承原型方法）→ 组合继承（调用两次父构造函数）→ 寄生组合继承（最优ES5方案）→ ES6 class（语法糖，本质寄生组合继承）
5. ES6 class注意点：class声明不会提升、内部默认严格模式、不可不用new调用

**延伸考点**：数据类型（instanceof原理） → 闭包与作用域（模块模式）

---

### 异步编程 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：JavaScript通过Event Loop实现单线程异步，宏任务（setTimeout、I/O、UI渲染）和微任务（Promise.then、MutationObserver）有明确的执行优先级：微任务在当前宏任务结束后、下一个宏任务开始前全部执行。

**常见面试问法**：
- "请说明Event Loop的执行顺序，宏任务和微任务有什么区别？"
- "Promise的原理是什么？async/await和Promise是什么关系？"

**回答框架**：
1. Event Loop流程：执行同步代码（属于宏任务）→ 清空微任务队列 → 必要时渲染 → 取下一个宏任务 → 循环
2. 微任务优先级：process.nextTick > Promise.then > MutationObserver
3. Promise原理：状态机（pending→fulfilled/rejected），then注册回调存入微任务队列，链式调用通过返回新Promise实现
4. async/await：async函数返回Promise，await暂停执行并将后续代码注册为微任务（等价于.then的语法糖）
5. setTimeout精度：最小间隔4ms（嵌套≥5层时），受主线程阻塞影响不保证准时执行，requestAnimationFrame更适合动画场景

**延伸考点**：闭包与作用域（回调中的闭包） → 浏览器渲染原理（宏任务与渲染时机）

---

### ES6+ [频率: ⭐⭐⭐⭐]

**核心要点**：ES6+引入了块级作用域、箭头函数、解构等核心特性，改变了变量声明方式、this绑定规则和数据结构选择。

**常见面试问法**：
- "let/const/var的区别是什么？const定义的对象能修改属性吗？"
- "箭头函数和普通函数有什么区别？什么场景不能用箭头函数？"

**回答框架**：
1. let/const/var：var函数作用域+变量提升，let/const块级作用域+TDZ；const声明时必须初始化且不可重新赋值（但对象属性可修改，因const保证栈内存引用地址不变）
2. 箭头函数：没有this/arguments/super/new.target，this继承外层（定义时确定），不能做构造函数、不能用作Generator
3. 不能用箭头函数的场景：对象方法（this不指向对象）、原型方法、事件回调（需要this指向DOM）、构造函数
4. 解构赋值：数组按位置、对象按属性名，支持默认值和重命名（{name: alias}），函数参数解构
5. Map/Set/WeakMap：Map键可为任意类型、保持插入顺序；Set值唯一；WeakMap键必须为对象、不可遍历、键为弱引用（GC可回收），适用于关联额外数据且不阻止回收

**延伸考点**：数据类型（Symbol） → 原型与继承（class语法糖）

---

## 二、浏览器

### 渲染原理 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：浏览器渲染流程为 DOM解析 → CSSOM解析 → Render Tree → Layout → Paint → Composite，其中重排触发Layout重算，重绘只触发Paint，二者性能代价差异显著。

**常见面试问法**：
- "请描述从输入URL到页面渲染的完整过程"
- "重排和重绘有什么区别？如何减少重排？"

**回答框架**：
1. 渲染流程详解：HTML解析构建DOM Tree → CSS解析构建CSSOM → 合并生成Render Tree（display:none不参与）→ Layout计算节点位置大小 → Paint绘制像素 → Composite合成层（GPU加速）
2. 重排(Reflow)：改变几何属性（宽高、位置、margin等）触发，代价高；重绘(Repaint)：改变外观属性（颜色、阴影等）触发，代价相对低
3. 触发重排的场景：增删DOM、修改几何属性、读取offsetWidth等强制同步布局属性、窗口resize
4. 优化策略：批量修改DOM（DocumentFragment）、避免逐条修改样式（合并class切换）、离线DOM操作、使用transform/opacity代替top/left（触发Composite而非Layout）、requestAnimationFrame集中DOM操作
5. CSS解析阻塞JS执行、JS执行阻塞DOM解析（async/defer可改变阻塞行为）

**延伸考点**：异步编程（requestAnimationFrame） → 性能优化（首屏加载）

---

### 存储 [频率: ⭐⭐⭐⭐]

**核心要点**：浏览器存储方案从Cookie到IndexDB各有适用场景，Cookie设计用于HTTP状态管理而非本地存储，LocalStorage/SessionStorage提供更大容量，IndexDB适合大量结构化数据。

**常见面试问法**：
- "Cookie、LocalStorage、SessionStorage有什么区别？"
- "Cookie的安全属性有哪些？如何防止Cookie被窃取？"

**回答框架**：
1. 对比四者：Cookie（4KB、自动携带、可设过期时间）、LocalStorage（5MB+、持久存储、同源共享）、SessionStorage（5MB+、会话级、标签页隔离）、IndexDB（无上限、异步API、支持事务和索引）
2. Cookie安全属性：HttpOnly（禁止JS访问防XSS窃取）、Secure（仅HTTPS传输）、SameSite（Strict/Lax/None防CSRF）、Domain/Path（作用域控制）
3. SameSite详解：Strict（完全禁止第三方携带）、Lax（导航GET请求允许，默认值）、None（允许但需配合Secure）
4. 存储选择：会话数据用SessionStorage、持久配置用LocalStorage、大量数据用IndexDB、服务端状态用Cookie
5. 注意：LocalStorage同源标签页共享（可通过storage事件跨标签通信）、SessionStorage标签页独立

**延伸考点**：安全（XSS窃取Cookie） → 跨域（Cookie跨域携带）

---

### 安全 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：前端三大安全威胁XSS（注入恶意脚本）、CSRF（伪造用户请求）、CSP（内容安全策略作为防御手段），各自有明确的攻击原理和防御方案。

**常见面试问法**：
- "XSS有哪些类型？如何防御XSS攻击？"
- "CSRF的攻击原理是什么？如何防御？"

**回答框架**：
1. XSS三种类型：存储型（恶意脚本存入数据库，如评论注入，危害最大）、反射型（URL参数中携带脚本，服务端渲染到页面）、DOM型（前端JS直接操作不可信数据写入DOM）
2. XSS防御：输入过滤（白名单）、输出编码（HTML/JS/URL上下文分别编码）、使用textContent代替innerHTML、CSP限制脚本来源、HttpOnly保护Cookie
3. CSRF原理：利用浏览器自动携带Cookie的特性，诱导用户访问恶意页面，向目标站点发送伪造请求
4. CSRF防御：SameSite Cookie（Lax/Strict）、CSRF Token（服务端签发、请求时验证）、验证Referer/Origin头、关键操作二次确认
5. CSP：通过HTTP头或meta标签声明资源加载策略（default-src/script-src/style-src等），禁止inline脚本和eval，上报违规行为

**延伸考点**：存储（Cookie安全属性） → 跨域（同源策略）

---

### 跨域 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：同源策略（协议+域名+端口一致）是浏览器最基本的安全机制，跨域方案中CORS是标准方案，JSONP仅支持GET，代理是开发环境常用方案。

**常见面试问法**：
- "什么是同源策略？为什么要有同源策略？"
- "CORS的简单请求和预检请求有什么区别？"

**回答框架**：
1. 同源策略：限制不同源的DOM访问、Cookie读取、AJAX请求，保护用户数据安全；注意：跨域请求实际已发出并被服务端处理，只是浏览器拦截了响应
2. CORS：服务端设置Access-Control-Allow-Origin等响应头；简单请求（GET/POST+标准头）直接发送；非简单请求先发OPTIONS预检，服务端返回允许的方法和头
3. CORS关键响应头：Access-Control-Allow-Origin（允许的源）、Allow-Methods（允许的方法）、Allow-Headers（允许的请求头）、Allow-Credentials（允许携带Cookie时Origin不能为*）
4. JSONP：利用script标签不受同源限制，回调函数接收数据；仅支持GET、存在安全风险（XSS）、无法获取HTTP状态码
5. 代理方案：开发环境webpack/vite配置proxy（同源代理转发）、生产环境Nginx反向代理

**延伸考点**：安全（CSRF与同源策略） → 存储（Cookie跨域携带）

---

## 三、框架

### React：Virtual DOM与Diff [频率: ⭐⭐⭐⭐⭐]

**核心要点**：Virtual DOM是JS对象描述的DOM抽象层，Diff算法采用同层比较+三种策略（树比较、组件比较、元素比较），将O(n³)降为O(n)。

**常见面试问法**：
- "Virtual DOM的优势是什么？Diff算法的策略是什么？"
- "React的key有什么作用？为什么不建议用index做key？"

**回答框架**：
1. Virtual DOM意义：跨平台抽象、批量更新减少DOM操作、提供声明式编程模型
2. Diff三大策略：只比较同层级（跨层级直接删除重建）、不同类型元素直接替换（不递归比较子树）、相同类型元素更新属性后递归比较子节点
3. key的作用：标识同层节点的身份，帮助Diff识别节点移动/新增/删除，提高复用率
4. index做key的问题：列表增删时index对应关系变化，导致错误的节点复用（如输入框内容错位），应使用稳定唯一标识如id
5. React 18之前是Stack Reconciler（同步递归不可中断），之后Fiber架构实现可中断增量更新

**延伸考点**：React Fiber架构 → Vue Diff算法对比

---

### React：Fiber架构 [频率: ⭐⭐⭐⭐]

**核心要点**：Fiber是将递归不可中断的更新改为链表结构可中断的增量更新，通过时间切片（Time Slicing）实现并发渲染，优先处理高优先级任务（如用户输入）。

**常见面试问法**：
- "React Fiber是什么？为什么要引入Fiber？"
- "Fiber是如何实现任务中断和恢复的？"

**回答框架**：
1. 背景：Stack Reconciler递归更新一旦开始不可中断，长任务阻塞主线程导致卡顿
2. Fiber结构：每个节点是一个Fiber单元，包含child/sibling/return指针形成链表，可随时暂停遍历
3. 双缓冲机制：current树（当前屏幕）和workInProgress树（正在构建），交替切换实现无闪烁更新
4. 调度原理：requestIdleCallback（早期）/ MessageChannel + 时间切片（5ms），任务超时则让出主线程，下一帧继续
5. 优先级调度：Lane模型（31位二进制表示优先级车道），同步任务最高优先级， Suspense/Offscreen最低

**延伸考点**：React Hooks原理 → React 18并发特性

---

### React：Hooks原理 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：Hooks基于Fiber链表存储状态，每次渲染按调用顺序读取对应Hook节点，因此Hooks必须在顶层调用且不能条件判断中调用。

**常见面试问法**：
- "为什么Hooks不能在条件语句中使用？Hooks的原理是什么？"
- "useState和useEffect的执行时机是什么？"

**回答框架**：
1. 存储机制：Hooks以链表形式挂载在Fiber节点的memoizedState上，useState对应hook对象（{memoizedState, queue, next}），useEffect对应effect链表
2. 调用顺序：每次渲染按顺序遍历链表取值，条件语句导致顺序错位则取到错误的状态
3. useState：返回当前hook的memoizedState和dispatch，dispatch将update加入queue并调度渲染，批量更新机制在React 18中默认开启（自动批处理）
4. useEffect：渲染完成后异步执行（微任务），return函数为清理函数在下次effect执行前调用；useLayoutEffect在DOM更新后同步执行（阻塞绘制）
5. useCallback/useMemo：缓存函数/计算结果，依赖数组浅比较决定是否更新，过度使用反而增加比较开销

**延伸考点**：Fiber架构（Hook存储位置） → 闭包与作用域（闭包陷阱）

---

### React：状态管理 [频率: ⭐⭐⭐⭐]

**核心要点**：状态管理方案从Redux的单向数据流到Zustand/Jotai的极简设计，核心取舍在于样板代码量、性能粒度和学习成本。

**常见面试问法**：
- "Redux的工作流程是什么？Redux Toolkit解决了什么问题？"
- "Zustand和Redux有什么区别？如何选择状态管理方案？"

**回答框架**：
1. Redux流程：dispatch(action) → reducer纯函数处理 → 返回新state → 订阅者更新视图；三大原则：单一数据源、state只读、纯函数修改
2. Redux问题：样板代码多（action type/reducer/action creator）、异步需中间件（redux-thunk/redux-saga）
3. Redux Toolkit：createSlice自动生成action creator、Immer处理不可变更新、createAsyncThunk简化异步
4. Zustand：无Provider包裹、直接subscribe、浅比较优化渲染、中间件机制（persist/immer）
5. Jotai：原子化状态（atom），自下而上组合，组件只订阅用到的atom，更新粒度最细
6. 选择依据：小型项目Context+useReducer足够、中大型Redux Toolkit成熟稳定、追求极简Zustand、细粒度更新Jotai

**延伸考点**：React Hooks原理 → 性能优化（避免不必要渲染）

---

### React 18并发特性 [频率: ⭐⭐⭐⭐]

**核心要点**：React 18引入并发渲染（Concurrent Rendering），核心是可中断渲染+自动批处理+过渡更新，提升交互响应体验。

**常见面试问法**：
- "React 18有哪些新特性？自动批处理是什么？"
- "useTransition和useDeferredValue有什么区别？"

**回答框架**：
1. 自动批处理：React 18之前只在React事件中批处理setState，18版本所有场景（setTimeout、Promise、原生事件）均自动批处理，可通过flushSync退出
2. useTransition：将状态更新标记为低优先级过渡更新（startTransition），不阻塞高优先级输入响应，isPending标识过渡状态
3. useDeferredValue：延迟更新值的副本，类似防抖但不会丢失中间状态，适合搜索列表等场景
4. Suspense增强：支持并发模式下的数据获取，配合React.lazy实现代码分割，fallback显示加载状态
5. useId：生成稳定唯一ID，解决SSR hydration时ID不匹配问题

**延伸考点**：Fiber架构（优先级调度） → 性能优化（首屏加载）

---

### Vue：响应式原理 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：Vue2使用Object.defineProperty劫持属性getter/setter，Vue3改用Proxy代理整个对象，解决了Vue2无法检测属性增删和数组索引变化的问题。

**常见面试问法**：
- "Vue2和Vue3的响应式原理有什么区别？"
- "Vue2中为什么不能检测数组和对象的新增属性？Vue3是怎么解决的？"

**回答框架**：
1. Vue2原理：递归遍历data对象，Object.defineProperty为每个属性定义getter（收集依赖）和setter（触发更新），数组通过重写7个变异方法实现响应式
2. Vue2局限：无法检测属性新增/删除（需$set/$delete）、无法检测数组索引直接赋值和length变化、深层对象需递归性能开销大
3. Vue3原理：Proxy代理整个对象，拦截get（track收集依赖）和set（trigger触发更新），懒递归（访问时才代理嵌套对象）
4. Vue3优势：可检测属性增删、数组索引变化、Map/Set等新数据结构、惰性代理提升初始化性能
5. 依赖收集：Vue2的Dep+Watcher（观察者模式），Vue3的effect+track/trigger（响应式系统解耦），computed是惰性effect

**延伸考点**：Vue Diff算法 → 闭包与作用域（依赖收集中的闭包）

---

### Vue：Diff算法 [频率: ⭐⭐⭐⭐]

**核心要点**：Vue2采用双端比较算法，Vue3改为首尾预处理+最长递增子序列优化，减少DOM移动操作。

**常见面试问法**：
- "Vue2和Vue3的Diff算法有什么区别？"
- "什么是最长递增子序列？Vue3为什么要用它？"

**回答框架**：
1. Diff触发时机：响应式数据变化 → 虚拟DOM重新渲染 → 新旧VNode对比 → 最小化DOM操作
2. Vue2双端比较：同时从新旧节点的头头、尾尾、头尾、尾头四个方向比较，匹配则移动指针，减少比较次数
3. Vue3优化：先从头尾去除相同前缀后缀（预处理），剩余节点通过key建立映射，用最长递增子序列确定不需要移动的节点，只需移动其余节点
4. 最长递增子序列(LIS)：贪心+二分查找O(nlogn)，找出新节点序列中相对顺序不变的最长子序列，这些节点无需移动
5. key的作用与React一致：标识节点身份，提高复用率，不使用key则默认"就地复用"策略

**延伸考点**：Vue响应式原理 → React Virtual DOM与Diff

---

### Vue：Composition API [频率: ⭐⭐⭐⭐]

**核心要点**：Composition API以函数形式组织逻辑，替代Options API的按选项类型分散组织，解决Mixin命名冲突和数据来源不清晰的问题。

**常见面试问法**：
- "Composition API和Options API有什么区别？"
- "setup函数/comscript标签中如何使用生命周期？"

**回答框架**：
1. Options API问题：相关逻辑分散在data/methods/computed/watch中、Mixin命名冲突、Mixin数据来源不清晰
2. Composition API优势：按功能组织代码（逻辑聚合）、通过函数复用逻辑（composables）、TypeScript类型推导友好
3. 核心API：ref（基本类型响应式，.value访问）、reactive（对象响应式）、computed、watch/watchEffect、生命周期钩子（onMounted等）
4. ref vs reactive：ref适用所有类型、reactive仅适用对象；reactive解构丢失响应性（需toRefs）；模板中ref自动解包
5. script setup语法糖：编译时自动处理、顶层变量自动暴露给模板、defineProps/defineEmits编译器宏

**延伸考点**：Vue响应式原理（ref/reactive实现） → React Hooks对比

---

### Vue：Pinia vs Vuex [频率: ⭐⭐⭐⭐]

**核心要点**：Pinia是Vue官方推荐的新状态管理库，去除了Vuex的mutations和嵌套模块，API更简洁，TypeScript支持更好。

**常见面试问法**：
- "Pinia和Vuex有什么区别？为什么要用Pinia替代Vuex？"
- "Pinia是如何实现响应式的？"

**回答框架**：
1. Vuex结构：state/getters/mutations(同步)/actions(异步)/modules，mutations是修改state的唯一方式，模块需要namespaced避免命名冲突
2. Pinia改进：去除mutations（actions直接修改state）、扁平化设计（每个store独立无需嵌套模块）、完整的TypeScript推导、更轻量（约1KB）
3. Pinia核心：defineStore定义store、state/getters/actions三要素、storeToRefs解构保持响应性、插件系统扩展（持久化等）
4. 响应式实现：Pinia基于Vue的reactive/ref实现，天然与Vue响应式系统集成，组件外也可使用
5. 迁移：Vuex的mutation → Pinia的action、Vuex模块 → 独立Pinia store、getters/computed保持一致

**延伸考点**：Vue响应式原理 → React状态管理对比

---

## 四、工程化

### 构建工具：Webpack vs Vite [频率: ⭐⭐⭐⭐⭐]

**核心要点**：Webpack基于Bundle机制打包所有模块后启动开发服务器，Vite利用浏览器原生ESM实现按需编译，开发体验显著提升。

**常见面试问法**：
- "Webpack和Vite的核心区别是什么？Vite为什么快？"
- "Vite生产环境为什么还用Rollup打包？"

**回答框架**：
1. 开发模式差异：Webpack从入口递归解析依赖→打包Bundle→启动服务器→浏览器加载；Vite先启动服务器→浏览器请求模块→按需编译返回ESM
2. Vite快的原因：无需打包直接启动（冷启动秒级）、按需编译（只编译当前页面用到的模块）、esbuild预构建依赖（Go编写，比JS工具快10-100倍）、强缓存（依赖缓存、模块304协商缓存）
3. 生产构建：Vite生产环境用Rollup打包（Tree Shaking更成熟、代码分割更优、CSS处理更好），不用esbuild打包因esbuild对代码分割和CSS处理不够完善
4. Webpack优势：生态成熟、Loader/Plugin丰富、微前端集成、复杂项目定制能力强
5. 选型：新项目推荐Vite、遗留大型Webpack项目可逐步迁移（@vitejs/plugin-legacy兼容旧浏览器）

**延伸考点**：Tree Shaking → Babel编译流程

---

### 构建工具：Tree Shaking [频率: ⭐⭐⭐⭐]

**核心要点**：Tree Shaking基于ESM静态分析消除未使用的导出代码，依赖ESM的静态结构特性（import/export在编译时确定），CommonJS无法实现。

**常见面试问法**：
- "Tree Shaking的原理是什么？为什么CommonJS不能Tree Shaking？"
- "sideEffects字段有什么作用？"

**回答框架**：
1. 原理：ESM的import/export是静态声明，编译时可确定依赖关系，标记未使用的export为unused，由压缩工具（Terser）删除死代码
2. CommonJS无法Tree Shaking：module.exports是动态赋值，require可在条件语句中调用，运行时才能确定导出内容
3. sideEffects：package.json中配置，false表示所有文件无副作用可安全Tree Shaking，数组指定有副作用的文件（如CSS、polyfill）
4. 副作用：模块执行时会影响全局的行为（如修改全局变量、注册事件、设置CSS），有副作用的模块不能被Tree Shaking移除
5. 注意事项：避免导出对象（namespace导出不利于分析）、使用具名导出而非默认导出、Babel需配置modules: false保留ESM

**延伸考点**：Webpack vs Vite → ES6+模块系统

---

### 构建工具：Babel [频率: ⭐⭐⭐⭐]

**核心要点**：Babel是JavaScript编译器，核心流程为解析(parse)→转换(transform)→生成(generate)，通过插件机制实现语法转换和Polyfill注入。

**常见面试问法**：
- "Babel的编译流程是什么？preset和plugin有什么区别？"
- "@babel/polyfill和@babel/plugin-transform-runtime有什么区别？"

**回答框架**：
1. 三阶段流程：Parse（@babel/parser将代码转为AST）→ Transform（@babel/traverse遍历AST，插件修改节点）→ Generate（@babel/generator将AST转回代码+sourceMap）
2. preset vs plugin：preset是插件集合（@babel/preset-env包含年度特性插件），plugin是单个转换功能，执行顺序plugin先于preset，plugin从前往后、preset从后往前
3. Polyfill方案对比：@babel/preset-env + useBuiltIns:'usage'（按需引入，污染全局）、@babel/plugin-transform-runtime（不污染全局，提取公共helper，适合库开发）
4. core-js版本：core-js@3支持所有ECMAScript特性包括提案，core-js@2已不维护
5. 常见配置：targets指定目标浏览器、useBuiltIns配置引入策略（false/entry/usage）、corejs指定版本

**延伸考点**：ES6+（语法特性） → Webpack（Loader配置）

---

### 性能优化：首屏加载 [频率: ⭐⭐⭐⭐⭐]

**核心要点**：首屏加载优化的核心思路是减少关键资源体积和数量、利用缓存和预加载策略、将非关键资源延迟加载。

**常见面试问法**：
- "你做过哪些首屏加载优化？效果如何？"
- "SSR和预渲染有什么区别？各自适用什么场景？"

**回答框架**：
1. 资源优化：代码分割（路由懒加载、动态import）、Tree Shaking、压缩（gzip/brotli）、图片优化（WebP/AVIF、响应式图片、懒加载）
2. 加载策略：preload（关键资源提前加载）、prefetch（空闲时加载未来资源）、preconnect（提前建立连接）、dns-prefetch（DNS预解析）
3. 渲染策略：SSR（服务端渲染完整HTML，SEO友好但服务器压力大）、SSG（静态站点生成，构建时生成HTML）、预渲染（prerender-spa-plugin，构建时无头浏览器渲染）
4. 缓存策略：强缓存（Cache-Control: max-age）、协商缓存（ETag/Last-Modified）、CDN缓存、Service Worker离线缓存
5. 指标衡量：FCP（首次内容绘制）、LCP（最大内容绘制）、TTI（可交互时间）、CLS（累积布局偏移）

**延伸考点**：浏览器渲染原理 → 构建工具（代码分割）

---

### 性能优化：网络优化 [频率: ⭐⭐⭐⭐]

**核心要点**：网络优化的核心是减少请求次数、减小传输体积、缩短网络延迟，从DNS解析到响应接收全链路优化。

**常见面试问法**：
- "前端可以做哪些网络层面的优化？"
- "HTTP/2相比HTTP/1.1有哪些改进？"

**回答框架**：
1. 减少请求：合并小资源（雪碧图→SVG sprite/Icon Font）、内联关键CSS、域名分片（HTTP/1.1）→ 多路复用（HTTP/2）
2. 减小体积：gzip/brotli压缩、图片压缩和格式优化、移除无用CSS/JS、请求/响应数据精简
3. 缩短延迟：CDN就近分发、HTTP/2（多路复用、头部压缩HPACK、服务器推送）、HTTP/3（QUIC基于UDP、0-RTT连接）、减少重定向
4. 缓存利用：强缓存避免请求、协商缓存减少传输、Service Worker拦截请求、CDN边缘缓存
5. 请求优化：防抖节流减少频繁请求、请求取消（AbortController）、数据预获取、WebSocket长连接替代轮询

**延伸考点**：首屏加载优化 → 浏览器存储（缓存策略）

---

### 性能优化：运行时优化 [频率: ⭐⭐⭐⭐]

**核心要点**：运行时优化关注主线程不阻塞、内存不泄漏、渲染不卡顿，核心手段是减少主线程工作量、利用Web Worker分流、优化渲染频率。

**常见面试问法**：
- "如何优化长列表的渲染性能？"
- "什么场景下会使用Web Worker？"

**回答框架**：
1. 长列表优化：虚拟列表（只渲染可视区域+缓冲区，react-window/vue-virtual-scroller）、分页加载、无限滚动+IntersectionObserver
2. 计算优化：防抖(debounce)和节流(throttle)控制执行频率、Web Worker将耗时计算移至子线程（不阻塞UI）、requestAnimationFrame集中DOM操作
3. 内存优化：及时解除事件监听、闭包引用置null、避免全局变量累积、WeakMap/WeakSet弱引用、定期检查内存泄漏（DevTools Memory面板）
4. 渲染优化：减少重排重绘、CSS containment（contain属性隔离布局影响）、will-change提示浏览器优化、transform/opacity触发合成层
5. 框架层面：React.memo/useMemo/useCallback减少不必要渲染、Vue的v-once/v-memo静态标记、computed缓存计算结果

**延伸考点**：浏览器渲染原理（重排重绘） → React Hooks（性能优化）

---

## 五、手写题高频

- 防抖(debounce) / 节流(throttle)
- 深拷贝（处理循环引用）
- Promise.all / Promise.race / Promise.allSettled
- call / apply / bind
- 发布订阅模式（EventEmitter）
- 数组扁平化（flat）
- 对象深比较（isEqual）
- LRU缓存
- curry（柯里化）
- instanceof
- new操作符
- 并发控制（limitRequest）
- 模板引擎
- 路由（hash / history）
- 虚拟DOM转真实DOM
