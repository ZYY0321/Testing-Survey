# {论文名称：用一句中文说明研究问题}

## 一段话介绍

先回答：它研究哪个 GUI 测试问题、怎样解决、用什么证据评价。让没读过论文的人能理解；不要先堆标签或模型名字。综述说明它怎样组织研究，而不是套成新测试器。

## 关键术语与阅读前提

只解释本篇真正需要的词。首次出现写中文含义与用途，链接 ../glossary.md 或 ../field.md；作者缩写回原文核对。没有可靠全称不猜。

## 动机

按作者的问题写清“现有方法在什么情况下遇到什么障碍”。给出必要原文位置；自编 GUI 例子标注为教学例子，不冒充论文案例。

## 贡献

按作者编号或明确写“我们的归纳”，解释实际新增什么。贡献类型与具体贡献分开；有实现、用了数据集或做了实验不自动意味着 harness、benchmark 或 empirical。

## 方法

先用连贯中文说明整体，再拆成流程：

1. 输入是什么、怎样表示？区分源码、layout、控件树、截图、需求。
2. 中间模块怎样处理，模型具体收到什么？
3. 动作怎样选择、由谁执行、失败如何反馈？
4. 判定依据来自哪里、哪些观察能支持 bug 报告？
5. 何时停止，最终产物是什么？

用一个完整例子或流程图把模块连接起来。例子来源写清。综述则说明检索、筛选、抽取、分类与综合，不填写假造的执行器。查看参考文献之后的附录。

## 核心技术 / 综述主题详解

这节是内容主体，不能只给分类表。

综述：按论文主要主题分别写研究问题、输入/输出、技术分支、代表工作具体怎样服务该问题、GUI 例子、评测与局限。允许归纳和联系，不逐段翻译；工具只列名字不算展开。不将未读原始工作的算法/数字补猜成确定事实。

方法论文：展开各模块的字段、规则、状态更新与反馈，说明原文案例怎样体现设计，以及消融/实验对应哪项主张。教学解释帮助理解机制，不能代替记录论文实际设计。

交付前检验：没读过原文的合作者是否能根据笔记说明“论文具体做了什么、有什么研究价值、证据支持到哪里”？若只能复述主题名称或模型名字，笔记还没完成。

## 实验

先解释每项实验想回答什么，再记数据与数字：

- 数据用途、来源、规模、筛选条件、已知/新 bug 和真值。
- 比较对象：baseline 是什么；消融移除/替换什么。
- 环境、预算、重复次数、均值/取最好、模型配置和人工准备。
- 指标：解释计算对象/分母与单位，不只列英文名。
- 结果：主数字、百分比/百分点、能支持的结论与支持不了的结论。
- 新缺陷：报告/确认/修复分开记。
- 原文数字不一致：保留计数及版本疑点，不悄悄替作者修正。

综述的论文统计不是工具性能；编码一致性不是检测准确率。

## 局限

论文承认的：注明章节。

我们的观察：给出证据或限定语，不冒充作者原话，不凭旧材料宣称当前没人解决。

## 和我们课题的关系

说明本篇为 GUI 探索、执行、oracle、充分性、维护或评测提供什么。若谈后续方向，明确它是待核查的问题假设，不是已验证创新点。略去与 GUI 无关的背景。

## 原文摘要

见所读版本 PDF 的具体页，或在允许范围内摘录；不能把自己的转述标成原文摘要。

## 原文摘录

选短而有信息的原文，优先问题、机制、判定和实验协议。每条给章节与所读版本页码；不能只摘两个标题充作证据，避免大段复制。

> 短原文
> — Sec / Appendix，PDF 页码（或注明印刷页码）。

## 所读版本与核对范围

所读 PDF/全文来源、版本、日期；实际读到哪些正文与附录、视觉核对哪些关键表；是否对照正式版、是否复现。full 不是全部引文都核验，extracted 不是用户已核对。缺失数据按事实标明。

## 书目与分类（记录用）

<details>
<summary>展开标题、作者、版本和标签</summary>

- title:
- authors:
- year:
- era: classic | llm
- venue:
- venue_type: conference | journal | workshop | journal-first | preprint | thesis
- url:
- pdf_url:
- version:
- code_url:
- institution:
- aka:
- door: gui | code | both | survey
- question: generate | execute | oracle | adequacy | maintain | eval | repair
- contribution_type: problem | empirical | method | benchmark | harness | replication | survey
- platform: android | ios | web | desktop | unit | repo | multi
- method: random | search | rl | llm-prompt | multi-agent | trained | static-analysis | hybrid | benchmark
- oracle_type: crash | assertion | metamorphic | differential | spec | llm-judge | human
- bug_type: crash | functional | display | flaky | security
- benchmark:
- baselines:
- headline_result:
- one_liner:
- use_in_survey:
- concepts:
- priority: must | should | maybe
- read_depth: unread | abstract | full
- owner:
- status: reading | extracted | checked

</details>
