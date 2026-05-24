# 云计算/运维面试八股文库

## 一、Linux

### 常用命令 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：运维需熟练掌握文件操作（grep/awk/sed/find）、进程管理（top/ps/kill）、网络排查（netstat/ss/curl）三大类命令，是排查问题的基础工具。
**常见面试问法**：
- "怎么查找包含error的日志并统计出现次数？"
- "怎么查看某个进程的CPU和内存占用？"
**回答框架**：
1. 文件操作：grep -r "error" log/递归搜索、awk '{print $1}'列提取、sed -i 's/old/new/g'替换、find / -name "*.log"查找
2. 进程管理：ps aux | grep nginx查进程、top -Hp PID查线程、kill -9 PID强杀、lsof -i :80查端口占用
3. 网络排查：ss -tlnp查监听端口、curl -v测试HTTP、tcpdump抓包、nslookup查DNS
4. 组合实战：grep "error" app.log | awk '{print $3}' | sort | uniq -c | sort -rn 统计错误分布
**延伸考点**：Shell脚本 → 系统监控

### 文件权限与用户管理 [频率: ⭐⭐⭐⭐]
**核心要点**：Linux文件权限用rwx三组位表示属主/属组/其他，chmod修改权限、chown修改属主，SUID/SGID/Special权限用于特殊场景。
**常见面试问法**：
- "rwx分别代表什么？755是什么权限？"
- "SUID有什么作用？"
**回答框架**：
1. 权限位：r=4读、w=2写、x=1执行，三组分别对应属主/属组/其他用户
2. 755 = 属主rwx(7) + 属组r-x(5) + 其他r-x(5)，目录需x权限才能进入
3. chmod：chmod 755 file、chmod u+x file、chmod -R递归修改
4. SUID：执行时以文件属主身份运行（如/usr/bin/passwd以root运行修改密码），安全风险需谨慎
**延伸考点**：进程管理 → 安全加固

### 进程管理与信号 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：进程有前台/后台/守护进程三种运行方式，信号是进程间通信机制，SIGTERM(15)优雅终止、SIGKILL(9)强制终止、SIGHUP(1)重载配置。
**常见面试问法**：
- "SIGTERM和SIGKILL有什么区别？"
- "守护进程怎么创建？"
**回答框架**：
1. 进程类型：前台进程（占用终端）、后台进程（&运行）、守护进程（脱离终端，如nginx）
2. 信号：SIGTERM(15)优雅终止（可捕获处理清理）、SIGKILL(9)强制终止（不可捕获）、SIGHUP(1)重载配置
3. 进程状态：R运行、S睡眠、D不可中断睡眠、Z僵尸、T停止
4. 僵尸进程：子进程退出但父进程未wait，用kill -9父进程或修复父进程代码
**延伸考点**：常用命令 → 系统监控

### Shell脚本编写 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Shell脚本是运维自动化的基础，核心是变量/条件/循环/函数+管道组合，实现批量操作和定时任务。
**常见面试问法**：
- "写一个脚本监控磁盘使用率超过80%告警"
- "Shell脚本中$?、$#、$@分别是什么？"
**回答框架**：
1. 特殊变量：$?上一条命令退出码、$#参数个数、$@所有参数、$$当前PID
2. 条件判断：if [ -f file ]文件存在、if [ $a -gt $b ]数值比较、if [ -z $str ]空字符串
3. 循环：for i in $(seq 1 10); do ... done、while read line; do ... done < file
4. 实战：磁盘监控 df -h | awk 'NR>1{print $5}' | sed 's/%//' | while read usage; do [ $usage -gt 80 ] && echo "ALERT"; done
**延伸考点**：常用命令 → 定时任务crontab

### 系统监控与日志分析 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：系统监控关注CPU/内存/磁盘/网络四大指标，日志分析用grep/awk定位问题，是运维日常最核心的工作。
**常见面试问法**：
- "怎么监控系统资源？"
- "日志文件太大怎么处理？"
**回答框架**：
1. CPU：top/htop实时监控、vmstat查看上下文切换、mpstat查看各核CPU
2. 内存：free -h查看使用、vmstat查看swap、slabtop查看内核slab
3. 磁盘：df -h查看使用率、iostat查看IO、du -sh查看目录大小
4. 日志处理：tail -f实时查看、logrotate自动轮转切割、journalctl查systemd日志
**延伸考点**：监控告警体系 → ELK日志平台

---

## 二、Docker

### 镜像与容器 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：镜像是只读的分层模板，容器是镜像的运行实例（加可写层），容器轻量秒级启动，与虚拟机共享内核但隔离进程。
**常见面试问法**：
- "Docker镜像和容器有什么区别？"
- "Docker和虚拟机有什么区别？"
**回答框架**：
1. 镜像：只读分层存储，通过Dockerfile构建，相同层可复用节省空间
2. 容器：镜像+可写层（Container Layer），运行时状态，删除容器可写层丢失
3. Docker vs VM：Docker共享宿主内核、秒级启动、MB级大小；VM独立内核、分钟启动、GB级大小
4. 隔离机制：Linux Namespace（PID/NET/MNT/UTS/IPC/USER）隔离视图、Cgroups限制资源
**延伸考点**：Dockerfile → 镜像分层原理

### Dockerfile指令 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Dockerfile定义镜像构建步骤，每条指令生成一层，CMD/ENTRYPOINT定义启动命令，合理编写可减小镜像体积和构建时间。
**常见面试问法**：
- "CMD和ENTRYPOINT有什么区别？"
- "COPY和ADD有什么区别？"
**回答框架**：
1. FROM：基础镜像，尽量选Alpine减小体积
2. COPY vs ADD：COPY只复制文件、ADD额外支持URL下载和自动解压tar，推荐用COPY
3. CMD vs ENTRYPOINT：CMD可被docker run参数覆盖、ENTRYPOINT不会被覆盖（CMD作为默认参数）
4. 最佳实践：合并RUN减少层数、清理缓存（rm -rf /var/cache/apk/*）、多阶段构建、.dockerignore排除无关文件
**延伸考点**：镜像与容器 → 多阶段构建

### 镜像分层原理 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Docker镜像采用联合文件系统（UnionFS）分层存储，每层只存储与上层的差异，相同层可跨镜像复用，大幅节省存储和传输时间。
**常见面试问法**：
- "Docker镜像为什么分层？有什么好处？"
- "修改一个文件为什么镜像体积会增大？"
**回答框架**：
1. 分层结构：底层→上层依次叠加，最上层容器可写层，下层只读
2. 联合文件系统：OverlayFS将多层合并为一个统一视图，读时从上到下查找，写时COW（Copy-On-Write）
3. 复用优势：多个镜像共享相同的基础层，push/pull只传输差异层
4. 体积增大：修改文件时COW复制整个文件到新层，即使只改一行，删除文件不会减小体积（只是标记删除）
**延伸考点**：Dockerfile → 多阶段构建

### Docker网络模式 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Docker默认bridge模式通过veth pair+bridge实现容器间通信，host模式直接使用宿主网络，overlay用于跨主机通信。
**常见面试问法**：
- "Docker的bridge网络模式是怎么工作的？"
- "容器间怎么通信？"
**回答框架**：
1. bridge：默认模式，docker0网桥+veth pair，容器通过NAT访问外部，--link或自定义bridge实现容器名解析
2. host：容器直接使用宿主网络栈，无网络隔离，性能最好但端口冲突风险
3. none：无网络，仅lo接口，用于安全敏感场景
4. overlay：跨主机容器通信，基于VXLAN隧道，用于Docker Swarm/多主机集群
**延伸考点**：K8s网络 → Service

### 数据卷（Volume/Bind Mount） [频率: ⭐⭐⭐⭐]
**核心要点**：Volume由Docker管理存储在/var/lib/docker/volumes，Bind Mount直接挂载宿主目录，两者实现数据持久化但管理方式不同。
**常见面试问法**：
- "Volume和Bind Mount有什么区别？"
- "容器删除后数据还在吗？"
**回答框架**：
1. Volume：docker volume create创建，Docker管理，推荐方式，支持命名卷和匿名卷
2. Bind Mount：-v /host/path:/container/path直接挂载，依赖宿主目录结构，适合开发环境
3. 数据持久化：容器删除后Volume数据保留（除非docker rm -v），Bind Mount数据在宿主目录
4. 最佳实践：生产用Volume、开发用Bind Mount、敏感配置用tmpfs（内存存储）
**延伸考点**：Docker网络 → K8s PV/PVC

### Docker Compose [频率: ⭐⭐⭐⭐]
**核心要点**：Docker Compose用YAML定义多容器应用，一条命令启动全部服务，核心是服务定义、依赖管理和网络配置。
**常见面试问法**：
- "Docker Compose怎么用的？"
- "服务间依赖怎么处理？"
**回答框架**：
1. 核心配置：services定义容器、volumes定义数据卷、networks定义网络
2. 服务定义：image/build指定镜像、ports映射端口、depends_on控制启动顺序、environment环境变量
3. 依赖管理：depends_on只控制启动顺序，不等待服务就绪，需healthcheck或wait-for-it脚本
4. 常用命令：docker-compose up -d启动、docker-compose logs查看日志、docker-compose down销毁
**延伸考点**：Dockerfile → K8s部署

### 多阶段构建 [频率: ⭐⭐⭐⭐]
**核心要点**：多阶段构建在一个Dockerfile中使用多个FROM，编译阶段用完整镜像构建，运行阶段只拷贝产物到精简镜像，大幅减小最终镜像体积。
**常见面试问法**：
- "怎么减小Docker镜像体积？"
- "多阶段构建的原理是什么？"
**回答框架**：
1. 问题：编译需要完整环境（JDK/Go/Node），但运行只需要编译产物，单阶段镜像包含大量无用依赖
2. 多阶段：第一阶段FROM golang:1.20 AS builder编译→第二阶段FROM alpine:3.18 COPY --from=builder /app/app
3. 体积对比：单阶段可能800MB，多阶段可降到20MB
4. 其他优化：选Alpine基础镜像、合并RUN指令、清理包管理器缓存
**延伸考点**：Dockerfile → 镜像分层

---

## 三、Kubernetes

### K8s核心概念 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：K8s核心是Pod（最小调度单元）、Deployment（无状态应用管理）、Service（服务发现和负载均衡）、ConfigMap/Secret（配置管理）。
**常见面试问法**：
- "Pod和Container有什么区别？"
- "Deployment和StatefulSet有什么区别？"
**回答框架**：
1. Pod：最小调度单元，包含一个或多个容器，共享网络和存储，同Pod容器localhost互通
2. Deployment：管理无状态应用，支持滚动更新/回滚，Pod可随意替换
3. StatefulSet：管理有状态应用，Pod有稳定标识（名称/序号）、稳定持久化、有序部署/删除
4. ConfigMap：存储非敏感配置、Secret：存储敏感数据（Base64编码，需配合RBAC加密）
**延伸考点**：Pod生命周期 → Service

### Pod生命周期与健康检查 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Pod经历Pending→Running→Succeeded/Failed生命周期，通过livenessProbe存活检查和readinessProbe就绪检查控制流量和重启。
**常见面试问法**：
- "livenessProbe和readinessProbe有什么区别？"
- "Pod的CrashLoopBackOff是什么原因？"
**回答框架**：
1. 生命周期：Pending（调度中）→ContainerCreating（创建容器）→Running（运行中）→Succeeded/Failed
2. livenessProbe：存活检查，失败则重启容器（应用死锁但进程还在的场景）
3. readinessProbe：就绪检查，失败则从Service摘除流量（应用启动中或依赖不可用）
4. CrashLoopBackOff：容器启动后立即崩溃反复重启，常见原因：配置错误、OOM、应用异常退出、探针配置不当
**延伸考点**：K8s核心概念 → 滚动更新

### Service类型 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Service通过标签选择器关联Pod，ClusterIP集群内访问、NodePort暴露节点端口、LoadBalancer云厂商负载均衡、ExternalName外部域名映射。
**常见面试问法**：
- "K8s的Service有哪几种类型？"
- "ClusterIP和NodePort有什么区别？"
**回答框架**：
1. ClusterIP：默认类型，分配集群内虚拟IP，仅集群内可访问，适合内部服务
2. NodePort：在ClusterIP基础上，在每个Node上开放端口（30000-32767），外部可通过NodeIP:Port访问
3. LoadBalancer：在NodePort基础上，云厂商自动创建外部负载均衡器，适合生产环境对外暴露
4. ExternalName：映射到外部DNS名称（CNAME），不创建代理，适合引用集群外服务
**延伸考点**：Ingress → K8s网络

### Ingress原理 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Ingress是K8s的HTTP层路由规则，基于域名和路径将外部流量路由到不同Service，Ingress Controller（Nginx/Traefik）负责实现。
**常见面试问法**：
- "Ingress和Service有什么区别？"
- "Ingress是怎么工作的？"
**回答框架**：
1. Ingress vs Service：Service是L4层（TCP/UDP）负载均衡，Ingress是L7层（HTTP/HTTPS）路由
2. 工作原理：外部流量→Ingress Controller→根据Ingress规则（域名/路径）→路由到对应Service→Pod
3. Ingress Controller：Nginx Ingress（最常用）、Traefik（云原生）、Kong（API网关）
4. TLS终止：Ingress配置tls证书，Controller负责HTTPS解密→HTTP转发到后端Pod
**延伸考点**：Service类型 → 网络与安全

### 滚动更新与回滚 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Deployment滚动更新逐步替换旧Pod，maxSurge控制超出副本数、maxUnavailable控制不可用副本数，回滚通过revision历史恢复。
**常见面试问法**：
- "滚动更新的策略是什么？"
- "发布出问题怎么回滚？"
**回答框架**：
1. 滚动更新：kubectl set image触发，逐步创建新Pod删除旧Pod，保证服务不中断
2. 策略配置：maxSurge（最大超出副本数，默认25%）、maxUnavailable（最大不可用副本数，默认25%）
3. 就绪控制：readinessProbe确保新Pod就绪后才删除旧Pod，防止流量打到未就绪Pod
4. 回滚：kubectl rollout undo deployment/xxx回滚到上一版本、kubectl rollout undo --to-revision=N回滚到指定版本
**延伸考点**：Pod生命周期 → 灰度发布

### 资源限制（requests/limits） [频率: ⭐⭐⭐⭐⭐]
**核心要点**：requests是调度时的资源保证（最低保障），limits是运行时的资源上限（最高限制），CPU可超限限速、Memory超限OOMKill。
**常见面试问法**：
- "requests和limits有什么区别？"
- "CPU和Memory的超限行为有什么不同？"
**回答框架**：
1. requests：调度器根据requests决定Pod分配到哪个Node，保证最低资源可用
2. limits：运行时限制容器最大资源使用，超过limits触发限制机制
3. CPU超限：CPU是可压缩资源，超限后throttle限速（CFS quota），容器变慢但不被杀
4. Memory超限：Memory是不可压缩资源，超限后OOMKill直接杀掉容器（OOMKilled状态）
5. QoS等级：Guaranteed（requests=limits）> Burstable（requests<limits）> BestEffort（无requests/limits）
**延伸考点**：HPA → 调度策略

### HPA自动扩缩容 [频率: ⭐⭐⭐⭐]
**核心要点**：HPA根据CPU/内存使用率或自定义指标自动调整Deployment副本数，核心是指标采集→阈值比较→副本数调整的闭环。
**常见面试问法**：
- "HPA怎么工作的？"
- "基于自定义指标怎么做HPA？"
**回答框架**：
1. 工作原理：Metrics Server采集指标→HPA Controller定期比较当前指标与目标值→计算所需副本数→调整Deployment replicas
2. 计算公式：desiredReplicas = ceil(currentReplicas × currentMetricValue / desiredMetricValue)
3. CPU/内存HPA：基于Pod资源使用率，需配置requests才能计算使用率
4. 自定义指标：Prometheus Adapter将Prometheus指标暴露给K8s API，HPA基于自定义指标（QPS/队列长度）扩缩
**延伸考点**：资源限制 → 调度策略

### 调度策略 [频率: ⭐⭐⭐⭐]
**核心要点**：K8s调度器根据节点资源/亲和性/污点等条件为Pod选择最优Node，nodeSelector简单标签匹配、affinity更灵活、taint/toleration实现节点隔离。
**常见面试问法**：
- "Pod怎么调度到指定节点？"
- "taint和toleration有什么用？"
**回答框架**：
1. nodeSelector：最简单，通过标签匹配节点（nodeSelector: disktype: ssd）
2. nodeAffinity：更灵活，支持In/NotIn/Exists等操作符，required（硬性）/preferred（软性）
3. podAffinity/podAntiAffinity：Pod亲和/反亲和，将Pod调度到有特定Pod的节点/远离特定Pod
4. taint/toleration：节点打污点（taint）排斥Pod，Pod设置容忍（toleration）才能调度到该节点，用于GPU节点/专用节点隔离
**延伸考点**：资源限制 → HPA

---

## 四、CI/CD

### Jenkins Pipeline进阶 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Jenkins Pipeline用代码定义CI/CD流程，支持多阶段并行、条件执行、参数化构建，共享库实现流水线模板复用。
**常见面试问法**：
- "Jenkins Pipeline怎么写的？"
- "怎么实现多环境部署？"
**回答框架**：
1. 声明式Pipeline：pipeline { agent any; stages { stage('Build') { steps { ... } } } }
2. 多环境：参数化构建（choice参数选择dev/staging/prod）+ when条件执行不同部署逻辑
3. 并行执行：stage('Test') { parallel { stage('Unit') { ... }; stage('Integration') { ... } } }
4. 共享库：将通用逻辑封装为vars/*.groovy，多项目复用，统一流水线标准
**延伸考点**：Git工作流 → ArgoCD

### GitOps与ArgoCD [频率: ⭐⭐⭐⭐⭐]
**核心要点**：GitOps以Git仓库为唯一事实来源，ArgoCD监听Git仓库变化自动同步到K8s集群，实现声明式、可审计、自动化的持续部署。
**常见面试问法**：
- "什么是GitOps？和传统CI/CD有什么区别？"
- "ArgoCD怎么工作的？"
**回答框架**：
1. GitOps核心：Git仓库存储所有K8s清单（YAML/Helm/Kustomize），变更通过Git提交触发部署
2. 传统CI/CD vs GitOps：传统方式CI推送部署到集群；GitOps由集群内的ArgoCD拉取Git变更并同步
3. ArgoCD工作：监听Git仓库→检测变更→自动/手动同步到K8s→实时显示应用状态（Synced/OutOfSync）
4. 优势：Git审计追踪、回滚即git revert、多集群管理、应用状态可视化
**延伸考点**：Jenkins Pipeline → 灰度发布

### 灰度发布策略 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：灰度发布逐步将流量切换到新版本，蓝绿部署全量切换、金丝雀发布按比例渐进、滚动更新逐步替换，选择取决于风险容忍度。
**常见面试问法**：
- "蓝绿部署和金丝雀发布有什么区别？"
- "K8s怎么做金丝雀发布？"
**回答框架**：
1. 蓝绿部署：两套完整环境（蓝/绿），流量一次性切换，回滚快但资源浪费
2. 金丝雀发布：先给新版本少量流量（5%），观察无问题后逐步扩大（20%→50%→100%）
3. K8s实现：Deployment+Service权重路由或Istio VirtualService精确流量比例
4. 最佳实践：灰度期间监控核心指标（错误率/延迟/业务指标）、设置自动回滚阈值
**延伸考点**：滚动更新 → 监控告警

### 制品管理 [频率: ⭐⭐⭐⭐]
**核心要点**：制品管理（Harbor/Nexus）统一存储Docker镜像/Maven包/NPM包，支持版本管理、安全扫描和访问控制。
**常见面试问法**：
- "Harbor和Nexus有什么区别？"
- "镜像安全扫描怎么做的？"
**回答框架**：
1. Harbor：专注容器镜像仓库，支持镜像复制、RBAC、漏洞扫描（Trivy/Clair）、镜像签名
2. Nexus：通用制品仓库，支持Maven/NPM/Docker/PyPI等多种格式
3. 安全扫描：CI中集成Trivy/Clair扫描镜像漏洞，Critical/High漏洞阻断发布
4. 最佳实践：镜像标签用Git SHA而非latest、定期清理旧版本、私有仓库配置镜像拉取密钥
**延伸考点**：Docker基础 → CI/CD流水线

---

## 五、监控告警

### Prometheus + Grafana [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Prometheus是时序数据库+采集引擎，通过Pull模式采集指标，Grafana可视化展示，两者组合是云原生监控的事实标准。
**常见面试问法**：
- "Prometheus的工作原理是什么？"
- "Prometheus的Pull模式和Push模式有什么区别？"
**回答框架**：
1. 架构：Prometheus Server（采集+存储）→PromQL查询→Grafana可视化→Alertmanager告警
2. Pull模式：Prometheus主动拉取目标指标（/metrics端点），目标需暴露HTTP接口，适合云原生
3. Push模式：应用主动推送指标到Pushgateway，Prometheus从Pushgateway拉取，适合短生命周期任务
4. 数据模型：指标名{标签=值} 时间戳 数值，如http_requests_total{method="GET",status="200"}
**延伸考点**：PromQL → 告警规则

### PromQL [频率: ⭐⭐⭐⭐⭐]
**核心要点**：PromQL是Prometheus的查询语言，支持即时查询、范围查询、聚合运算和函数操作，是编写告警规则和看板的基础。
**常见面试问法**：
- "PromQL怎么写？rate和irate有什么区别？"
- "怎么计算HTTP请求的5分钟平均错误率？"
**回答框架**：
1. 即时查询：http_requests_total返回当前时刻所有时间序列
2. 范围查询：http_requests_total[5m]返回5分钟内所有数据点
3. rate vs irate：rate计算范围区间平均增长率（平滑）、irate计算最近两个数据点增长率（灵敏）
4. 聚合：sum(rate(http_requests_total[5m])) by (method) 按method分组求和
5. 错误率：sum(rate(http_requests_total{status=~"5.."}[5m])) / sum(rate(http_requests_total[5m]))
**延伸考点**：Prometheus → 告警规则

### 告警规则与通知 [频率: ⭐⭐⭐⭐]
**核心要点**：告警规则定义触发条件（PromQL表达式+阈值），Alertmanager负责去重、分组、路由和通知，核心是减少告警风暴和误报。
**常见面试问法**：
- "告警怎么设计的？怎么避免告警风暴？"
- "Alertmanager怎么工作的？"
**回答框架**：
1. 告警规则：expr触发条件+for持续时间+severity级别+labels/annotations
2. Alertmanager功能：去重（相同告警合并）、分组（相关告警合并通知）、抑制（高级别告警抑制低级别）、静默（维护期间静默）
3. 通知渠道：Email/Slack/钉钉/企业微信/Webhook，按团队/级别路由到不同渠道
4. 避免告警风暴：合理设置for持续时间、分级告警（P1/P2/P3）、设置告警阈值需考虑历史波动
**延伸考点**：PromQL → 监控体系设计

### 日志系统（ELK/Loki） [频率: ⭐⭐⭐⭐⭐]
**核心要点**：ELK（Elasticsearch+Logstash+Kibana）是全功能日志平台，Loki是轻量级日志聚合（仅索引标签），选择取决于规模和资源。
**常见面试问法**：
- "ELK和Loki有什么区别？"
- "日志采集的架构是什么？"
**回答框架**：
1. ELK：Filebeat采集→Logstash处理→Elasticsearch存储索引→Kibana查询可视化，功能强大但资源消耗大
2. Loki：Promtail采集→Loki存储（仅索引标签，不索引日志内容）→Grafana查询，轻量但查询灵活性低
3. ELK vs Loki：ELK全文检索强大但存储成本高、Loki轻量低成本但仅标签索引
4. 选型：需要全文检索/复杂分析选ELK、只需按标签查日志+已有Prometheus选Loki
**延伸考点**：监控告警 → 链路追踪

### 链路追踪（Jaeger/SkyWalking） [频率: ⭐⭐⭐⭐]
**核心要点**：链路追踪记录请求在微服务间的调用路径和耗时，Jaeger是CNCF项目轻量简洁，SkyWalking功能丰富带UI和告警。
**常见面试问法**：
- "链路追踪的原理是什么？"
- "Jaeger和SkyWalking怎么选？"
**回答框架**：
1. 核心概念：Trace（一次完整请求链路）、Span（一个操作单元）、SpanContext（跨进程传递的追踪上下文）
2. 原理：请求入口生成TraceID→每个服务生成SpanID→通过HTTP Header/gRPC Metadata传递上下文→收集器聚合展示
3. Jaeger：CNCF毕业项目、轻量、支持多种存储（ES/Cassandra/Kafka）、UI简洁
4. SkyWalking：Apache项目、Java Agent无侵入、功能丰富（拓扑图/告警/Profile）、适合Java微服务
**延伸考点**：日志系统 → 微服务可观测性

---

## 六、网络与安全

### 负载均衡 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：LVS工作在L4层（传输层）性能最高、Nginx工作在L7层（应用层）功能最灵活、HAProxy两者兼顾，选择取决于场景。
**常见面试问法**：
- "LVS、Nginx、HAProxy有什么区别？"
- "四层负载均衡和七层负载均衡有什么区别？"
**回答框架**：
1. L4（传输层）：基于IP+端口转发，LVS DR/NAT模式，性能极高（内核态），但功能单一
2. L7（应用层）：基于HTTP头/URL/Cookie路由，Nginx/HAProxy，支持灰度/A/B/重写，性能低于L4
3. Nginx：反向代理+L7负载均衡+静态文件服务+SSL终止，最通用
4. 典型架构：客户端→LVS(L4)→Nginx(L7)→应用服务，L4扛并发L7做路由
**延伸考点**：DNS/CDN → K8s Ingress

### DNS与CDN [频率: ⭐⭐⭐⭐]
**核心要点**：DNS将域名解析为IP（递归+迭代查询），CDN通过边缘节点缓存静态资源就近服务用户，两者配合加速内容分发。
**常见面试问法**：
- "DNS解析的过程是什么？"
- "CDN的原理是什么？"
**回答框架**：
1. DNS解析：浏览器缓存→系统缓存→本地DNS→根DNS→顶级DNS→权威DNS，递归+迭代
2. CDN原理：用户请求→DNS解析到最近的CDN边缘节点→边缘节点有缓存则直接返回→无缓存则回源站获取并缓存
3. CDN适用：静态资源（图片/CSS/JS/视频）、大文件下载、直播流媒体
4. 不适用：动态API请求、实时数据、需要强一致性的内容
**延伸考点**：负载均衡 → SSL/TLS

### SSL/TLS [频率: ⭐⭐⭐⭐⭐]
**核心要点**：TLS通过证书验证身份、非对称加密协商密钥、对称加密传输数据，HTTPS = HTTP + TLS，是网络安全的基石。
**常见面试问法**：
- "TLS握手的过程是什么？"
- "HTTPS怎么保证安全的？"
**回答框架**：
1. TLS握手：ClientHello（支持的加密套件）→ServerHello+证书→客户端验证证书→生成会话密钥→加密通信
2. 安全保证：证书验证服务器身份（防中间人）、非对称加密协商密钥（防窃听）、MAC校验数据完整性（防篡改）
3. TLS 1.3优化：1-RTT握手（TLS 1.2需2-RTT）、0-RTT恢复连接、移除不安全加密套件
4. 证书管理：Let's Encrypt免费证书+certbot自动续期、企业用商业CA证书
**延伸考点**：网络与安全 → 容器安全

### 容器安全与镜像扫描 [频率: ⭐⭐⭐⭐]
**核心要点**：容器安全包括镜像安全（漏洞扫描）、运行时安全（权限限制）、网络安全（网络策略）和合规审计，是云原生安全的核心。
**常见面试问法**：
- "Docker容器怎么安全加固？"
- "镜像安全扫描怎么做的？"
**回答框架**：
1. 镜像安全：Trivy/Clair扫描镜像CVE漏洞、使用可信基础镜像、不用root运行、多阶段构建减小攻击面
2. 运行时安全：Security Context限制容器权限（runAsNonRoot/readOnlyRootFilesystem/drop capabilities）
3. 网络安全：NetworkPolicy限制Pod间通信、Service Mesh mTLS加密、Ingress WAF防护
4. 合规审计：OPA/Gatekeeper策略准入控制、Falco运行时异常检测、审计日志记录
**延伸考点**：SSL/TLS → K8s RBAC

---

## 七、高频项目方向

### K8s部署平台 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：K8s部署平台实现应用的可视化部署、多环境管理、灰度发布和回滚，核心是封装K8s API提供友好的运维界面。
**常见面试问法**：
- "K8s部署平台怎么设计的？"
- "多环境怎么管理？"
**回答框架**：
1. 功能模块：应用管理→环境管理→部署发布→监控告警→日志查询
2. 技术架构：前端React/Vue+后端Go/Python+K8s Client库+数据库MySQL
3. 多环境：dev/staging/prod集群隔离，同一套部署逻辑不同配置（Kustomize overlays）
4. 核心能力：一键部署、灰度发布（金丝雀）、自动回滚、资源配额管理、审计日志
**延伸考点**：K8s核心概念 → GitOps

### 监控告警系统 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：监控告警系统基于Prometheus+Grafana+Alertmanager构建，实现指标采集→可视化→告警→通知的完整闭环。
**常见面试问法**：
- "监控告警系统怎么设计的？"
- "告警误报率怎么控制？"
**回答框架**：
1. 架构：Exporter采集→Prometheus存储→Grafana展示→Alertmanager告警→通知渠道
2. 监控层次：基础设施（CPU/内存/磁盘）→中间件（MySQL/Redis/Kafka）→应用（QPS/延迟/错误率）→业务（订单量/支付率）
3. 告警分级：P1紧急（电话）、P2重要（IM+邮件）、P3一般（邮件）、P4提示（仅看板）
4. 误报控制：合理设置for持续时间、动态阈值（基于历史基线）、告警收敛（分组+抑制）
**延伸考点**：Prometheus → 告警规则

### CI/CD流水线项目 [频率: ⭐⭐⭐⭐]
**核心要点**：CI/CD流水线实现代码提交→构建→测试→部署的全自动化，核心是流水线设计、多环境管理和质量门禁。
**常见面试问法**：
- "CI/CD流水线怎么设计的？"
- "怎么保证部署质量？"
**回答框架**：
1. 流水线阶段：代码提交→代码检查（SonarQube）→构建镜像→安全扫描→部署dev→自动化测试→部署staging→人工验证→部署prod
2. 质量门禁：单元测试覆盖率>80%、0个Critical安全漏洞、代码异味不增加、集成测试通过
3. 多环境：dev自动部署、staging自动部署+人工验证、prod人工审批+灰度发布
4. 工具链：GitLab/GitHub+Jenkins/ArgoCD+Harbor+SonarQube+Slack通知
**延伸考点**：Jenkins Pipeline → 灰度发布

### 日志分析平台 [频率: ⭐⭐⭐⭐]
**核心要点**：日志分析平台实现日志采集→存储→查询→可视化→告警，核心是ELK/Loki+采集Agent+告警规则。
**常见面试问法**：
- "日志平台怎么设计的？"
- "日志量太大怎么处理？"
**回答框架**：
1. 架构：Filebeat/Promtail采集→Kafka缓冲→Logstash/Loki处理→ES/Loki存储→Kibana/Grafana查询
2. 日志规范：统一JSON格式、必含traceId/requestId/timeStamp/level/service字段
3. 大量日志：Kafka缓冲削峰、ES冷热数据分离（热数据SSD/冷数据HDD）、日志采样（非ERROR级别采样1%）
4. 告警：基于日志内容的实时告警（ERROR率超阈值）、基于日志量的异常告警
**延伸考点**：ELK/Loki → 链路追踪

### 自动化运维（IaC） [频率: ⭐⭐⭐⭐]
**核心要点**：基础设施即代码（IaC）用代码定义和管理基础设施，Ansible配置管理、Terraform资源编排，实现可版本化可复现的运维。
**常见面试问法**：
- "Ansible和Terraform有什么区别？"
- "基础设施即代码有什么好处？"
**回答框架**：
1. Terraform：声明式资源编排，定义期望状态自动规划变更，管理云资源（VPC/ECS/RDS/K8s）
2. Ansible：声明式配置管理，通过Playbook批量配置服务器，无需Agent（SSH连接）
3. 区别：Terraform管资源生命周期（创建/修改/销毁）、Ansible管配置状态（安装/配置/启动）
4. IaC好处：版本控制可追溯、环境可复现、变更可审计、团队协作、灾难恢复快速
**延伸考点**：CI/CD → GitOps

---

## 八、项目高频追问

### K8s集群规模追问 [频率: ⭐⭐⭐⭐]
**核心要点**：追问考察你对大规模集群的管理经验和踩坑，而非仅会基础操作。
**常见面试问法**：
- "K8s集群规模多大？多少节点？"
- "大规模集群遇到什么问题？"
**回答框架**：
1. 集群规模：N个Master+M个Worker节点、P个Pod、Q个Service
2. 性能问题：etcd读写延迟增大（优化etcd参数+SSD）、API Server QPS瓶颈（增加缓存）、Scheduler调度慢
3. 网络问题：iptables模式规则过多导致延迟（切换IPVS模式）、Service数量过多
4. 运维问题：节点NotReady处理、Pod驱逐策略、集群升级方案（先升级Master再Worker）
**延伸考点**：K8s核心概念 → 调度策略

### Pod频繁重启排查 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：Pod频繁重启是K8s最常见的问题，需从事件、日志、资源、探针四个维度系统性排查。
**常见面试问法**：
- "怎么处理Pod频繁重启？"
- "OOMKilled和CrashLoopBackOff怎么排查？"
**回答框架**：
1. 查看状态：kubectl describe pod查看Events和Last State
2. OOMKilled：内存超限被杀→调大resources.limits.memory→检查内存泄漏→优化代码
3. CrashLoopBackOff：容器启动后崩溃→kubectl logs查错误→配置错误/依赖不可用/探针失败
4. 探针失败：livenessProbe失败导致重启→调整探针参数（initialDelaySeconds/failureThreshold）→检查探针逻辑
5. 资源不足：节点资源不够导致驱逐→增加节点/调整requests/优化资源分配
**延伸考点**：Pod生命周期 → 资源限制

### 监控告警设计追问 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：追问考察你设计监控体系的系统性思维，包括指标选择、告警分级、误报控制和故障响应流程。
**常见面试问法**：
- "监控告警怎么设计的？误报率多少？"
- "怎么避免告警疲劳？"
**回答框架**：
1. 监控层次：基础设施→中间件→应用→业务，从底到上逐层覆盖
2. 告警分级：P1紧急（5分钟响应/电话通知）、P2重要（30分钟/IM）、P3一般（2小时/邮件）
3. 误报控制：for持续时间避免瞬时抖动、动态基线告警替代静态阈值、告警收敛（分组+抑制+静默）
4. 告警疲劳：定期Review告警规则删除无效告警、P3+告警不通知只看板展示、设置告警静默窗口
**延伸考点**：Prometheus → 告警规则

### 线上故障排查追问 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：线上故障排查是运维最核心的能力，追问考察你从发现→定位→恢复→复盘的完整流程和应急能力。
**常见面试问法**：
- "线上故障怎么排查？举一个例子"
- "故障恢复后做什么？"
**回答框架**：
1. 发现：监控告警/用户反馈/巡检发现异常
2. 定位：先止血再定位→查看监控指标（CPU/内存/QPS/延迟）→查看日志→链路追踪→定位根因
3. 止血：回滚版本/扩容/降级/限流/熔断，优先恢复服务再查根因
4. 复盘：故障时间线→根因分析（5 Whys）→改进措施→责任到人→deadline跟踪
5. 改进：加监控/告警、修复代码Bug、优化架构、完善应急预案
**延伸考点**：监控告警 → 性能调优

### 服务高可用保障追问 [频率: ⭐⭐⭐⭐⭐]
**核心要点**：高可用是运维的核心目标，追问考察你对多副本、多可用区、自动故障转移和容灾备份的系统性设计。
**常见面试问法**：
- "怎么保证服务高可用？"
- "单点故障怎么避免？"
**回答框架**：
1. 多副本：Deployment replicas≥2、Pod反亲和性分散到不同节点
2. 多可用区：Pod分布到不同AZ、Service跨AZ负载均衡、存储跨AZ复制
3. 自动故障转移：livenessProbe自动重启、HPA自动扩容、PDB保证最小可用副本
4. 容灾备份：etcd定期备份、数据库主从+跨区域复制、定期容灾演练
5. 限流降级：核心服务限流保护、非核心服务降级、熔断器防止级联故障
**延伸考点**：K8s核心概念 → 监控告警
