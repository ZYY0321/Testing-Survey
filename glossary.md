# GUI testing 术语表

这些解释服务于 GUI 探索、执行、判定、维护、评测及文献组织。先用 [field.md](field.md) 理解领域，再遇词查本页。教学例子与论文结果分开；术语定义不能代替论文效果证据。

<a id="ata-evaluation"></a>

## ATA / verdict / step alignment：测试代理、判定与失败步骤对齐

自主测试代理（ATA）接收给定步骤与预期，按序执行并检查断言；自主操作代理（AWA）通常以完成用户目标为主要标准。Verdict 指通过/失败标签。两者都能操作界面，但任务完成本身不能证明中间断言得到验证。

以“应失败”为正类时，Sensitivity（灵敏度/Recall）衡量失败用例被正确报失败的比例；Specificity（特异度）衡量通过用例被正确判通过的比例。二者不可互换，也不等于报告 Precision。

失败步骤对齐检查模型与人是否在同一步失败。自编例子：规格要求第 5 步的价格应为 30，但实际是 40；Agent 若第 2 步就因点错而停止，最终 fail 虽相同，却没有检验价格错误。Chevrot 等将这种“结论相同、原因错位”纳入 Type III error，并提出 TruAcc 收紧评价。停止在同一步仍不能证明同一根因，passing 用例是否跳过断言也需额外证据。

出处：[Are Autonomous Web Agents Good Testers? 笔记](notes/chevrot2025webagents.md)，所读 arXiv v1 Section 4.4；该版本公式与结果表存在计算口径疑点，术语解释不代表数字已复现。

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

<a id="functional-flow-graph"></a>

## 功能流图（Functional Flow Graph, FFG）

把用户功能及其状态依赖表示为有向图。FuncDroid的节点记录目标、有关变量和操作轨迹，边记录前后轨迹及启用条件；不是仅记录页面是否能跳转。例如先从血糖页添加提醒，再从管理页删除，会影响是否能再次添加同一时间提醒。图来自运行观察和模型推理，条件本身仍需验证。来源：[FuncDroid](notes/he2026funcdroid.md)，Sec.3.1–3.2，PDF第5–13页。

<a id="chromaeyes-visual-oracle"></a>

## 明暗模式一致性 / 边缘复核 / IoU

明暗模式一致性要求主题转换后元素保持可见和可辨认，不能要求像素相同。IoU是两个检测框交集与并集面积之比，ChromaEyes以0.5判断对应位置；某边只有一边检测到对象时，再比较同区域边缘，以排除检测器漏检。自编GUI例子：深色模式的保存图标未被对象检测器找到，但截图中轮廓与亮色模式相同，此时不立即把漏检当缺陷。若两模式都有同样的截断，此规则也未必发现。出处：[ChromaEyes笔记](notes/shweta2026chromaeyes.md)，§3.3–3.4、§4.4，PDF第10–14页。

<a id="symbolic-gui-oracle"></a>

## GUI symbolization / schema / DSL：界面符号化、数据结构与断言语言

符号化把截图或界面树中的业务对象变成可引用的数据，例如商品有标题、价格和数量，购物车有商品列表。schema 规定字段与类型；DSL 提供访问当前/历史状态、抽取数据、比较关系的接口。完整 GUI 例子：保存加入商品前后的购物车，抽取新商品，再检查旧商品仍在、数量与价格一致、小计等于旧小计加新商品金额。这样判定可由程序比较，而不只依靠模型看图回答“正确”。不过抽取的数据及断言仍可能由模型生成错误，结构化不等于独立真值。来源：[WebTestPilot](notes/teoh2026webtestpilot.md) §2–4，PDF pp.5–11，尤其 Figures 3–6 和 Table 1。


<a id="page-reidentification"></a>

## Page reidentification：跨时间识别同一逻辑页面

页面内容改变后，仍可能属于同一页面类型。WebTestPilot 先按 DOM 树编辑距离选择历史候选，再让模型比较截图，判断是否沿用相同 page_id。例子：空购物车和加入相机后的购物车应关联，才能验证增加的商品与金额；仅将两张图视作不同状态会丢掉这种比较。它有助于检索历史，不证明两状态所有业务含义一致，也不保证后续断言正确。来源：[WebTestPilot](notes/teoh2026webtestpilot.md) §4.3.2，PDF p.11。


<a id="property-based-gui"></a>

## Property-based testing / executable property：基于属性的GUI测试与可执行属性

先指定对一类情况都应成立的功能规则，再让测试框架生成不同操作序列寻找违反规则的状态。Android可执行属性可表示为前置条件P、交互I、后置条件Q：例如文件列表存在时，选择一个文件夹并点击，随后路径应包含该目录名。它不同于只写固定一次输入输出；属性也可以包含局部操作，框架负责反复创造可检查的状态。iPBT将人写的结构化规则编译成Kea代码，不负责自动发现所有规则，也不改变Kea的输入生成。来源：[iPBT](notes/xiong2026ipbt.md) §1–3，PDF pp.1–8；具体原例Figure1，pp.1–2。


<a id="enriched-widget-context"></a>

## Enriched widget context：增强控件上下文

将控件原始标识与模型推断的语义放在一起，帮助把测试描述落到具体控件。iPBT输入页面截图、应用和Activity名、控件局部图与text/resource_id/content_description/class，生成semantic label和functionality。例子：AnkiDroid预览页里resource_id为qa的Test文本被标注为“问题文本显示区”，属性中的“问题”因而能够映射到qa；但“选中的卡片”和“正在浏览的卡片”仍可能被混淆。语义注释是模型推断，不是开发者保证的真值。来源：[iPBT](notes/xiong2026ipbt.md) §3.1，PDF pp.4–6；失败例p.10。


<a id="deep-q-network"></a>


## 深度Q网络（Deep Q-Network, DQN）

用神经网络估计某个状态下做一个动作的未来累计奖励，而不是为所有状态动作组合维护一张不断增大的表。在GUI测试中，奖励可来自新状态或不同动作，不自动表示界面行为正确。WebCQ把页面结构向量与当前候选动作特征拼接，逐个估值，适配每页按钮数不同的情形。来源：[WebCQ](notes/fan2026webcq.md)，Sec.2.1/4.2，PDF第2、4–5页。

<a id="ctde"></a>


## 集中训练、分散执行（Centralized Training and Decentralized Execution, CTDE）

多个执行器训练时分享观察、动作和奖励，由集中模块协调学习；执行时各自根据当前看到的页面选择动作。例如几个浏览器测试同一网站，可以共同积累经验，再分别决定点击什么。WebCQ使用QTRAN使局部动作价值与联合价值学习相容，并在各浏览器至少产生新转换后训练；训练期间仍有等待。它不是用多个用户角色共同完成一个业务事务。来源：[WebCQ](notes/fan2026webcq.md)，Sec.2.2–4.3，PDF第3–6页。

<a id="failure-log-deduplication"></a>


## 失败日志规范化与去重

先把错误日志中的易变内容（如数字）统一，再对相同文本合并计数，以减少同一种现象的重复统计。WebCQ只保留浏览器SEVERE日志，再规范化去重；这得到的是不同日志数量，不是开发者确认的独立缺陷数量。同一根因可能产生不同日志，不同根因也可能被规范化合并。来源：[WebCQ](notes/fan2026webcq.md)，Sec.6.1.1，PDF第7页。

<a id="mbt-industrial"></a>

## MBT / IFML / 混合方法研究：显式行为建模与工业评价

模型驱动测试（MBT）的model可指人工维护的页面、控件、动作和条件图，不是大语言模型。IFML是交互流建模语言，Cao等扩展为电视GUI的T-IFML，表达遥控焦点和控件激活条件。原文例子是邮箱未填时密码框不可用，生成器应过滤直接操作密码框的不可行路径。执行路径覆盖模型不意味着模型已涵盖应用全部需求。

混合方法研究同时用覆盖率、工时等定量数据和问卷访谈理解采用体验；Likert为按同意程度评分的有序量表。问卷评分显示更认可维护性不能证明工具找出更多缺陷。Cao等的短期/持续参与者是两组事后评价，不是同一人完整前后随访。出处：[工业MBT笔记](notes/cao2025mbtindustrial.md)，§2.2、§3–4，PDF第5–16页（含机构封面，较印刷页多1）。

<a id="test-brittleness"></a>

## 测试脆弱性（test brittleness）

测试与旧版界面、数据或交互时序绑定，应用或环境变化后即使业务功能仍合理，原脚本也无法继续。例如酒店网站两个年龄下拉框短暂保留同名选项，脚本误选隐藏旧元素。它不等同同版本下不稳定的flaky，也不能直接作为产品bug。维护应区别定位器、流程、框架交互和环境原因。出处：[Who’s to Blame?](notes/zhang2025webbrittleness.md)，Sec.II–IV，PDF第2–9页。

<a id="set-of-mark"></a>

## Set-of-Mark（SoM）：给屏幕目标编号

在截图中的目标区域画框并编号，让提示用编号指代操作对象。例如一页有多个心形图标时，模型可输出“操作8号”，执行器再查8号对应的位置。KuiTest先结合控件树与视觉检测找到控件再编号；这与把整屏切成固定网格编号不同。编号减少指代含糊，但检测框或编号选错仍会操作失败。来源：[KuiTest](notes/hu2025kuitest.md) §III-C、Figure3，PDF p.4。

<a id="icon-function-library"></a>

## Icon function library：图标功能库与相似检索

把无文字图标的图像、人工功能名称和说明保存起来，用图像向量相似度为新图标检索候选功能。KuiTest以CLIP编码图像，最高余弦相似度超过阈值δ时把功能说明加入提示。例子：没有文字的心形按钮与收藏图标匹配，可提示模型预测“收藏当前内容”；具体应用可能赋予它不同语义，所以检索结果不能当独立真值。评测库18类来自随机十页，仍含人工准备。来源：[KuiTest](notes/hu2025kuitest.md) §III-C、IV-A4，PDF pp.4–5、7。

<a id="uilens-display-taxonomy"></a>

## 显示缺陷操作定义：溢出、裁剪、重叠、缺失与省略

操作定义规定数据集中哪些情况算正例，而非任何应用都适用的设计真理。UI-Lens把文字越出可见容器、内容硬裁切、组件不当重叠、应显示内容空白及不恰当省略分别标注；正常可滚动区域的边缘露出、渐隐、加载/占位、弹幕或用户生成内容等按具体任务排除。异常省略还用短文本阈值（中文≤2、英文≤4、数字≤4）等规则。原文例子：横向滚动列表露出下一项会被模型误报裁剪；Figma以遮字后补省略号合成正例，不代表真实代码运行触发。出处：[UI-Lens笔记](notes/xiang2026uilens.md)，主文§2/§4、补充B/C，主文PDF第4–8页、补充第2–10页。

<a id="uilens-evaluation"></a>

## 单屏分类与跨屏问题匹配：评测单位和位置证据

单屏分类问某截图是否有指定类问题，指标分母为截图；即使数据标有多个框，分类准确率也不是元素定位精度。跨屏开放式检测输出涉及页与描述；UI-Lens以预测和真值页集合有交集、且问题描述语义一致作为匹配，不要求所有相关页完全相同。自编例子：真值涉及列表/详情/付款三页，预测只提详情，页交集成立仍需核描述，不能据通过匹配说它找全三页。judge和一对一去重未充分说明时，应限制召回解释。出处：[UI-Lens笔记](notes/xiang2026uilens.md)，主文§3.1/图5与补充E，主文PDF第5–6页、补充第11–12页。

<a id="multi-user-interactive-testing"></a>

## 多用户交互测试

让不同用户设备协同执行有依赖的操作，并检查另一端反馈。例如User1发起通话，User2应收到邀请并及时接听。不同于多个agent分别规划/执行同一手机，也不同于多个浏览器各自探索网站。Agent for User每设备一个用户agent，以switch命令交接；其非崩溃判错仍依赖人工。出处：[Agent for User](notes/feng2025agentforuser.md)，Sec.II–IV，PDF第2–9页。

<a id="action-sequence-similarity"></a>

## 动作序列相似度（LCS）

最长公共子序列保留顺序但允许跳过动作；用生成序列和人工参考的公共长度衡量贴近程度。Agent for User以两倍LCS长度除以两序列长度之和。相似度高说明动作接近参考，不证明所有业务预期已检查；不同合理路径可能产生不同相似度。出处：[Agent for User](notes/feng2025agentforuser.md)，Sec.IV-A，PDF第7页。

<a id="soap-opera-testing"></a>

## Exploratory testing / Soap Opera Testing：探索式测试与场景引导探索

探索式测试边操作、观察、判断边设计后续测试；Soap Opera Testing以较复杂使用场景组织主线，同时尝试相关输入或分支寻找未预设的错误。它不是无准备的随机点击，也不等同重放固定脚本。完整GUI例子：按Firefox关闭并恢复标签的主线操作，同时观察中途缩略图；出现分享入口后探索取消返回是否保留正确标题，再回主线。人工还可对用户名用长字符串边界输入，检查重叠。自动系统只完成主线不能证明完成这些边界探索。来源：[自动Soap Opera Testing](notes/su2025asot.md) §2–4，PDF pp.3–15。

<a id="scenario-knowledge-graph"></a>

## Scenario Knowledge Graph（SKG）：步骤与预期相连的场景知识

把历史报告中的场景摘要、前置条件、操作步骤和预期行为关联起来。含义相同的步骤归一并赋ID，保存不同表达及关联oracle；检索“打开标签托盘”可找到执行细节与缩略图应显示的预期，帮助当前GUI动作和判定。本文按结构化文本分块，再用关键词和向量检索，并非从业务源码生成完整规格。历史预期可能属于别的页面版本、重复或不适用，不能因为检索到就直接报告错误。来源：[自动Soap Opera Testing](notes/su2025asot.md) §3.2、3.5，PDF pp.8–11。

<a id="gui-lag"></a>

## GUI卡顿（GUI lag）

用户期望画面随操作继续变化，却发生可感知的掉帧、延迟加载或冻结。GUIWatcher用录屏区间表达：相邻渲染时间明显拉长、加载占位符持续超过阈值、无占位符的静止区间。正常阅读静态页面不自动属于卡顿，阈值还需结合交互场景。出处：[GUIWatcher](notes/liu2025guiwatcher.md)，Sec.II–III，PDF第3–6页。

<a id="presentation-timestamp"></a>

## 显示时间戳（Presentation Time Stamp, PTS）

视频帧应当显示的时间位置。用相邻PTS差可以观察某帧停留多久，例如理想60FPS约16.7毫秒一次变化；长间隔可能指示掉帧或延迟。它不是脚本等待时间，也不是图像分析程序的运行耗时。录制/编码必须保留真实呈现时序，否则时间差未必反映手机实际体验。出处：[GUIWatcher](notes/liu2025guiwatcher.md)，Sec.II/III，PDF第3–6页。

<a id="reca11-transcript"></a>

## 可访问性用户测试转写 / 字幕切块 / All与Common

有障碍用户用屏幕阅读器、语音/开关导航、放大工具完成GUI任务，并出声解释困难；VTT转写保留每条字幕的起止时间、说话者和文本。Reca11按100条字幕切块，带前块1条上下文，抽取带时间戳的问题。100条字幕不是100秒或100 token；模型不直接看录像。五次输出以标题+描述的句向量相似度去重，All取独特问题并集，Common取五次均出现的交集，不是多数投票。原文例子：用户反馈新增分类页面按钮却叫添加收入，模型整理成标签问题并给回看时间。出处：[Reca11笔记](notes/huq2025reca11.md)，§III.C/IV.A及图2/6，PDF第3–9页。

<a id="reca11-evaluation"></a>

## 用户反馈抽取真值：confirmed、mentioned与真正缺陷确认

Reca11把参考问题集内条目标为confirmed；参考集外但录像中用户确实提过的问题为mentioned；无相应反馈为non-issue。前两类都算抽取TP，用户提过而模型漏掉为FN，故其recall是用户反馈抽取率，不是应用全部注入错误的检出率。自编GUI例子：用户抱怨按钮名字难懂可算mentioned，但尚不能据此说维护者已确认代码故障。反之，真实截断若无人说出且转写没体现，工具也无从抽取。出处：[Reca11笔记](notes/huq2025reca11.md)，§III.D/IV.B，PDF第6、9页；原文表VI计数另有不一致，不能直接复算所有聚合。

<a id="bug-testing-cognitive-map"></a>

## bug测试认知图（testing cognitive map）

将相似应用历史bug报告挂到当前应用的Activity跳转图，表示到达某组件后应尝试的风险操作。BugHunter用LLM按功能映射两应用节点，例如AddTransaction和AddExpense；完成一份报告后优先测同页剩余报告，再转相邻页。这是生成目标与路线的地图，不保证历史错误在当前应用也存在。出处：[BugHunter](notes/chen2025bughunter.md)，Sec.2.2–2.3，PDF第8–11页。

<a id="xr-grounding"></a>

## XR / IGE / GUI grounding：三维场景的可交互对象与二维定位

XR包括VR/AR/MR；Orienter实际数据来自Steam VR-Only应用。IGE是可交互界面元素，可以是球、杯子、树而非标准按钮。GUI grounding将语言所指对象定位到屏幕；Orienter用单眼二维截图输出框，不恢复深度或抓取轨迹。原文例子：树在种植游戏可拾取，在钓鱼游戏可能只是背景，因此交互性需结合玩法语境。出处：[Orienter笔记](notes/li2026xrgrounding.md)，所读arXiv v4 §1/§3.1/§3.5，PDF第3、5–6、10页；正式FSE2026题名不同，正式全文未校对。

<a id="xr-simulated-coverage"></a>

## 动作空间约束 / 模拟IGE覆盖：命中框不等于真实动作成功

Orienter下游把预采截图作为固定场景，逐步生成二维点；点在人工可交互框内算有效，某框至少一次被点中算覆盖。预测框限制动作候选可减少无效背景采样，但没有实际点击/抓取、状态变化或缺陷预言。自编例子：点命中球框，仍可能因球在远处或控制器操作不对而无法抓取，不能据命中算实际交互成功。论文60分钟是模拟采样进度，不是设备在线执行耗时保证。出处：[Orienter笔记](notes/li2026xrgrounding.md)，所读v4 §5.3.4/§5.4/§6.3，PDF第13–14、18页。

<a id="llmdroid-pagecluster"></a>

## PageCluster / Root Page / Dice：页面簇、根页面与结构相似度

LLMDroid把结构相近的运行时页面归为一簇，首个页面为根页面。若两控件的类型、resource-id、支持动作、宽高全部相同，就视作相同控件；Dice为两倍共有控件数除以两页控件总数之和。新页加入与根页面最相似且超过阈值的簇，否则另建簇。默认0.6来自五应用试验，不是通用语义等价标准。初次只总结根页面，模型空闲时再用其他页面的独特控件补摘要。

自编例子：新闻列表换了一批标题但控件结构相近，可避免每次刷新都总结；同样的结构却可能包含新功能，不能据聚类判它已经测试充分。出处：[LLMDroid笔记](notes/wang2025llmdroid.md)，Sec.3.1.1、第5–6页；Sec.4.4、第18–19页。

<a id="llmdroid-coverage-trigger"></a>

## Coverage stagnation trigger / concurrent summary：覆盖停滞触发与后台总结

LLMDroid通常由原探索器执行，LLM在后台把页面整理成功能摘要；当连续窗口内的方法覆盖相对增长均低于动态阈值，才暂停原探索器，调用LLM选下一目标。论文设窗口80、初始阈值0.05、下限0.01；阈值按当前增长相对历史均值的偏差更新。模型回复总耗时与真正阻塞操作的时间不同，因为后台总结可与探索重叠。

自编例子：探索器重复点列表而不再执行新方法，才请求模型推荐设置入口；覆盖停滞本身不是软件错误，也不证明模型建议正确。出处：[LLMDroid笔记](notes/wang2025llmdroid.md)，Sec.3.1.2–3.1.3、第8–9页；Sec.4.3、第17–18页。

<a id="llmdroid-offline-navigation"></a>

## Offline page navigation：不调用LLM的本地路径导航

LLMDroid在历史GUI操作轨迹构成的图上求路线，重启应用后从起点重放到目标页。“Offline”指规划和逐步导航不询问LLM，仍在运行中的应用上执行。它允许页面相似匹配、跳过已跨越节点，失败后换较新路线并有限重试。

自编例子：过去走过主页→工具页→测速页，之后可在本地复用该路线，不必每一步付费请求模型；若弹窗或服务端改变流程，历史路线仍会失效。出处：[LLMDroid笔记](notes/wang2025llmdroid.md)，Sec.3.2.2、第10–11页。

<a id="tracedroid-code-signals"></a>

## Suspicious code-level signals / callback backtracking：可疑代码信号与回调反向追踪

TraceDroid把代码规则命中的风险片段、对应GUI控件、上游事件调用链连接成可供探索使用的线索。它先用上下文判断空指针、线程/生命周期等潜在风险，再沿监听器绑定、布局ID和调用关系回溯到用户能操作的入口。例如原文Fig.4以btnSubmit和onClick→validate→saveNote展示提交操作如何进入可能不安全的保存代码。信号提示应优先测试，并不直接证明代码有错；真实崩溃还需运行时异常与进程证据。

出处：[TraceDroid笔记](notes/chen2026tracedroid.md)，Sec.3.1、PDF第5–8页；73条规则涉及1–3个上下文条件，不能简化成只按API名匹配。

<a id="tracedroid-path-monitor"></a>

## Suspicious-path completion / execution-aware monitoring：可疑路径补全与执行监控

TraceDroid将可疑控件接到ATG粗路径，再给模型两跳子图、目标属性、回调条件及动作空间，输出含页面、选择器和操作的JSON序列。先检查图跳转和控件存在性，运行时再比较计划路径、实际动作/界面与系统日志，区分完成、未完成和崩溃，必要时恢复与重试。图上可达或控件存在只是轻量结构检查，不能证明所有数据和业务前置条件已满足。

出处：[TraceDroid笔记](notes/chen2026tracedroid.md)，Sec.3.2.3–3.3.3、PDF第10–12页；论文评价为crash检出，不将监控到冻结/偏离自动扩大为已验证非崩溃oracle。

<a id="guirevive-obsolescence"></a>

## Obsolete GUI test / repair ground truth：过时测试与修复真值

GUIRevive研究旧版可执行、新版因无法定位或操作目标而中断的GUI测试。修复可替换失败控件并在其前补导航，目标是保留原交互功能，不是修改被测应用。其benchmark由随机旧版序列跨版重放构造，人工找新版对应控件作真值；功能已删除、找不到对应控件的案例被排除。评价成功率看找到的控件是否与人工真值一致，不能等同整个测试的断言全部正确。

原文例子：暗色主题从主菜单复选框迁移到设置内选项；旧脚本失败但功能仍在。出处：[GUIRevive笔记](notes/song2026guirevive.md)，Sec.2–3.1、5.1.4，第4–6、14页。

<a id="guirevive-functional-validation"></a>

## Functionality-preserving validation / WES：功能保持验证与控件效果摘要

GUIRevive先要求旧控件和候选在导航、展示、可编辑三种交互角色中一致，再比较各自在对应版本被操作后的可观察功能效果。WES是Widget Effect Summarization，即原操作前后界面变化的文字摘要，给匹配、验证和探索提供语义依据。判断依赖多模态模型，不是源码等价证明，也不是完整业务oracle。

原文暗色主题案例中，打开主题设置页的入口与可直接改变主题的选项角色不同；找到前者不等于完成最终目标修复。出处：[GUIRevive笔记](notes/song2026guirevive.md)，Sec.4.1.3、4.2，第11–12页；案例Fig.3第8–9页。

<a id="guirevive-goal-exploration"></a>

## Goal-guided inter-state exploration / PES：目标引导的跨状态探索与历史摘要

GUIRevive当前页找不到有效修复候选时，以旧交互的功能效果为目标，依据当前可操作控件、已有路径摘要选择下一个入口或返回。每步后用操作前后截图和控件局部图生成简短效果描述，按顺序追加为Past Exploration Summarization相关历史；避免重复同一路径，必要时回退。默认最多15探索步。这里的状态包含弹窗、抽屉等，并非每次都进入新Activity。

原文Permission Manager X需要从主菜单转到Settings、Theming和主题选项，逐状态寻找原暗色功能。目标是恢复旧测试，不是无目标地增加页面覆盖。出处：[GUIRevive笔记](notes/song2026guirevive.md)，Sec.4.3、5.1.3，第12–14页；Fig.3第8–9页。

<a id="tusr-test-unit"></a>

## Test Unit（测试单元）与多对多脚本修复

TUSR 的测试单元是 GUI 脚本中一段**连续、共同完成单一测试意图**的操作，不是函数级“单元测试”。例如旧版 ConnectBot 用“点协议下拉框→选telnet”两步切换协议，新版改成“Add host→点协议字段→选telnet”三步；修复应对齐整段意图，不强求每个旧操作恰好对应一个新操作。单元的最小边界有解释空间，作者用LLM切分并以导航、对话框和输入隔离规则纠正。出处：[TUSR笔记](notes/cao2026tusr.md)，Sec.2–3.2，PDF第3–9页。

<a id="tusr-intention-preservation"></a>

## 脚本修复中的原意图保持

修复正确需要新版操作仍在测试旧版原来要检验的事情；仅“找到了按钮”“脚本跑完”“最终画面一样”都不足以单独证明。例如旧脚本故意先输入、清空、再改值，修复器若只输入最终值，可能绕过本来要测的编辑过程。TUSR通过输入操作隔离局部约束这一风险，但运行时完成标志仍由LLM判断；其结果正确性在实验中另由三名学生对照旧版意图核查。该评价不是应用没有产品缺陷的oracle。出处：[TUSR笔记](notes/cao2026tusr.md)，Sec.3.2.2、3.3及4.2，PDF第8–14页。

<a id="tusr-reflective-memory"></a>

## TUSR的单元级与应用级反思记忆

单元级记忆保存当前测试单元失败尝试的原因分析和下一次的修改建议；应用级记忆用XML识别GUI状态，并记录这个状态中曾出现的无效操作，例如向不可滚动的界面滑动。重试时同时提供当前单元的失败经验和当前状态的无效操作清单，避免把相同错误重复生成。每次尝试超过原单元长度三倍仍未完成，会触发失败反思并回到单元起点；论文没有完整给出全局重试上限或副作用恢复细节。这不是模型训练，也不能自动当作跨应用可迁移记忆。出处：[TUSR笔记](notes/cao2026tusr.md)，Sec.3.1及3.3，PDF第6、9–11页。

<a id="interdroid-inter-app"></a>

## 跨应用功能（inter-app functionality）

多个应用协作完成一个用户目标，如录音应用经系统分享面板调用邮件发送文件，或原应用跳转系统设置取得照片权限。跨应用GUI测试需要在应用切换后保留目标、选择正确外部控件并检查返回过程；不等同于多个Agent或独立测试多个应用。InterDroid调查中的分享、系统设置、第三方服务、信息同步、认证，是其样本的功能分类，不是GUI测试全领域分类。出处：[InterDroid笔记](notes/chen2026interdroid.md)，Sec.1–2，PDF第2–3页。

<a id="interdroid-conversational-memory"></a>

## 对话参考记忆与当前执行记忆

InterDroid的全局记忆把检索到的历史功能改写为用户/助手交替的示例对话，包含目标、截图与动作；局部记忆记录当前会话实际执行的动作和完成状态，二者进入提示上下文。这里没有证明执行模型权重被更新，也不能把历史成功当作当前已观察到的结果。原文录音分享例子中，参考流程进入Gmail，当前记录仍须单独标明本次哪些动作尚未完成。出处：[InterDroid笔记](notes/chen2026interdroid.md)，Sec.3.2、Fig.5–6，PDF第6–7页。

<a id="interdroid-path-metrics"></a>

## 路径完全匹配率（EM）与功能路径覆盖

InterDroid的EM要求生成动作序列与人工参考序列完全一致；页面和动作覆盖衡量跨应用任务探索程度，原文未展开全部集合与替代路线的计分规则。自编例子：两条不同路径都能完成邮件分享，严格序列匹配仍可能只认可参考路径；反之，走对路径也不能证明收件人或后端数据正确。EM、执行完成和缺陷检出应分开报告。出处：[InterDroid笔记](notes/chen2026interdroid.md)，Sec.4.1/4.3及Table1，PDF第8–9页。

<a id="combination-interaction"></a>

## Combination interaction / page-level and widget-level planning：组合交互与两层规划

COMBDroid将同一功能内多个控件依赖配合的流程称为组合交互，例如航班查询须先输入并选择城市，再选日期、滚动填写乘客/舱位，最后确认。一个功能上下文可跨临时弹窗，不等于静态截图，也不是任意跨应用工作流。页面级计划决定依次完成什么，控件级生成根据当前树/截图编号把眼前一步变成一次click/input/scroll；完成后原探索器继续。

出处：[COMBDroid笔记](notes/liu2026combination.md)，Sec.1/Fig.1第2页；Sec.3.1–3.2第4–7页。完成率用于评价执行，不能直接当功能或显示缺陷检出率。

<a id="combdroid-dual-memory"></a>

## Context memory / experience memory：COMBDroid上下文与经验双记忆

上下文记忆针对当前功能记录功能步骤、已填参数、主表单/临时面板状态和最近动作反馈，初始为空，滚动时保留参数，弹窗返回后将选择结果并回主上下文，结束清空。经验记忆则从成功轨迹抽象功能说明、关键界面和通用策略，检索相似经验用于新计划。原文航班例子中，具体城市属于当前参数，打开地点选择→输入→选候选是可复用策略。

出处：[COMBDroid笔记](notes/liu2026combination.md)，Sec.3.3/Fig.6第7–8页。论文明确当前上下文每轮为空，但未充分说明经验库跨应用/重复实验的初始化隔离，不能将两种记忆混为一个清空协议。

<a id="perframe-loading-boundaries"></a>

## Loading start/end frame / FPS：加载边界与帧误差

PerFrame从测试视频定位一次加载的起止帧，统一30FPS，每帧约33.3毫秒；5帧约0.167秒、20帧约0.667秒。一般定义以交互触发首个可见加载响应为起点、主要内容完整且可交互的最早帧为终点；Android实现却以OCR首次识别开发者触摸坐标为起点，两种时间可能不同。单边界误差不等于完整时长误差；原文未分报两端和合并规则。

原文商户页案例经历多次更新，不能在页面框架刚出现时就认作完成。出处：[PerFrame笔记](notes/liu2026perframe.md)，Sec.2.1、3.6.1、4.1.4，第2–3、7–8页。

<a id="perframe-semantic-refinement"></a>

## SSIM pre-selection / semantic refinement：画面相似度预筛与语义细化

PerFrame计算相邻视频帧的结构相似度SSIM，从显著变化附近找稳定候选；随后把候选和末帧参照拼图给多模态模型，判断主要图片文字控件是否加载完，并逐轮缩小采样邻域。SSIM只提出位置，模型区分内容加载与正常动画，最终再复查各轮候选。正文未给完整阈值、窗口及计票规则。

自编教学例：广告GIF持续变动但主要列表已加载，不能一直等整屏像素静止；反之空白占位不动也不代表完成。出处：[PerFrame笔记](notes/liu2026perframe.md)，Sec.3.2–3.5、Fig.4–5，第5–7页。

<a id="perframe-incomplete-load"></a>

## Incomplete loading / observation endpoint：未完成加载与观察终点

PerFrame正常路径将录像末帧当完成页面参照；若模型预先判断末帧仍未加载完，就直接把末帧标成分析结束帧并终止。此输出表示录像观察已结束，不表示应用在该时刻完成；真实耗时可能更长或始终失败。论文弱网99.1%的帧标签符合率受此政策影响，不是独立的真实完成检测率。

自编教学例：30秒录像结束仍转圈，最多可说截至30秒尚未完成，不能报告30秒成功加载。出处：[PerFrame笔记](notes/liu2026perframe.md)，Sec.3.6.3、Table2–3，第7、9页。

## 维护原则

术语首次出现在笔记的阅读正文时，写中文含义并链接相关解释。只列工具名字、只展开英文全称，都不够；要说明这个词在 GUI 测试里具体承担什么作用。论文特有缩写按该论文展开，不借别的领域补猜。

论文中的机制和结果以对应 [单篇笔记](catalog.md) 的版本/章节为准。Android 与 MDN 文档用于基础定义，不能替代 GUI 测试论文的效果证据。
