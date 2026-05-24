<div align="center">
  <img src="assets/logo.svg" width="680px" alt="OfferPilot" />

  <br/>

  **AI-Powered Interview Prep System**

  一句话触发完整面经 · 按岗位 · 按公司 · 按阶段

  [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)](LICENSE)
  [![Type: Skill](https://img.shields.io/badge/Type-Skill-orange.svg?style=flat-square)](#什么是-skill)
  [![Positions: 12](https://img.shields.io/badge/Positions-12-blue.svg?style=flat-square)](#支持岗位)
  [![Version: 3.0](https://img.shields.io/badge/Version-3.0-green.svg?style=flat-square)]()
  [![PRs Welcome](https://img.shields.io/badge/PRs-Welcome-brightgreen.svg?style=flat-square)]()

</div>

---

## 项目背景

最近有个找实习的想法，搜面经的过程很痛苦——信息太分散，牛客、知乎、CSDN 到处翻，找到的还经常过时或有错，自己整理又特别浪费时间。后来发现把面经整理成结构化的知识库，配合 AI 工具使用，效率提升了好几倍——AI 读取知识库后，回答变得一致、有深度、有针对性，不再是泛泛而谈。

所以做了这个 Skill，方便自己也方便同样在找实习的同学。一键生成备考方案，不用再自己到处搜面经了。

---

## 什么是 Skill？

Skill 是一种 **AI 可读的能力包**——把专业领域知识、交互规则、输出格式打包成结构化文件，让 AI 编程助手（Cursor、Claude Code、Cline 等）自动理解并按规则执行。

传统方式是你自己翻面经、自己整理笔记、自己问 AI。Skill 让 AI **自动加载面试知识库**，按预设的岗位映射、公司特色、输出格式来回答，就像给 AI 装了一个面试专家的大脑。

```
没有 Skill:  你 -> 问 AI -> AI 泛泛回答（每次不一样，缺深度）
有了 Skill:  你 -> 问 AI -> AI 读取知识库 -> 结构化输出（一致、完整、有针对性）
```

OfferPilot 就是一个面试备考 Skill，包含：
- **INTERVIEW.md** — 系统配置（5种模式 + 岗位映射 + 公司特色 + 异常处理）
- **references/** — 12个岗位的深度知识库（每个考点含频率+问法+回答框架）
- **适配文件** — 自动适配 Cursor / Claude Code / Cline / Trae 等 AI 工具

---

## 核心亮点

- **不是静态题库，是 AI 面试教练** — 说出你的岗位和目标公司，自动生成针对性备考方案
- **结构化知识库，每个考点五要素** — 频率标注 · 核心要点 · 面试问法 · 回答框架 · 延伸考点
- **模拟面试，逐题点评** — 像真实面试官一样逐题出题，回答后逐题点评，最后输出面试报告
- **项目分析，自动识别技术栈** — 发项目路径，AI 自动读取代码、识别技术栈、生成针对性面试题

---

## 快速开始

### 1. 获取项目

```bash
git clone https://github.com/ywx914705/OfferPilot.git
cd OfferPilot
```

或直接下载 ZIP 解压到任意目录。

### 2. 配置 AI 工具

根据你使用的 AI 工具，按以下步骤配置。项目根目录已内置所有适配文件，无需额外创建。

**Claude Code**

```bash
cd OfferPilot
claude
# 直接对话，CLAUDE.md 会自动生效
> 帮我准备字节Java后端实习面试
```

**Cursor**

```bash
# File -> Open Folder -> 选择 OfferPilot 目录
# 打开 Cursor Chat (Ctrl+L)
> 帮我模拟面试，我准备腾讯前端校招
```

**Cline (VS Code 插件)**

```bash
code OfferPilot
# 打开 Cline 面板
> 分析我的项目 /path/to/my-project
```

**Trae IDE**

```bash
# 用 Trae IDE 打开项目文件夹，直接对话
# 项目规则文件 .trae/rules/project_rules.md 自动生效
> 还有2周面试，帮我规划C++后端备考计划
```

**Trae 插件（VS Code / JetBrains）**

```bash
# 安装 Trae 插件后，在设置中添加项目规则
# 规则文件：.trae/rules/project_rules.md
# 或在 AI 对话窗口 → 设置 → 规则 → 导入 INTERVIEW.md
> 帮我准备测试开发面试
```

**通义灵码（VS Code / JetBrains / Lingma IDE）**

```bash
# 用 Lingma IDE 或安装通义灵码插件后打开项目
# 项目规则文件 .lingma/rules/offerpilot.md 自动生效
# 也可在设置 → 规则 → 添加规则 → 类型选"始终生效" → 粘贴 INTERVIEW.md 内容
> 帮我准备阿里Java后端校招面试
```

**CodeGeeX（VS Code / JetBrains）**

```bash
# 安装 CodeGeeX 插件后打开项目
# 在设置中添加自定义 Prompt 模板，指向 INTERVIEW.md
# 或在交互模式中直接输入："基于 INTERVIEW.md 的规则，帮我准备面试"
> 帮我准备前端面试
```

**豆包 MarsCode IDE**

```bash
# 在 MarsCode IDE 中打开项目
# 在 AI 助手设置中添加项目规则，粘贴 INTERVIEW.md 内容
> 帮我准备字节C++后端实习面试
```

**DevEco CodeGenie（华为鸿蒙开发）**

```bash
# 用 DevEco Studio 打开项目
# 项目规则文件 .codegenie/project_rule.md 自动生效
# 或在 CodeGenie 设置 → Rules → Import Rule 导入
> 帮我准备嵌入式面试
```

**WorkBuddy（腾讯 CodeBuddy 桌面版）**

```bash
# 方式一：安装 Skill（推荐）
# 1. 将 skills/offerpilot/ 文件夹复制到 WorkBuddy 技能目录
#    Windows: C:\Users\你的用户名\WorkBuddy\Claw\skills\offerpilot\
# 2. 在 WorkBuddy 中执行 /reload offerpilot
# 3. 直接对话即可触发
> 帮我准备面试

# 方式二：全局规则
# 在 C:\Users\你的用户名\.workbuddy\USER.md 中添加规则
# 将 INTERVIEW.md 的内容复制到 USER.md 中
> 帮我准备字节C++后端实习面试
```

**ChatGPT / Claude 网页版（零配置）**

1. 打开 `references/` 下对应岗位的文件（如 `java-backend.md`）
2. 全选复制文件内容
3. 粘贴到 ChatGPT/Claude 对话框
4. 输入："基于以上知识库，帮我准备Java后端面试"

> 可同时粘贴多个文件获得更完整输出：岗位文件 + handwrite.md + project-packaging.md

**GitHub Copilot / Aider**

```bash
# Copilot: code OfferPilot -> @workspace 对话
# Aider: aider --read references/java-backend.md
```

**Windsurf**

```bash
# 用 Windsurf 打开项目文件夹
# .cursorrules 兼容，直接对话即可
```

**Continue (VS Code 插件)**

```bash
code OfferPilot
# Continue 自动读取 .clinerules 或项目配置
# 在 Continue 面板中直接对话
```

**Roo Code (VS Code 插件)**

```bash
code OfferPilot
# 在 Roo Code 面板中配置自定义指令指向 INTERVIEW.md
# 直接对话即可
```

**Augment**

```bash
# 用 Augment 打开项目文件夹
# 自动读取项目配置文件
```

**Codex CLI**

```bash
cd OfferPilot
codex "帮我准备字节Java后端实习面试"
```

### 3. 开始使用

| 你说的 | AI 自动做什么 |
|--------|-------------|
| "帮我准备字节C++后端实习面试" | 加载 cpp-backend.md -> 按字节特色排序 -> 输出八股文+手撕题+项目建议 |
| "帮我模拟面试" | 进入模拟面试模式 -> 逐题出题 -> 逐题点评 -> 面试报告 |
| "分析我的项目 /path/to/project" | 读取项目代码 -> 识别技术栈 -> 生成针对性面试题 |
| "还有2周面试，帮我规划" | 生成个性化备考计划（每日安排+重点分配） |
| "我网络比较弱，专项训练" | 难度递进出题 -> 点评+变体题 -> 掌握度评估 |
| "展开讲讲 HashMap" | 基于知识库展开详细回答框架 |

---

## 5种功能模式

| 模式 | 触发方式 | 功能 |
|------|----------|------|
| 八股文生成 | "帮我准备XX面试" | 生成针对性八股文清单+手撕题+项目建议 |
| 模拟面试 | "帮我模拟面试" | 逐题出题+点评+面试报告（4种面试模式） |
| 项目分析 | "分析我的项目 /path" | 自动识别技术栈+生成针对性面试题+包装建议 |
| 备考计划 | "还有2周面试，帮我规划" | 个性化学习路线（1周/2周/1月/3月模板） |
| 专项突破 | "我网络比较弱" | 难度递进训练+变体题+掌握度评估 |

---

## 支持岗位

| 岗位 | 核心技能 | 知识库 |
|------|----------|--------|
| C++后端 | STL、网络编程、操作系统 | cpp-backend.md |
| Java后端 | JVM、Spring、MySQL | java-backend.md |
| 前端 | JS/TS、React/Vue、浏览器 | frontend.md |
| AI算法 | 机器学习、深度学习、NLP/CV | ai-algorithm.md |
| 测试开发 | 测试框架、自动化、CI/CD | test-dev.md |
| 嵌入式 | C语言、单片机、RTOS | embedded.md |
| 移动端 | Android/iOS、跨平台 | mobile.md |
| 数据分析 | SQL、Python、统计学 | data-analysis.md |
| 云计算运维 | Linux、容器、K8s | cloud-ops.md |
| AI Agent开发 | Agent框架、RAG、MCP、Multi-Agent | ai-agent.md |
| AIGC/大模型应用 | Prompt工程、RAG、微调、模型部署 | aigc.md |
| MLOps/ML工程 | 特征平台、模型部署、监控、训练工程 | mlops.md |

通用参考：handwrite.md（手撕代码） · project-packaging.md（项目包装） · mock-interview.md（模拟面试） · project-analysis.md（项目分析） · study-plan.md（备考计划）

---

## 核心架构

```
+--------------------------------------------------+
|                   平台适配层                       |
|                                                   |
|   INTERVIEW.md  .cursorrules                      |
|   CLAUDE.md     .clinerules                       |
|   .trae/rules/  .lingma/rules/  .codegenie/       |
|                                                   |
|   系统配置 + 触发规则 + 岗位映射                    |
|   + 输出格式 + 异常处理                            |
+-------------------------+------------------------+
                          |
                          | AI 按需读取
                          v
+--------------------------------------------------+
|              核心知识库 (纯 Markdown)              |
|                                                   |
|   references/                                     |
|   +-- cpp-backend.md       C++ 后端   ~30 考点    |
|   +-- java-backend.md      Java 后端  ~35 考点    |
|   +-- frontend.md          前端      ~25 考点     |
|   +-- ai-algorithm.md      AI 算法   ~27 考点     |
|   +-- test-dev.md          测试开发  ~27 考点     |
|   +-- embedded.md          嵌入式    ~25 考点     |
|   +-- mobile.md            移动端    ~24 考点     |
|   +-- data-analysis.md     数据分析  ~26 考点     |
|   +-- cloud-ops.md         云计算    ~30 考点     |
|   +-- ai-agent.md         AI Agent  ~40 考点     |
|   +-- aigc.md             AIGC      ~30 考点     |
|   +-- mlops.md            MLOps     ~30 考点     |
|   +-- handwrite.md         手撕代码              |
|   +-- project-packaging.md 项目包装              |
|   +-- mock-interview.md    模拟面试              |
|   +-- project-analysis.md  项目分析              |
|   +-- study-plan.md        备考计划              |
+--------------------------------------------------+
```

**设计原则**：知识库与平台解耦。新增平台只需加一个适配文件，核心知识库零改动。

**每个考点包含**：频率标注 | 核心要点 | 面试问法 | 回答框架 | 延伸考点

---

## 公司特色考点

支持**任意公司**，不限于下表：
- 已知公司 -> 按特色表调整考点侧重
- 未知公司 -> 根据行业/规模/技术栈**智能推断**面试风格
- 未提公司 -> 按通用标准输出，不强制选择

**已知公司特色：**

| 公司 | 手撕特点 | 八股重点 | 项目追问 |
|------|----------|----------|----------|
| 字节 | 题量大、难度高、2-3道 | C++深挖、网络、算法 | 极细追问 |
| 腾讯 | 中等难度、偏基础 | 网络、OS、底层原理 | 重理解深度 |
| 阿里 | 偏业务场景 | Java八股、业务理解 | 重业务思考 |
| 百度 | 算法为主 | 算法、技术细节 | 重原理 |
| 美团 | 中等、偏实际 | Java、分布式 | 场景题多 |
| 快手 | C++底层题多 | C++、音视频 | 中等 |
| 小红书 | Java、高并发 | Java、高并发 | 重业务场景 |
| 华为 | 通信协议、嵌入式 | 通信、嵌入式 | 综合素质 |
| 网易 | 中等、偏游戏/音视频 | C++、游戏 | 中等 |
| 京东 | Java、偏业务 | Java、分布式 | 重业务 |
| 滴滴 | 中等、偏实际 | Go/Java、高并发 | 场景题 |
| 米哈游 | C++、游戏方向 | C++、图形学 | 重游戏技术 |
| 大疆 | 嵌入式、C++ | 嵌入式、图像处理 | 重工程能力 |
| 更多... | 持续更新中 | | |

**未知公司智能推断维度：**

| 推断维度 | 推断逻辑 |
|----------|----------|
| 公司规模 | 大厂(万人+) -> 手撕难度高、八股深；中小厂 -> 偏实际项目经验 |
| 行业领域 | 互联网 -> 高并发/分布式；AI -> 算法推导；游戏 -> C++/图形学；金融 -> 安全/一致性 |
| 技术栈 | Java为主 -> Spring/中间件；Go -> 并发/微服务；Python -> ML/DL |
| 公司阶段 | 创业公司 -> 全栈/落地能力；成熟公司 -> 深度专精/系统设计 |

---

## 使用示例

**C++ 后端 · 字节 · 实习**

> "帮我准备字节 C++ 后端实习面试"

输出：C++ 八股文（按字节特色排序）-> 手撕高频题 -> 项目包装建议 -> 字节面试特色提醒

**AI 算法 · 阿里 · 校招**

> "我想投阿里的算法岗"

输出：AI 算法八股 -> 算法题 -> 推荐方向 -> 阿里面试特色提醒

**模拟面试 · 字节 · C++后端**

> "帮我模拟面试，我准备字节C++后端"

输出：逐题出题 -> 每题点评（亮点+不足+建议）-> 面试报告（总评+各维度评分+Top3提升建议）

**项目分析 · 用户项目**

> "分析我的项目 /path/to/my-project"

输出：自动识别技术栈 -> 项目分析报告 -> 3个亮点 -> 5个追问预判 -> 10题针对性面试题

**备考计划 · 2周冲刺**

> "还有2周面试，帮我规划Java后端备考"

输出：2周标准计划 -> 每日学习安排 -> 按岗位调整重点分配 -> 推荐复习顺序

**专项突破 · 薄弱点**

> "我计算机网络比较弱，专项训练"

输出：L1概念->L2原理->L3应用->L4对比->L5设计 难度递进 -> 每题点评+变体题 -> 掌握度评估

---

## 项目追问套路

所有岗位通用——面试官最爱问的叙述方式：

```
X "我用了 XX 技术"
V "我遇到了 XX 问题，选择了 XX 方案，对比了 XX 的优劣，最终效果是 XX"

核心原则：说原因 · 说对比 · 说效果
```

---

## 与其他项目对比

| 对比项 | OfferPilot | 静态八股仓库 | 刷题 App |
|--------|-----------|-------------|----------|
| 形式 | AI 驱动知识库 | 静态 Markdown | App / Web |
| 个性化 | 岗位+公司+阶段 | 无 | 有限 |
| 岗位覆盖 | 9 大岗位 | 通常 1-2 个 | 有限 |
| 考点深度 | 频率+问法+回答框架 | 关键词罗列 | 有限 |
| 手撕代码 | 按岗位/公司分级+解题思路 | 通常无 | 有但无分级 |
| 项目指导 | STAR法则+追问预判 | 通常无 | 通常无 |
| 模拟面试 | YES - 4种模式+面试报告 | NO | 有限 |
| 项目分析 | YES - 自动识别技术栈 | NO | NO |
| 备考计划 | YES - 个性化学习路线 | NO | 有限 |
| 专项突破 | YES - 难度递进+掌握度评估 | NO | 有限 |
| 多平台 | YES | YES - 自己翻 | NO |
| 离线使用 | YES | YES | NO |

**本质区别**：其他项目是「知识库」（你自己翻），OfferPilot 是「AI 可读的知识库」（AI 帮你用）。

---

## 常见问题

**Q：什么是 Skill？跟普通知识库有什么区别？**

Skill 是 AI 可读的能力包。普通知识库是你自己翻阅的资料，Skill 是让 AI 自动读取并按规则执行的结构化文件。区别在于：普通知识库是「人读的」，Skill 是「AI 读的」——AI 读取后会按预设的岗位映射、输出格式、公司特色来回答，而不是泛泛而谈。

**Q：必须用特定工具才能用吗？**

不是。核心知识库是纯 Markdown，配合 Cursor、Claude Code、Cline、Trae 等工具的规则文件都能使用，直接粘贴到 ChatGPT 对话框也行。

**Q：跟直接问 ChatGPT 有什么区别？**

直接问是「一次性泛泛回答」，结构化知识库让输出一致、完整、有针对性——相当于给 AI 装上了面试专家的知识体系。

**Q：内容准确吗？**

基于真实面经历年整理，建议交叉验证。发现错误欢迎提 Issue 或 PR。

**Q：模拟面试是怎么工作的？**

AI 会像真实面试官一样逐题出题，你回答后逐题点评（亮点+不足+建议），最后输出面试报告（总评+各维度评分+提升建议）。支持4种模式：标准模拟、基于项目定制、专项突破、限时手撕。

**Q：项目分析支持哪些技术栈？**

自动识别 package.json / pom.xml / CMakeLists.txt / requirements.txt / go.mod / Dockerfile 等标识文件，覆盖前端、Java、C++、Python/AI、Go、云运维、移动端等主流技术栈。

**Q：备考计划是怎么生成的？**

根据你的可用时间（1周/2周/1月/3月）选择模板，按岗位调整重点分配，如果有薄弱点会调整学习顺序。

---

## 贡献指南

### 可以贡献什么

- **补充八股文** — 在对应 `references/*.md` 中添加新考点
- **新增岗位** — 创建 `references/<name>.md`，更新 `INTERVIEW.md` 和 `README.md`
- **新增平台适配** — 创建对应平台的适配文件，更新 `README.md`
- **补充公司考点** — 在 `INTERVIEW.md` 的公司特色表中添加新公司
- **补充手撕题** — 在 `references/handwrite.md` 中按岗位分类添加
- **补充模拟面试题** — 在 `references/mock-interview.md` 中补充公司面试风格
- **补充备考经验** — 在 `references/study-plan.md` 中分享备考时间线

### 提交流程

```bash
1. Fork 本仓库
2. git checkout -b feature/<name>
3. git commit -m 'Add <description>'
4. git push origin feature/<name>
5. 提交 Pull Request
```

### 考点格式规范

```markdown
### 考点名称 [频率: 5星]

**核心要点**：一句话概括

**常见面试问法**：
- "请说说XX的原理"
- "XX和YY有什么区别？"

**回答框架**：
1. 先说结论/定义
2. 展开原理
3. 对比相关概念
4. 举例应用场景

**延伸考点**：关联考点1 -> 关联考点2
```

---

## 项目结构

```
OfferPilot/
+-- INTERVIEW.md              # 系统配置（核心流程+岗位映射+公司特色）
+-- CLAUDE.md                 # Claude Code 适配
+-- .cursorrules              # Cursor 适配
+-- .clinerules               # Cline 适配
+-- .trae/rules/              # Trae IDE / Trae 插件适配
+-- .lingma/rules/            # 通义灵码适配
+-- .codegenie/               # DevEco CodeGenie 适配
+-- skills/offerpilot/        # WorkBuddy Skill 适配
+-- LICENSE                   # MIT 许可证
+-- .gitignore
+-- .github/
|   +-- ISSUE_TEMPLATE/
|   |   +-- content.md        # 内容补充/错误 Issue 模板
|   |   +-- feature.md        # 功能建议 Issue 模板
|   +-- PULL_REQUEST_TEMPLATE.md
+-- references/               # 核心知识库
|   +-- cpp-backend.md        # C++ 后端
|   +-- java-backend.md       # Java 后端
|   +-- frontend.md           # 前端
|   +-- ai-algorithm.md       # AI 算法
|   +-- test-dev.md           # 测试开发
|   +-- embedded.md           # 嵌入式
|   +-- mobile.md             # 移动端
|   +-- data-analysis.md      # 数据分析
|   +-- cloud-ops.md          # 云计算运维
|   +-- ai-agent.md           # AI Agent 开发
|   +-- aigc.md               # AIGC/大模型应用
|   +-- mlops.md              # MLOps/ML工程
|   +-- handwrite.md          # 手撕代码（通用）
|   +-- project-packaging.md  # 项目包装（通用）
|   +-- mock-interview.md     # 模拟面试系统
|   +-- project-analysis.md   # 项目分析指南
|   +-- study-plan.md         # 个性化备考计划
+-- README.md
```

---

## License

[MIT](LICENSE)

---

<div align="center">

如果 OfferPilot 帮助你拿到了 Offer，给个 Star 吧!

[报告 Bug](https://github.com/ywx914705/OfferPilot/issues) · [提 Feature](https://github.com/ywx914705/OfferPilot/issues) · [提交 PR](https://github.com/ywx914705/OfferPilot/pulls)

</div>
