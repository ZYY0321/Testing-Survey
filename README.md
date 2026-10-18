# AI for GUI Testing 文献调研

本仓库围绕 **GUI testing** 整理文献。阅读入口分为三层：

| 文件 | 用途 |
|---|---|
| [catalog.md](catalog.md) | 具体研究方向的单篇论文总表；每篇一行，适合横向比较 |
| [field.md](field.md) | 综述论文与领域地图；每篇综述单独成表，不把不同综述硬塞进同一行 |
| [glossary.md](glossary.md) | GUI testing 术语；阅读笔记遇到生词时查询 |
| `notes/<id>.md` | 单篇论文的完整分析、实验核对、原文摘录与版本说明 |
| [examples/](examples/README.md) | 大表写法示例与填写要求；新加一行前先对照这里 |

## UX 问题驱动探索与 UX oracle：专题入口

以下按实际研究作用组织，重点覆盖2025–2026年工作。**到达目标页面、触发问题、主动复核和最终判错是不同任务**；通用导航成功率不能直接作为UX缺陷发现效果。全部具体机制、数据、版本及局限仍以单篇笔记为准。

| 阅读主题 | 论文与发表场合 | 主要值得比较的机制 | 任务边界 |
|---|---|---|---|
| 候选异常驱动的主动复核 | [ReFLAIR](notes/he2026reflair.md)，FSE2026 Research | 宽窄布局对应；对疑似缺失项滚动、展开菜单后重判 | 响应式Web信息/点击功能丢失，依赖DOM，不是所有原生端视觉缺陷 |
| 设置变化引发的显示问题 | [SUDFinder](notes/huang2026sudfinder.md)，TSE2026 | 系统设置变化、页面触达与显示异常判定结合 | 重点是设置相关显示缺陷，所读作者版与正式身份分开记录 |
| 测试目标感知的状态探索 | [A11yScan](notes/zhang2025a11yscan.md)，ICSE2025 Research | 控件状态组合、关键页重访，再用上下文复核规则警告 | 非LLM可访问性方法；不是按视觉异常概率自适应搜索 |
| 定向任务中的主动检查 | [GUITester](notes/gao2026guitester.md)，Findings ACL2026 | 任务中插入边界/替代路径/状态检查，分离异常发现与归因 | Findings而非ACL主会；UX指交互流程，尚未覆盖布局重叠和截断 |
| 跨浏览器视觉差异的语义筛查 | [XBIDetective](notes/grewal2026xbidetective.md)，ICSE2026 SEIP | 对跨浏览器截图差异作视觉语义判断 | 工业实践论文；不是全站探索器，不与单屏显示基准直接排名 |
| 动态内容的过程型oracle | [TIMESTUMP](notes/mehralian2025timestump.md)，ICSE2025 Research | 动作前中后控件、事件和焦点判断读屏障碍 | 不读取像素判断遮挡，不是视觉布局检测器或风险搜索器 |

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
| [TouchAlign](https://conf.researchr.org/details/fse-2026/fse-2026-industry-papers/56/What-You-See-Is-Not-What-You-Tap-Detecting-Misalignments-between-Visual-and-Interact) | FSE2026 **Industry Papers**；比较视觉边界与实际点击响应边界 | 研究主题直接相关，尚未取得全文 |
| [Minuku](https://conf.researchr.org/details/ase-2025/ase-2025-industry-showcase/15/Minuku-Detecting-Diverse-Display-Issues-in-Mobile-Apps-with-Small-scale-Dataset) | ASE2025 **Industry Showcase**；微调视觉语言模型检测遮挡、缺失、溢出 | 尚未取得全文，不能当Research Track论文 |
| [GenA11y](https://doi.org/10.1145/3729371) | FSE2025 Research；语义可访问性判定，也是ReFLAIR的对照 | [作者PDF](https://seal.ics.uci.edu/publications/2025_FSE_GenA11y.pdf)已取得，待全文记录 |
| [A11yNavigator](https://doi.org/10.1109/ASE63991.2025.00159) | ASE2025 Research；屏幕阅读器的网页导航障碍 | [作者PDF](https://ics.uci.edu/~seal/publications/2025_ASE.pdf)已取得，待全文记录；不混用同名学位论文 |
| [AccessDroid](https://doi.org/10.1145/3797108) | FSE2026 Research；运行时语义树的读屏障碍 | 研究长文未取得，不用同工具5页演示稿替代 |
| [DroidPalette](https://doi.org/10.1109/ASE63991.2025.00169) | ASE2025 Research；颜色可访问性刻画与修复，主要任务为repair | [作者PDF](https://huaxunhuang.github.io/src/ase25.pdf)已取得，待全文记录与版本差异核对；暂非探索主线 |

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
