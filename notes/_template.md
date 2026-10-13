# {id}

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

## 一段话介绍

用我们自己的话。先说它解决哪个测试任务，再说它怎么解决。字段中的候选值只保留适用项；`question` 和 `contribution_type` 第一项为主标签。信息不足时写「待核实」，不凭摘要补猜实验细节。

## 原文摘要

贴摘要，或写「见 PDF」。不要把摘要改写成自己的话之后还放在这一节。

## 动机

论文认为现有工作缺什么。用他们的问题，不要换成我们的问题。

## 贡献

按论文自己的编号写。每条一句。

`contribution_type` 记录贡献类型，下面记录具体贡献。注明分类依据所在的章节或贡献段；提出方法、复用评测集和提供实现代码应分别判断。

1.
2.
3.

## 方法

- 输入：
- 动作怎么选：
- 判定器：
- 何时停止：

## 实验

- 数据：
- 指标：
- 主数字：
- 对照：

## 原文摘录

>
> — Sec , p.

## 局限

论文承认的：

我们不买账的：

## 和我们课题的关系

这篇在综述里承担什么。它留下的、我们可以接的空档是什么。
