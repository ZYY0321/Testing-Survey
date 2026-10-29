# AI for GUI Testing 文献调研

本仓库围绕 **GUI testing** 整理文献。阅读入口分为三层：

| 文件 | 用途 |
|---|---|
| [catalog.md](catalog.md) | 具体研究方向的单篇论文总表；每篇一行，适合横向比较 |
| [field.md](field.md) | 综述论文与领域地图；每篇综述单独成表，不把不同综述硬塞进同一行 |
| [glossary.md](glossary.md) | GUI testing 术语；阅读笔记遇到生词时查询 |
| `notes/<id>.md` | 单篇论文的完整分析、实验核对、原文摘录与版本说明 |
| [examples/](examples/README.md) | 大表写法示例与填写要求；新加一行前先对照这里 |
| [reviews/](reviews/README.md) | 审稿记录库：投到哪、中没中、审稿人为什么这么判；写自己的论文前对照 |

## UX 问题驱动探索与 UX oracle：专题入口

### 2026-10-27 补充：移动探索、运行干预与缺陷基准

以下六篇已完成正式全文记录，仍为 `full / extracted`，未运行复现或由用户逐项核对。另为 MemoDroid 建摘要级记录，明确保留全文缺口。各篇正文解释输入、流程、真值、实验分母和对 GUI 测试的具体作用，通用手机任务成功不作为产品缺陷发现率。

| 论文与笔记 | 正式场合 | 阅读用途与边界 |
|---|---|---|
| [DroidFixBench](notes/mahmud2026droidfixbench.md) | ISSTA 2026 Research | 真 Android 源码变异及可执行故障资产；原实验测修复，不能把通过测试当语义正确 |
| [AnTrap](notes/gan2026antrap.md) | EMNLP 2026 Main | 四层十类运行干预；Pass@3、专用/混合训练分开，不是产品故障发现基准 |
| [D-GARA](notes/chen2026dgara.md) | AAAI 2026 Technical Track | 条件触发可交互中断、状态核验；条件鲁棒率不是两个总成功率相除 |
| [OS-Genesis](notes/sun2025osgenesis.md) | ACL 2025 Main Long Papers | 局部交互反推任务、重新执行和奖励采样；生成训练轨迹不自动生成测试预期 |
| [OS-Oracle](notes/wu2026osoracle.md) | CVPR 2026 Main | 错误动作合成、逐步critic与独立人工动作评价；不替代应用业务/UX oracle |
| [MobileWorld](notes/kong2026mobileworld.md) | ACL 2026 Main Long Papers | 可重置手机/后端和四种成功检查；有动态外部工具依赖，未构造产品故障集 |
| [MemoDroid](notes/chen2025memodroid.md) | ASE 2025 Research | **abstract / extracted**：只读官方摘要，全文受访问验证限制；三层记忆机制待补正文和原始实验 |

下方是已有更广范围的专题导航，不把本轮补充范围扩大成删去历史论文记录。

以下按实际研究作用组织，重点覆盖2025–2026年工作。**到达目标页面、触发问题、主动复核和最终判错是不同任务**；通用导航成功率不能直接作为UX缺陷发现效果。全部具体机制、数据、版本及局限仍以单篇笔记为准。

| 阅读主题 | 论文与发表场合 | 主要值得比较的机制 | 任务边界 |
|---|---|---|---|
| 候选异常驱动的主动复核 | [ReFLAIR](notes/he2026reflair.md)，FSE2026 Research | 宽窄布局对应；对疑似缺失项滚动、展开菜单后重判 | 响应式Web信息/点击功能丢失，依赖DOM，不是所有原生端视觉缺陷 |
| 设置变化引发的显示问题 | [SUDFinder](notes/huang2026sudfinder.md)，TSE2026 | 系统设置变化、测试布局强制渲染与显示异常判定结合 | 强制渲染不等于真实导航可达；所读作者版与正式身份分开记录 |
| 测试目标感知的状态探索 | [A11yScan](notes/zhang2025a11yscan.md)，ICSE2025 Research | 控件状态组合、关键页重访，再用上下文复核规则警告 | 非LLM可访问性方法；不是按视觉异常概率自适应搜索 |
| 定向任务中的主动检查 | [GUITester](notes/gao2026guitester.md)，Findings ACL2026 | 任务中插入边界/替代路径/状态检查，分离异常发现与归因 | Findings而非ACL主会；UX指交互流程，尚未覆盖布局重叠和截断 |
| 跨浏览器视觉差异的语义筛查 | [XBIDetective](notes/grewal2026xbidetective.md)，ICSE2026 SEIP | 对跨浏览器截图差异作视觉语义判断 | 工业实践论文；不是全站探索器，不与单屏显示基准直接排名 |
| 动态内容的过程型oracle | [TIMESTUMP](notes/mehralian2025timestump.md)，ICSE2025 Research | 动作前中后控件、事件和焦点判断读屏障碍 | 不读取像素判断遮挡，不是视觉布局检测器或风险搜索器 |
| 点击区域的主动验证 | [TouchAlign](notes/qiu2026touchalign.md)，FSE2026 **Industry** | 实际点按探测可见框与交互框的错位，静态/动态反馈分流 | 页面入口和重放路径已给定；中心点击正确是前提，不是自主全局探索 |
| 规范与语义证据驱动oracle | [GenA11y](notes/he2025gena11y.md)，FSE2025 Research | 按37项规范分别提取DOM、计算后样式和必要图像 | 运行时截图/缩放不等于动态状态探索，总分不能代表截断子类效果 |
| 辅助技术的真实导航检查 | [A11yNavigator](notes/jain2025a11ynavigator.md)，ASE2025 Research | 比较DOM交互目标与真实NVDA可到达/可激活结果 | 不是自然语言页面导航或视觉布局检测；完整执行成本较高 |
| 误操作风险的控件探索 | [EP-Detector](notes/guo2025epdetector.md)，ICSE2025 Research | 围绕易误操作控件进行导航、事件模拟与异常判定 | 需区分误操作风险、已触发后果和自然语言任务导航，具体定义见笔记 |
| 颜色修复与效果验证（相邻工作） | [DroidPalette](notes/gu2025droidpalette.md)，ASE2025 Research | 同页选色、XML属性试验、修后规则复检与用户/维护者评价 | 主任务是repair，不作探索主线；规则通过不等于完整UX正确 |

相关已读参照：[UI-Lens](notes/xiang2026uilens.md)提供单屏/跨屏显示评价，[ChromaEyes](notes/shweta2026chromaeyes.md)利用明暗模式关系，[VisionDroid](notes/liu2024visiondroid.md)结合探索和非崩溃语义判定，[KuiTest](notes/hu2025kuitest.md)比较控件预期与单步响应。VisionDroid已补核TSE2025正式发表与ICSE2026 Journal-first身份，全文证据仍注明所读arXiv版本。

### 怎样比较这些评测材料

- **离线图像与在线交互分开**：UI-Lens主要评价给定截图的识别；GUITestBench需要执行任务；ReFLAIR、SUDFinder还需要对应页面/设置状态。单凭图片检测分数不能比较导航或缺陷触发。
- **独立缺陷与派生任务分开**：GUITestBench的143任务来自26缺陷；重复任务不增加独立缺陷数。设置组合、同缺陷多截图也应分别计数。
- **明确预期和真值来源**：宽屏参照、设置保持关系、标准规则、人工标注、用户影响与模型裁判各有边界；模型运行时可见的信息与评测时提供的答案不可混淆。
- **比较相同预算**：状态更多可能伴随更长运行；Pass@3不能替代单次效果，估算并行时间不能替代实测。需要同时记录覆盖、触发、判错、误报与端到端耗时。

这些材料支持进一步研究**可达但尚未找到、暂时不可见、合法隐藏与真实缺陷如何区分**，不构成“视觉oracle或主动复核无人研究”的证明。

### 已定位但尚未完成全文记录的相邻工作

以下只作检索导航，不计入catalog已完成篇目，也不把官方摘要当成全文核实。获取与阅读状态截至2026-10-18。

| 候选 | 已核实的场合与主题线索 | 当前状态 |
|---|---|---|
| [Minuku](https://conf.researchr.org/details/ase-2025/ase-2025-industry-showcase/15/Minuku-Detecting-Diverse-Display-Issues-in-Mobile-Apps-with-Small-scale-Dataset) | ASE2025 **Industry Showcase**；微调视觉语言模型检测遮挡、缺失、溢出 | 尚未取得全文，不能当Research Track论文 |
| [AccessDroid](https://doi.org/10.1145/3797108) | FSE2026 Research；运行时语义树的读屏障碍 | 研究长文未取得，不用同工具5页演示稿替代 |
| [HTML5 canvas视觉oracle](https://link.springer.com/article/10.1007/s10664-026-10906-3) | EMSE，2026-07-16在线发表、2027卷期；绘图内容不在普通DOM中 | 已取得2025预印本，尚未全文整理或与新增作者/实验的期刊扩展稿核对，不把它简称为2025正式论文 |
| [GUITestScape](https://arxiv.org/abs/2605.29532) | 2026预印本；探索测试与显示/交互缺陷过程评价 | 已取得PDF，待全文记录；尚未确认正式录用，不作为已核实权威发表工作 |

## 记录规则

- **综述论文**：在 `field.md` 中建立独立表格，并链接到 `notes/` 的详细阅读笔记。
- **具体方向论文**：在 `catalog.md` 中按统一列完整记录，并链接到 `notes/`。
- **术语**：写进 `glossary.md`；笔记首次出现重要术语时用中文解释并链接。
- **原文证据**：笔记必须写出短英文原句、章节和 PDF 页码，不能只写“见第几页”。
- **事实边界**：作者原话、我们的归纳、我们的局限判断分开写；预印本与正式版、已知 bug 与新 bug、确认与修复分开记。
- **阅读状态**：`full` 表示已核对全文关键主张和证据；`checked` 仅在另一位合作者对照原文复核后使用。

## `catalog.md` 的列

具体方向论文统一使用以下列，版式与 [examples/](examples/README.md) 保持一致：

| 列 | 写什么 |
|---|---|
| 论文 | 名称、简称和详细笔记链接 |
| 年份 · 场合 | 正式发表信息；未核实就明确标注 |
| 第一作者单位 | 以所读版本或正式页面为准 |
| 三问 | 主要回答“测什么 / 怎么知道错了 / 测得够不够”中的哪一问 |
| 动机 | 旧方法在哪个具体场景失效 |
| 贡献 | 作者真正新增的研究产物，不能把普通实验自动算作贡献类型 |
| 方法 | 输入、关键模块、执行与输出 |
| 实验与关键数字 | 数据、比较对象、预算、指标和主结果 |
| 作者承认的不足 | 论文明确写出的限制 |
| 还没解决的问题 | 我们根据证据作出的限定，不能冒充作者结论 |
| 和我们的关系 | 在同一格中分别写“与 UX testing 的关系”和“与 GUI testing 的关系”：前者结合 UX-Agent 的缺陷检测与主动验证链路，后者说明论文补上探索、执行、oracle、维护或评测的哪一环 |
| 论文叙事（问题→方法→证据） | 先交代研究问题及旧方法为什么不足，再说明核心方法与实验如何支持结论；这一格应能脱离同一行其他内容单独阅读 |

## 分类字段

分类字段保留在每篇笔记末尾，便于后续统计，不要求读者先学会标签才能读表。

| 位置 | 允许的值 |
|---|---|
| door | `gui` `code` `both` `survey` |
| question | `generate` `execute` `oracle` `adequacy` `maintain` `eval` `repair` |
| contribution_type | `problem` `empirical` `method` `benchmark` `harness` `replication` `survey` |
| platform | `android` `ios` `web` `desktop` `unit` `repo` `multi` |
| method | `random` `search` `rl` `llm-prompt` `multi-agent` `trained` `static-analysis` `hybrid` `benchmark` |
| oracle_type | `crash` `assertion` `metamorphic` `differential` `spec` `llm-judge` `human` |
| bug_type | `crash` `functional` `display` `flaky` `security` |
| venue_type | `conference` `journal` `workshop` `journal-first` `preprint` `thesis` |
| era | `classic` `llm` |
| priority | `must` `should` `maybe` |
| read_depth | `unread` `abstract` `full` |
| status | `reading` `extracted` `checked` |

字段不适用、证据不足或现有枚举无法准确表达时可以留空，并在笔记中说明实际情况；不要把浏览器严重日志强行标成 crash，也不要把单一电视平台标成多平台。

`question` 第一项是主任务。使用了 benchmark 不等于贡献了 benchmark；有代码不自动等于贡献了 harness；做了常规对比实验不自动等于 `empirical`。修测试脚本归 `maintain`，修被测程序归 `repair`。

## 新增论文

1. 先查 `catalog.md` 与 `field.md`，避免重复。
2. 复制 [notes/_template.md](notes/_template.md)，文件名使用 `第一作者姓年份关键词.md`。
3. 按论文类型更新 `catalog.md` 或 `field.md`；填写前先对照 [examples/](examples/README.md)。
4. 检查链接、数字分母、版本和原文摘录后再提交。
