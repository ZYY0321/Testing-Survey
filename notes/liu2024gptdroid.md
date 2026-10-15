# GPTDroid：用功能记忆引导 LLM 探索 Android GUI

> 这篇主要解决**测试往哪里走**，不是**怎样判断所有功能是否正确**。模型输入是控件树文字，不是截图；实验中的 bug 全是 crash。

术语：[控件树](../glossary.md#view-hierarchy) · [Activity](../glossary.md#activity) · [覆盖率](../glossary.md#coverage) · [Oracle](../glossary.md#oracle) · [重复运行](../glossary.md#repetition)

## 一页读懂

| 问题 | 论文的回答 |
|---|---|
| 旧方法哪里不够 | 随机/模型方法不理解控件语义；学习方法依赖训练数据。即使接入 LLM，只看当前页和最近历史也容易在局部循环，难保持“正在测试哪个功能”的全局目标 |
| GPTDroid 新增什么 | 把 Android GUI 测试组织成连续问答，并加入**功能感知记忆**：记录已探索功能、页面/控件访问和最近操作 |
| 模型看见什么 | Manifest 中的应用/Activity 名称，以及 UIAutomator 控件树中的文字、ID、类型、位置和可点击属性；**没有截图** |
| 模型做什么 | gpt-3.5-turbo 输出点击、双击、长按、滚动、文本输入等命令；系统解析后用 ADB 执行，再把新页面或错误反馈给模型 |
| 怎样判效果 | Activity/code coverage、触发历史 crash、新 crash、消融实验；没有非崩溃功能 oracle 的 Precision/Recall |
| 主要结果 | 93 个应用上 Activity coverage 0.75、code coverage 0.66；历史 crash 95 个。另测 223 个应用，报告 53 个新 crash，其中 20 修复、15 确认、18 待回应 |

## 研究问题：为什么“会聊天”仍不足以测试 GUI

作者把已有困难分成两层：

1. **Local dilemma（局部困境）**：模型上下文有限，容易只根据最近页面做决定，忘记较早访问过什么，于是在局部反复点击。
2. **Low-level dilemma（低层困境）**：Activity 名称、按钮文字和点击次数是低层信息；测试员真正关心的是“正在新增收入”“是否完成删除设备”等功能级进度。

因此，GPTDroid 不只要求 LLM 选动作，还在每轮追问“当前在测试什么功能、完成了吗”，把答案连同实际访问记录写进下一轮上下文。这里的“完成”是**探索规划信号**，不是对功能正确性的证明。

## 作者声称的贡献：按原文四项逐项解释

Section 1 末尾明确列出四项，标题分别为 **Vision、Technique、Evaluation、Insight**：

| 原文项目 | 准确含义 | 本仓库怎样分类 |
|---|---|---|
| Vision | 将自动 GUI 测试表述为 LLM 与应用之间的交互式问答，让模型根据 GUI 语义推断下一步 | 问题建模，服务 generate/execute |
| Technique | 提出 GPTDroid，并以功能感知记忆让模型同时关注全局探索进度和功能语义 | 核心方法贡献 |
| Evaluation | 在真实应用上评价覆盖、历史 crash 和新 crash | 方法评价，不等于提出 benchmark |
| Insight | 从案例中归纳长操作链、功能优先、有效文本和复合操作四种行为 | 定性分析，不是四个独立准确率实验 |

原文对第一项的表述是：

> “The first work to formulate the automatic GUI testing problem to an interactive question & answering task.”
> — Section 1，v1 PDF p.2

因此本笔记将主要贡献类型记为 **method**。论文使用 Themis 和自建应用集合，但没有把新 benchmark 或通用 harness 作为贡献。

## 方法：一轮测试怎样运行

### 1. 观察：把 GUI 结构转换成自然语言

GPTDroid 从两处取信息：

- **AndroidManifest.xml**：应用名和声明的 Activity 名称，提供应用级线索。
- **UIAutomator view hierarchy**：当前 Activity 和控件节点。控件优先取 text、hint-text、resource-id 中的可读值，同时保留 class、clickable 和粗位置；匿名控件可借父/兄弟节点文字补语义。

resource-id 里的下划线和 CamelCase 会被拆成词，例如把 saveTaskButton 变成较可读的单词序列。这是文本预处理，不是训练控件检测模型。它看不见颜色、遮挡、错位、自绘 Canvas 等真实渲染现象。

### 2. 提问：六类信息组成三种 prompt

作者让 5 位标注者编写并试用候选提示，再通过卡片分类和讨论归纳出 6 类模式：

| 模式 | 作用 |
|---|---|
| App information | 告诉模型应用名和可能页面 |
| Page information | 告诉模型当前 Activity 和页面概况 |
| Widget information | 提供控件文字、类型、位置和可点击性 |
| General operation inquiry | 询问点击、长按、滚动等一般动作 |
| Text input inquiry | 页面有输入框时，要求给出一个或多个输入及提交动作 |
| Testing feedback | 目标不存在或回答不可执行时，重新给当前可选项并要求修正 |

它们组合成 Start Prompt（启动一次）、Test Prompt（正常迭代）和 Feedback Prompt（执行失败后纠正）。反馈处理的是“模型选了不存在的控件”等执行错误，不能自动判断应用存在 bug。

### 3. 决策与执行：从回答到设备动作

模型输出被限制为 5 类操作：click、double click、long press、scroll、input。输入场景允许多个“控件—文本”对，再附提交动作。系统解析回答并调用 ADB 执行；若目标无法匹配，则不把它计为应用缺陷，而是进入反馈提示。

整体闭环为：

> Manifest/控件树 → 文字 prompt → LLM 动作 → 解析/ADB 执行 → 新控件树或错误反馈 → 下一轮

### 4. 功能感知记忆：保存什么，怎样影响下一步

记忆并非完整聊天记录，而是三类结构化摘要：

| 记忆内容 | 怎样得到 | 下一轮有什么用 |
|---|---|---|
| 已探索功能及状态 | 让 LLM概括当前功能与是否完成 | 避免一直测同一功能，尝试未覆盖功能 |
| Activity/控件访问 | 执行器按 ActivityName、text/resource-id 等更新访问记录 | 识别重复页面和反复点击 |
| 最近 k 步细节 | 默认 k=5，保留近期页面、控件访问和离开动作 | 维持当前多步流程的局部连续性 |

初始功能线索来自应用描述和 Activity 名称，随后由模型继续细化。若模型把功能名概括错，记忆也可能随之偏离；论文没有单独评价“功能状态标注”的准确率。

## 论文案例：四种“像测试员”的行为具体是什么

这些是 Section 5 的定性案例，用于解释为什么覆盖和 crash 数可能提高，不是四个独立量化任务。

| 观察 | 原文案例 | 能支持的结论 | 不能扩大成什么 |
|---|---|---|---|
| 长而有意义的操作链 | SmartMeter 删除设备前，要搜索并添加设备、填写信息、启动、关闭，再长按删除 | 功能记忆有助于保持前置步骤和跨页路径 | 不能证明所有长业务流程都能完成 |
| 功能优先 | Moni 中优先进入新增收入，而部分基线困在设置循环 | 语义信息可能改变探索顺序 | 不是经过真值验证的“功能重要性排序器” |
| 有效文本输入 | 根据字段含义生成日期、地点等较合理内容 | 语义输入更容易通过表单约束、到达后续页面 | 没有覆盖系统化负例或所有业务规则 |
| 复合动作 | 健身记录需要名称、日期、组数、次数与提交 | 基本动作可组合成多字段流程 | 不表示支持任意拖拽、多指或复杂外设动作 |

## 实验：三个应用集合不要混

| 用途 | 数据 | 真值/任务 |
|---|---|---|
| Prompt 设计 | 101 个被效果实验筛掉、但 GPTDroid 可运行的应用 | 人工编写和整理提示模式；不计入主效果 |
| Effectiveness | Themis 20 个应用/34 个历史 crash + Google Play 73 个应用/109 个历史 crash，共 93/143 | 比覆盖、触发历史 crash、运行时间和消融 |
| Usefulness | 317 个候选筛到 223 个应用 | 搜索并提交未报告 crash；可与 93 应用集合有重叠 |

效果实验环境为 Ubuntu 18.04、Android 7.1 x86 模拟器、2GB RAM。每个工具在每个应用运行 60 分钟，并运行 3 次。原文写明：

> “We run each tool three times”
> — Section 3.1，v1 PDF p.7

紧接着原文说明取三次中的最高表现。因此表里的结果不是三次均值，也不能表示一次运行的稳定性。登录、外部文件和账号由实验脚本预先准备，不能说工具独立解决了这些前置条件。

### 比较对象

10 个核心工具为 Monkey、DroidBot、TimeMachine、WCTester、Stoat、Ape、Fastbot、ComboDroid、Humanoid、Q-testing；另有 5 个增强组合：DroidBot+QTypist、Ape+QTypist、Stoat+Toller、WCTester+Vet、Ape+Vet。后五项是“基础工具 + 输入/环境/避陷增强”，不是五个完全独立算法。

### 主结果与正确读法

| 指标 | GPTDroid | 最强对照 | 结论边界 |
|---|---:|---:|---|
| 平均 Activity coverage | 0.75 | 0.57，Ape+QTypist | +18 个百分点；作者按相对增幅写约 +32% |
| 平均 code coverage | 0.66 | 0.55，Ape+QTypist | +11 个百分点，相对 +20% |
| 覆盖控件数 | 5,243 | 3,811，TimeMachine | 数量最多的基线与覆盖率最高基线不是同一个 |
| 覆盖 Activity 数 | 1,049 | 853，Ape+QTypist | 全部声明 Activity 为 1,398；Activity 不等于完整 GUI 状态 |
| 触发历史 crash | 95 | 66 | 原始计数明确；正文“多 31%”与常用基线相对增幅不一致 |

95 与 66 的差是 29；以 66 为分母约为 43.9%，以 95 为分母约为 30.5%。因此本仓库保留作者“31%”原说法的口径疑点，引用时优先报告原始计数。

### 新 crash

223 个应用中共触发 135 个 crash，涉及 115 个应用；其中 53 个是新报告，涉及 41 个应用：

- 20 个已修复（fixed）
- 15 个获开发者确认（confirmed）
- 18 个待回应（pending）

摘要中的“35 confirmed and fixed”是 20+15 的合计，不能翻成“35 个都已经修复”。真实性比较只选 TimeMachine、Stoat+Toller、Humanoid 三个基线；作者称它们合计检出这 53 个新 bug 中的 9 个。

### 消融

| 配置 | Activity / code coverage | 它检验什么 |
|---|---:|---|
| 完整 GPTDroid | 0.75 / 0.66 | 基准 |
| 无 GUI context prompt | 0.17 / 0.14 | 将整理后的上下文换成更原始层级/名称后，覆盖显著下降 |
| 无功能感知记忆 | 0.34 / 0.28 | 缺少全局功能与近期历史后，探索明显退化 |

消融说明这两个设计在当前系统中有用。由于替换同时改变信息格式和信息量，不能把下降精确归因到某一个字段；k=5 也是该实验设置下的选择，不是普遍最优。

## 局限

### 论文明确讨论

- 控件 text/resource-id 没有可读语义时，LLM 难以理解。
- 需要复杂外部数据或网络连接的场景仍困难。
- 动作集合无法覆盖所有复杂手势。
- 长交互会让模型遗忘早期信息，这也是设计记忆模块的直接动机。

### 根据实验能进一步看到

- **Oracle 很弱**：缺陷终点是 crash。功能记忆中的“YES/完成”服务探索，并非独立需求判定。
- **感知范围有限**：只读控件树，无法检查真实截图中的遮挡、错位、颜色或 Canvas 内容。
- **泛化边界明显**：Android 7.1 x86、过滤后的应用和人工准备环境，不能直接外推到现代 Android、iOS、Web。
- **稳定性未充分报告**：三次取最好值会高于一次运行的典型体验，论文没有给均值、方差或模型采样细节。
- **成本与复现信息不全**：未完整记录模型快照、token、费用和延迟。

## 对 GUI testing 调研的价值

GPTDroid 最适合放在“测试生成/探索”主线，而不是 oracle 主线。它提供了一个清楚的分界：

- **GUI Agent 能走流程**：模型能理解当前页面并连续操作。
- **GUI 测试能发现错误**：还需要独立预期、失败证据和缺陷真值。

后续阅读应追问三件事：新工作是否加入截图/代码等额外信息；是否把探索轨迹变成含断言的可重放测试；是否用非崩溃缺陷的 Precision/Recall，而不只用覆盖与 crash。

## 所读版本与核对范围

- 完整阅读 arXiv 2310.15780v1（2023-10-24，共 12 页）Sections 1–7。
- 重新视觉核对 v1 PDF p.2 的贡献列表、p.7 的重复协议、p.8 的 Tables 6–7，并核对 Section 4–5 的新缺陷与案例。
- 正式身份通过 [ACM DOI](https://doi.org/10.1145/3597503.3639180) 与 [ICSE 2024 Research Track 页面](https://conf.researchr.org/details/icse-2024/icse-2024-research-track/180/Make-LLM-a-Testing-Expert-Bringing-Human-like-Interaction-to-Mobile-GUI-Testing-via-) 确认。正式 PDF 未取得，方法和数字以 v1 为准。
- 未运行代码、未复现应用、未逐一核验开发者 issue，也未独立阅读全文中的全部基线。

## 书目与分类

<details>
<summary>展开元数据</summary>

- title: Make LLM a Testing Expert: Bringing Human-like Interaction to Mobile GUI Testing via Functionality-aware Decisions
- authors: Zhe Liu; Chunyang Chen; Junjie Wang; Mengzhuo Chen; Boyu Wu; Xing Che; Dandan Wang; Qing Wang
- year: 2024
- era: llm
- venue: ICSE 2024 Research Track
- venue_type: conference
- url: https://doi.org/10.1145/3597503.3639180
- pdf_url: https://arxiv.org/pdf/2310.15780v1
- version: arXiv v1, 2023-10-24；正式版本书目已核实，正文差异未核对
- code_url:
- institution: Institute of Software, Chinese Academy of Sciences
- aka: GPTDroid
- door: gui
- question: generate execute
- contribution_type: method
- platform: android
- method: llm-prompt
- oracle_type: crash
- bug_type: crash
- benchmark: Themis 20 apps/34 crashes + Google Play 73 apps/109 crashes；新缺陷验证 223 apps
- baselines: 10 个核心工具 + 5 个 QTypist/Toller/Vet 增强配置
- headline_result: Activity coverage 0.75、code coverage 0.66；历史 crash 95；53 个新 crash 中 20 修复、15 确认、18 待回应
- one_liner: 将控件树文字、执行反馈和功能探索记忆交给 LLM，连续生成 Android 操作，提高覆盖与 crash 触发。
- use_in_survey: LLM GUI 探索代表；用于区分探索能力与非崩溃 oracle
- concepts: generate execute gui-exploration coverage crash oracle
- priority: must
- read_depth: full
- owner: Codex
- status: extracted

</details>
