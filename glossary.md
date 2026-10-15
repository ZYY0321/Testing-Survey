# GUI testing 术语表

这些解释服务于 GUI 探索、执行、判定、维护、评测及文献组织。先用 [field.md](field.md) 理解领域，再遇词查本页。教学例子与论文结果分开；术语定义不能代替论文效果证据。

**第一次只需要：** [layout](#layout)、[控件树](#view-hierarchy)、[Activity](#activity)、[状态图](#state-graphs)、[覆盖率](#coverage)、[oracle/真值](#truth)、[baseline/消融](#baseline)。其余按笔记出现的位置查阅。

<a id="layout"></a>

## Layout / layout file / runtime layout：布局、布局文件、运行时布局

Layout 首先指界面元素的组织和摆放，例如标题在上、输入框居中、保存按钮在下。GUI 测试论文说 layout-based，通常关注的是能被工具读取的结构信息，而不只是人眼觉得“布局好不好看”。

Android 的传统 View 系统可以用 XML 声明控件，也可在运行时创建。运行时 view hierarchy 反映当时控件和属性，**不是静态 XML 文件的逐字副本**。不能把论文里的所有 layout 都固定解释成一个源码文件。

自编例子：结构里写着“保存按钮、可点击、位于右下角”，但实际被弹窗遮挡；又或者 Canvas 上画了图标，工具读树时只看到外层画布。结构与截图便可能不一致。这正是 Yu 综述引言要解释的问题。

出处：[Android 官方布局说明](https://developer.android.com/develop/ui/views/layout/declaring-layout)（说明限于该页面的 View 系统）；[Yu 笔记](notes/yu2025guisurvey.md) 引言。

<a id="gui"></a>

## GUI / UI / UX：图形用户界面、用户界面、用户体验

GUI 强调通过图形控件交互；UI 是更宽的“用户界面”；UX 关注用户使用时的体验。GUI 测试可以检查功能、显示或可访问性，UX 问题只是可能涉及的一部分。验证删除后数据不恢复是 GUI 功能测试；研究用户是否喜欢某按钮颜色，未必提供软件缺陷检测证据。

<a id="widget"></a>

## Widget / element / component：控件、界面元素、组件

按钮、文本框、列表项等通常称 widget 或 element。Component 在不同论文里可能指 UI 组件，也可能指软件模块，不能脱离上下文固定翻译。

“元素检测”问有哪些元素、在哪里；“元素定位”问这一步应操作哪一个；“功能判定”问操作后的结果正确吗。三者的评价目标不同。

<a id="view-hierarchy"></a>

## View hierarchy / UI tree / XML：视图层级、控件树、结构化表示

把容器和控件按父子关系组织成树。XML 是表示这些节点与属性的一种格式，不是测试策略。

例如：窗口 → 表单容器 → 标题输入框、保存按钮。工具能取节点文本、类型、ID、边界等属性；父容器和相邻元素也提供语义线索。树可能漏掉自绘细节；能找到节点不保证它此时可见或可点击。

出处：[Android 布局说明](https://developer.android.com/develop/ui/views/layout/declaring-layout)；GPTDroid §2.1。

<a id="dom"></a>

## DOM / HTML：文档对象模型、网页标记

HTML 描述网页内容结构；浏览器把文档表示成可操作的对象树，称 DOM。Web GUI 工具可以读取或查询 DOM 找目标。

DOM 与 Android 控件树属于不同平台的结构信息，不能说是同一种文件格式。DOM 里有一个按钮也不意味着它已加载完成、在屏幕内或没有被遮挡。

出处：[MDN DOM 说明](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)。GPTDroid 是 Android 方法，不能把它写成 DOM 驱动的 Web 工具。

<a id="accessibility-tree"></a>

## Accessibility tree / accessibility：可访问性树、可访问性

可访问性树是供辅助技术理解界面的一种语义表示，例如按钮名称、角色和状态。它与完整 DOM/渲染结构不完全相同，具体可获得信息由平台决定。

Accessibility testing 检查辅助访问是否可用，例如读屏能否识别图标按钮、放大字体后还能否操作。不是网页“可被网络访问”的意思。依据：Yu §5.2；本仓库未复现具体平台 API。

<a id="image"></a>

## Screenshot / vision-based / multimodal：截图、视觉方法、多模态

截图是某时刻实际渲染的图像。Vision-based 表示利用图像/视觉信息，不限定使用大模型。Multimodal 指结合多种信息，例如文字与截图，或结构与图像。

GPTDroid v1 把界面结构转成文字输入 LLM；虽然属于 GUI 测试，也不能因此叫截图 VLM 方法。Yu 则覆盖传统视觉与学习视觉路线。

<a id="canvas"></a>

## Canvas / rendering / occlusion：画布、自绘渲染、遮挡

Canvas 可用于直接绘制图形，画出来的每个对象不一定对应独立可读控件节点。Rendering 是把结构和绘制过程变成实际画面；occlusion 是某内容被别的内容挡住。

例子：两个按钮在结构中各自存在，渲染后一个覆盖另一个。检查结构存在不能独自判定显示正确。出处：Yu 引言与 §6.1。

<a id="ocr"></a>

## OCR / CV：光学字符识别、计算机视觉

OCR（Optical Character Recognition）从图像中识别文字；CV（Computer Vision）是处理和理解图像的技术领域。检测按钮位置、识别图标或发现文字重叠都可涉及 CV。

OCR 识别出了“删除”文字，仍要知道它属于哪个控件、是否可操作、删除是否真的生效。文字识别不是完整 oracle。出处：Yu 的视觉技术讨论。

<a id="activity"></a>

## Activity / Fragment / screen / page：Android 组件与界面页面

Activity 是 Android 提供与用户交互界面的应用组件。Screen/page 在测试论文中常指某个界面或工具抽象的页面，其定义要查原文。一个 Activity 可以承载多个状态，也可能通过内部组件切换内容；不能按“一个 Activity = 一张截图 = 一个功能”理解。

Fragment 是可复用的界面部分及相关行为，可在 Activity 等环境中组合；此处只解释它可能影响界面划分，不要求学习 Android 开发细节。GPTDroid 的核心统计对象是 Activity，不是完整状态集合。

出处：[Android Activity 说明](https://developer.android.com/guide/components/activities/intro-activities)、[Fragment 说明](https://developer.android.com/guide/fragments)；GPTDroid §3.1。Fragment 具体结构不作为本篇实验结论。

<a id="app-files"></a>

## APK / AndroidManifest.xml：应用安装包、应用声明信息

APK 是 Android 应用安装包的一种格式。AndroidManifest.xml 声明应用相关组件和配置等信息。GPTDroid 从中取得应用/Activity 信息，再配合当前界面结构；并非通过阅读全部业务源码知道真实需求。

“可取得 APK/manifest”不等于“拥有完整源码和正确业务规格”。出处：GPTDroid §2.1/§3.1。

<a id="locator"></a>

## Locator / resource-id / text / hint / bounds：定位器与控件属性

Locator 是查找目标元素的规则。resource-id 是控件标识；text 是显示/结构中的文字属性；hint 常是输入提示；bounds 描述元素区域。它们不保证在所有版本都稳定。

“任务列表中的删除”可能需要结合这些属性和邻近标题才能定位。GPTDroid 取文本、ID、类型及邻近信息组织上下文；若文本和 ID 没有意义，模型理解可能受阻。

<a id="grounding"></a>

## Grounding / bounding box：语言目标落到控件、目标框

Grounding 把“点击保存”对应到具体元素或位置。Bounding box 是包住目标的矩形区域。某评测以预测点击点落在正确框内作为成功，这是定位成功，**不是应用功能正确率**。

例子：模型点中保存按钮但应用没保存，定位指标可能成功，功能测试却应该失败。出处：Yu §5.1；Trong 数据集分析用于区别评测目标。

<a id="events"></a>

## Event / action / action space / gesture：事件、动作、动作空间、手势

Action 是执行器能做的操作；action space 是允许选择的操作及参数集合。Event 可指点击等用户事件，也可指系统变化。Gesture 指滑动、长按、拖动等手势。

GPTDroid v1 定义点击、双击、长按、滚动、输入。支持长按并不保证能执行任意拖动或多指手势。读论文要看允许什么动作，而不是只看“能操作 GUI”。

<a id="state-graphs"></a>
<a id="gui-state"></a>

## State / transition / STG / FSM / EFG：状态、转移和界面图模型

State 表示工具选取的当前条件；transition 表示动作带来的状态变化。STG 是 State Transition Graph（状态转移图）；FSM 是 Finite State Machine（有限状态机）；EFG 是 Event-Flow Graph（事件流图），关注事件可怎样衔接。它们相关，但不都是相同节点定义。

自编例子：空列表 —点击新增→ 编辑页 —保存→ 有任务列表。把这种关系建成图，可以规划未走过的路径。状态抽象得过粗会漏情况，过细会造成很大的图。出处：Yu §3.1；Kong 分类与 GUI 模型讨论。

<a id="ripping"></a>
<a id="gui-exploration"></a>

## GUI ripping / crawling / tarpit：界面抽取、遍历、探索陷阱

GUI ripping/crawling 通常通过运行与操作收集控件和可达页面信息，帮助建立模型；具体遍历策略看论文。Tarpit 指探索器容易卡住、反复做无效操作的界面区域，例如始终无法满足输入要求。

不是每个无法前进的页面都有软件缺陷：有时只是测试数据不满足合法条件。出处：Kong GUI 模型讨论；Yu 生成与探索效率讨论。

<a id="coverage"></a>
<a id="adequacy"></a>

## Coverage / activity coverage / code coverage：覆盖率及其不同对象

Coverage 的基本形式是“已覆盖目标 / 定义的目标总数”，关键是目标和分母。Activity coverage 看组件访问；code coverage 看代码执行；代码覆盖还分行、方法、分支等口径。

两个 Activity 都访问了，不保证空标题、删除持久化等场景都测过。100% 某种覆盖也不保证有强 oracle。GPTDroid 的 0.75 是其应用集合的平均 Activity 覆盖；不能读成“75% 的功能正确”。

<a id="black-box"></a>

## Black-box / white-box / grey-box：黑盒、白盒、灰盒

描述方法可利用的内部信息程度。黑盒侧重外部可观察行为；白盒使用内部实现信息；灰盒介于两者。每篇如何划分要看编码准则。

这个轴与“使用代码、布局、图像”的信息来源轴不同。布局数据是否被算内部信息取决于方法和论文定义，不能仅凭“截图”或“XML”机械判断。出处：Yu §2/Appendix A；Kong Figure 6。

<a id="generation-methods"></a>
<a id="generate"></a>

## Random / model-based / search-based / learning-based：随机、模型、搜索、学习路线

Random 随机抽动作；model-based 用显式界面/行为模型组织路径；search-based 根据目标搜索更好的输入/序列；learning-based 从数据或反馈学习策略。一个工具可结合几种路线。

这里 model 不一定是大语言模型，也可能只是状态图。Yu 的 system-based 是该综述的分类，用于组织若干系统性方案，不应当成与所有路线绝对互斥的通用标准。

<a id="learning"></a>

## ML / DL / RL / CNN：机器学习、深度学习、强化学习、卷积网络

ML 从数据学习规律；DL 使用深层神经网络；RL 通过动作与反馈学习策略；CNN 是常用于图像处理的一类网络。它们是方法词，不是 GUI 测试任务。

在 GUI 场景中，网络可检测控件，RL 可选择探索动作。奖励“访问新状态”会推动探索，但不会自动给出业务正确性判断。这里只解释它们在论文中的角色，不展开无关模型架构。

<a id="llm"></a>

## LLM / VLM / MLLM：语言模型与多模态模型

LLM（Large Language Model）处理和生成语言；VLM（Vision-Language Model）结合视觉与语言；MLLM 通常指 Multimodal Large Language Model，可处理多模态输入。具体可接受什么，仍要看论文所用模型。

纯文本结构也能让 LLM 辅助 GUI 测试；截图理解也可以通过较早视觉算法完成。GUI testing、vision-based 和 LLM-based 是不同概念。

<a id="prompt"></a>

## Prompt / zero-shot / few-shot / fine-tuning：提示、示例与训练调整

Prompt 是每次给模型的指令和上下文。Zero-shot 不附该任务的示例；few-shot 附少量示例帮助限定格式或行为。Fine-tuning 则调整模型参数，和只改提示不同。

GPTDroid v1 用手工构造提示和示例，并规定动作输出，不是在本方法中训练一个 GUI 模型。Prompt 设计仍含人工工作。

<a id="context"></a>

## Context / memory / functionality-aware：上下文、记忆、功能感知

Context 是当前决策可看到的信息；memory 是保存并重新使用历史的机制。不是“上次输出过”就保证模型下一轮记得，信息必须仍在输入或可检索记忆中。

Functionality-aware 指关注“正在做创建任务”等功能级进度，而不只记某按钮点过几次。GPTDroid 的功能完成标记服务于规划，不能当成独立功能正确性真值。

<a id="reasoning"></a>

## CoT / ReAct / RAG：推理组织与检索增强

CoT（Chain-of-Thought）用中间推理组织问题；ReAct 结合推理、行动与观察反馈；RAG（Retrieval-Augmented Generation）将检索到的外部信息加入模型输入。

GUI 测试可借此使用需求、旧测试或错误反馈，但机制名称不是正确性保证。GPTDroid 的主要记忆设计不能因为“有历史”就自动归成 RAG，也不能因为“迭代交互”就断言使用了正式 ReAct 模板。

<a id="agents"></a>

## Agent / multi-agent / planner / actor / observer：执行循环及角色分工

Agent 在这里是观察环境、决策、执行并利用反馈继续工作的一套系统。Planner 规划步骤，actor 执行动作，observer/validator 观察或复核；多 Agent 是多角色协作的一种架构。

角色可以由一个系统实现，也可分成多个模型调用。是否更有效要测，不能从“有多个 Agent”推出“更准确”，也不能认为 GUI 每个任务必须各用一个 Agent。

<a id="execution-tools"></a>
<a id="execute"></a>

## ADB / UIAutomator / emulator：Android 操作与运行环境

ADB（Android Debug Bridge）提供与 Android 设备通信的工具接口；UIAutomator 可帮助取得/操作界面；emulator 是模拟设备运行环境。它们是执行基础设施，不是 GPTDroid 的语义推理贡献。

GPTDroid 在 Android 7.1 x86 模拟器上评测，不能据此推断 iOS/Web 也获得同样结果。本仓库未复现工具安装和版本兼容性。

<a id="test-spec"></a>
<a id="scripted-testing"></a>

## Test case / scenario / script / precondition / postcondition：测试用例、场景、脚本、前后条件

Scenario 描述要检查的业务情形；test case 明确输入、前置条件、操作与预期；script 是可执行实现。不同论文术语可能部分重叠，须看实际产物。

Precondition 如“已有任务 A”；postcondition 如“删除后 A 不再存在”。把场景翻成脚本不能丢掉后条件。SRS（Software Requirements Specification）是软件需求规格说明，提供预期来源。

<a id="r-and-r"></a>
<a id="maintain"></a>

## Record & replay / migration / regression / repair：录制重放、迁移、回归、修复

Record 保存已有操作；replay 再执行；migration 将测试转到不同设备、平台、版本或应用；regression 检查变更没有破坏原行为。

Repair 可能指修测试脚本，也可能指修程序。仓库以 maintain 记脚本修复，以 repair 记被测程序修复，避免混淆。相同按钮匹配成功不证明原断言得到保持。出处：Yu §3.2/§4.1。

<a id="flaky"></a>

## Flaky test / asynchronous loading / timeout：不稳定测试、异步加载、超时

Flaky 指在没有应导致结果变化的条件下，测试却有时通过、有时失败，需查环境、时序等原因。Asynchronous loading 表示数据/画面可能稍后才就绪；timeout 是超出所设等待/运行时间。

“还没加载就点击”可能导致测试器失败，不能立即认定应用 bug。更长固定等待也不一定总能解决，论文要说明就绪条件和预算。

<a id="bugs"></a>
<a id="crash-bug"></a>
<a id="functional-bug"></a>
<a id="display-bug"></a>

## Crash / non-crash / functional / display：故障表现与判定目标

Crash 是异常终止；non-crash 是未异常终止，但行为可能错误。Functional 指违反功能要求；display 指显示/渲染问题。现实中这些维度会关联：遮挡可导致无法完成操作，功能错误也可导致 crash。

记录时看实验实际检测哪种终点。GPTDroid 的业务逻辑路径发现 crash，不能直接记为非崩溃功能检测。详见 [field](field.md)。

<a id="truth"></a>
<a id="oracle"></a>
<a id="implicit-oracle"></a>
<a id="assertion"></a>

## Oracle / assertion / ground truth / human verification：预期依据、断言、独立真值、人工核验

Oracle 提供预期/判定依据；assertion 是显式检查条件；ground truth 是评测时拿来比较的可信标签或结果；human verification 是人工核对。

系统的 oracle 输出与评测真值不是同一东西。若用模型自己的判断同时当预测和标准答案，无法独立证明判定准确。

<a id="metamorphic"></a>
<a id="differential"></a>
<a id="llm-judge"></a>

### Metamorphic / differential / LLM-as-a-judge：没有直接标准答案时怎样判

**Metamorphic testing（蜕变测试）**不要求先知道单次运行的完整正确输出，而是规定多次相关运行应满足的关系。例如将字号放大后，核心文本仍应完整可达；关系被破坏时形成缺陷线索。

**Differential testing（差异测试）**比较多个实现、版本、设备或等价状态在相同输入下的结果，显著差异需要进一步解释。

**LLM-as-a-judge** 让语言/多模态模型根据需求、截图或轨迹判断 pass/fail。它可以补语义能力，但必须用独立真值报告误报与漏报；模型理由本身不是真值。

<a id="benchmark"></a>

## Dataset / benchmark / harness / artifact：数据、评测、基础设施与产物

Dataset 是数据集合；benchmark 通常还说明任务、真值、指标和协议，方便比较；harness 是组织运行、收集与评价的基础设施；artifact 泛指公开代码、数据等研究产物。

使用 Themis 不等于提出 Themis；发布一个工具也不自动意味着主要贡献是通用 harness。看作者究竟新增并验证了什么。

<a id="baseline"></a>

## Baseline / ablation：比较方法、消融实验

Baseline 是和新方法比较的已有方法/配置；ablation 在新方法内移除或替换组件，看结果怎样变化。

GPTDroid 与 Ape+QTypist 的比较回答“整体比它强多少”；移除记忆的实验回答“记忆在当前设置下有没有贡献”。两者不同；同时改变信息格式和信息量时，难精确拆分各自因果作用。

<a id="metrics"></a>
<a id="eval"></a>

## Precision / recall / F1 / false positive / false negative：准确报告比例、检出比例和误报漏报

在缺陷检测任务中，precision 看报告为 bug 的项目里有多少真 bug；recall 看真实 bug 中检出了多少；F1 是两者的调和平均。误报是正常行为被说成 bug，漏报是真 bug 未检出。

自编例子：实际 10 个 bug，系统报告 8 个，其中 6 个真的有 bug，则 precision=6/8，recall=6/10。真值和计数单位必须先定义；不能直接把这套数字当作 GPTDroid 已报告指标。

<a id="success-rate"></a>

## Success rate / action matching / task completion：成功率、动作匹配、任务完成

“成功”取决于任务：点中正确目标、与人工操作一致、达到指定最终状态，都可能叫成功。它们支持执行能力，不必然支持缺陷检测能力。

例如正常应用上成功完成删除，不能证明在“假删除”的错误版本上能报告失败。要评测测试，需明确预期、错误实例和判定。

<a id="percentage"></a>

## Percentage point / relative improvement：百分点、相对提升

从 57% 到 75% 是增加 18 个百分点。相对原来的 57%，增加 18/57≈31.6%，可四舍五入为 32%。两种写法都要说明分母。

GPTDroid 的覆盖率用这个口径；95 vs 66 的 bug 数和原文“31%更多”则不符合相同计算，笔记保留了版本疑点。

<a id="repetition"></a>

## Repeated runs / mean / best-of / budget：重复、均值、取最好与预算

重复运行能观察随机性。Mean 是平均；best-of 是从多次里取最高；二者不能互换。预算可按时间、事件数、模型调用量或费用限制。

同为 60 分钟，模型等待也会消耗时间；同为 1,000 步，调用成本可能不同。GPTDroid 三次取最高，因此不能说结果代表三次平均或一次运行稳定表现。

<a id="survey"></a>

## Survey / SLR / snowballing / taxonomy：综述、系统综述、引用追踪、分类

Survey 综合已有研究；SLR（Systematic Literature Review）强调明确问题、检索/筛选/抽取流程；snowballing 从已有论文的引用等追踪候选；taxonomy 是组织研究的分类体系。

这些是我们做文献调研用的术语。Yu 附录 A 给出了检索与筛选，并公开论文集合；不能因为流程放在参考文献后就忽略它。

<a id="agreement"></a>

## Fleiss’ kappa / coding / venue / track：编码一致性、场合、论文类别

Coding 在综述中指给论文按准则分类，不是写程序。Fleiss’ κ 衡量多人编码的一致性，考虑随机一致因素；0.95 不意味着 GUI 测试准确率 95%。

Venue 是发表会议/期刊；track 是会议内部类别，例如 research track。分类一致、场合可靠与具体算法有效，是不同层次证据。出处：Yu §2/Appendix D；GPTDroid 会议记录。

<a id="yu-topics"></a>

## GEN / R&R / FRM / SRP / RPT / ELE / U&A / EVA：Yu 综述的八个主题标签

这些是该综述用来组织文献的缩写，不要求背诵，也不当作互斥测试技术。

| 缩写 | 原文全称 | 中文任务 | 自编例子 |
|---|---|---|---|
| GEN | GUI Test Generation | 生成测试 | 产生删除并重启的步骤 |
| R&R | GUI Test Record & Replay | 录制/重放 | 保存并再次运行操作 |
| FRM | GUI Testing Framework | 测试框架 | 组织驱动、环境和执行结果 |
| SRP | GUI Test Script Maintenance | 脚本维护 | 删除按钮换位置后更新步骤 |
| RPT | GUI Test Report Analysis | 报告分析 | 合并同一 bug 的重复报告 |
| ELE | GUI Element Detection | 控件识别 | 找到按钮/文本框 |
| U&A | GUI Usability and Accessibility Testing | 易用性/可访问性测试 | 大字体下仍可操作 |
| EVA | GUI Testing Evaluation Criteria | 评测准则 | 明确覆盖与缺陷指标 |

出处：Yu 引言主题说明与 Appendix B。**SRP 是作者的标签，并非该全称各词首字母的标准缩写。** 不应自行展开成另一技术名。

<a id="program-analysis"></a>

## Static analysis / dynamic analysis / instrumentation：代码分析与执行观测

Static analysis 在不实际运行程序时分析实现，例如分析哪些 GUI 事件回调可能到达目标代码；dynamic analysis 观察程序运行中的行为；instrumentation 是加入或使用观测机制记录执行信息，例如统计哪些代码被执行。

它们可以给 GUI 探索指引，也可以采集覆盖。它们本身不自动提供正确业务预期。是否纳入本项目，仍看具体研究怎样服务 GUI 测试。出处：Kong 技术与覆盖采集讨论；GPTDroid §3.1 的指标说明。

<a id="fuzzing-concolic"></a>

## Fuzzing / concolic testing：输入变化与路径条件

Fuzzing（模糊测试）通过大量生成或改变输入探索异常行为；在 GUI 场景可涉及文本或事件序列，不限于均匀随机点击。Concolic 结合具体执行与符号条件，借程序路径上的约束帮助构造新输入。它属于代码分析可支撑 GUI 测试的一种思路，不是必须采用的项目架构。

自编例子：保存回调只在标题非空时进入后续代码，路径条件可帮助准备对应输入。但完整 GUI 流程仍要正确到达保存位置、执行输入，并用合适 oracle 判断结果。出处：Kong Section V-C-1 的传统技术讨论；本仓库未独立复现具体 concolic 工具。

<a id="model-budget"></a>

## Token / latency / model snapshot：模型资源与可复现条件

Token 是模型处理文本时使用的单位，不必与汉字或英文单词一一对应；latency 是请求/执行等待延迟；model snapshot 指记录具体模型版本而非只留系列名。GUI Agent 每步调用模型时，等待会进入总时间预算，长历史也会增加输入量。

这些是记录 LLM GUI 实验所需的条件。GPTDroid 未系统给出当前模型费用与完整版本配置，因此不能从旧结果估算 2026 成本。出处：GPTDroid 实现/实验信息及本笔记的复现边界；Trong 成本与效率讨论。

## 补充术语

以下解释综述主题中实际用到的词，先讲它在 GUI 研究里做什么，不要求背模型公式。

### Feature / embedding / similarity：特征、向量表示与相似性

Feature 是描述对象的属性或数值，例如截图的区域/外观、报告的文字信息。Embedding 将对象编码成一组数值，方便比较；similarity 是按特定规则衡量两个表示多相近。相似性取决于表示与比较方式，不是天然的“同一个 bug”真值。

GUI 里它们可用于匹配控件、找相似截图和报告去重。两张截图相似但错误原因不同，正说明从特征相似到问题相同仍需要任务定义与验证。对应：Yu §4.2/§5.1；Trong 维护阶段。这里只解释机制角色，不补猜某工具用了哪种未读网络。

### Clustering / prioritization / reproduction：聚类、优先级与复现

Clustering 按某种相似关系将报告分组；prioritization 决定报告/测试先处理哪个；reproduction 尝试重新触发已报告的故障。去重要求是否同一问题，聚类可以是更宽的相关问题分组，不能当同一任务。

例如多条删除失败报告可先聚在一起，再区分假删除、crash 和按钮遮挡；复现则需要准备条件并执行操作，确认是否重现相应错误。对应：Yu §4.2/Appendix B Table 7。

### Data-driven / keyword-driven / BDD / Gherkin：测试组织与业务表达

Data-driven 将执行流程与测试数据分开；keyword-driven 将场景词映射成可执行函数；BDD（Behavior-Driven Development）按业务行为与明确预期组织开发/测试。Gherkin 是常用于这种场景描述的结构化语言，基本形式用 Given/When/Then 表达给定条件、执行动作和期望。

对 GUI，例子是“给定已有 A，当删除并重启，那么 A 不存在”。这只是明确表达要求，仍要生成定位、操作与断言代码，格式本身不保证正确。对应：Yu §3.3；Trong 脚本生成阶段。

### ESM / Delta ESM / abstraction–concretization：事件模型与意图迁移

ESM（Event Sequence Model）表示 GUI 可能的事件序列；Delta ESM 表示变化。Yu 的 ATOM 例子用这些作为更新依据，不据此补猜具体算法。

Abstraction 是提取较高层的测试意图，concretization 是在目标界面找可执行实现。源版本一键删除，目标版本需菜单加确认；具体操作变了，高层“删除 A 并检查不恢复”应保持。对应：Yu §4.1；Trong 维护阶段。这是不同机制的作用解释，不说 ESM 与语义抽象就是同一种模型。

## 维护原则

术语首次出现在笔记的阅读正文时，写中文含义并链接相关解释。只列工具名字、只展开英文全称，都不够；要说明这个词在 GUI 测试里具体承担什么作用。论文特有缩写按该论文展开，不借别的领域补猜。

论文中的机制和结果以对应 [单篇笔记](catalog.md) 的版本/章节为准。Android 与 MDN 文档用于基础定义，不能替代 GUI 测试论文的效果证据。
