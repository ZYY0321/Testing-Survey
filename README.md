# AI for Testing 文献综述

用 Markdown。CSV 装不下摘录，二十六列的表在 GitHub 上也没法读。

| 文件 | 是什么 |
|---|---|
| `field.md` | 领域本身。一个概念一节，定义写在节里 |
| `notes/<id>.md` | 一篇论文。书目、分类、动机、贡献、摘录都在这一篇里 |
| `catalog.md` | 窄索引，只有 id、标题、年、场合、哪一问、笔记链接 |

概念（什么叫判定器、什么叫探索式测试）不写成论文。经典工作仍是一篇论文笔记，`era` 标成 `classic`，再写进 `field.md` 里该概念的「挂上的论文」。

## 怎么改

领域：两人认领不同的概念，把自己的名字写进该节的「负责人」。不要同时改同一节。

论文：复制 `notes/_template.md`，文件名用 `第一作者姓年份关键词`，例如 `author2026keyword`。同时在 `catalog.md` 加一行。另一人核对摘录后，把 `status` 改成 `checked`。

`read_depth` 只用 `unread`、`abstract`、`full`。摘要级的句子不能写成「论文证明了」。

## 这些词不要自己发明

多个值用空格隔开。

| 位置 | 允许的值 |
|---|---|
| door | `gui` `code` `both` `survey` |
| question | `generate` `oracle` `adequacy` `eval` `repair` |
| platform | `android` `ios` `web` `desktop` `unit` `repo` `multi` |
| method | `random` `search` `rl` `llm-prompt` `multi-agent` `trained` `static-analysis` `hybrid` `benchmark` |
| oracle_type | `crash` `assertion` `metamorphic` `differential` `spec` `llm-judge` `human` |
| bug_type | `crash` `functional` `display` `flaky` `security` |
| venue_type | `conference` `journal` `workshop` `journal-first` `preprint` `thesis` |
| era | `classic` `llm` |
| priority | `must` `should` `maybe` |
| status | `reading` `extracted` `checked` |

`question` 对应测试的三问：跑什么（`generate`）、怎么知道错了（`oracle`）、怎样算测够（`adequacy`）。`eval` 是评测协议本身，`repair` 是修 bug。一篇论文通常只主打一问。

## 领域文档里先有哪些概念

定义是空的，一起写。先把这些节写扎实，再大量收论文。

- 三问：测什么、怎么知道错了、怎样算测够
- 判定从哪来：崩溃、断言、蜕变关系、两个实现互相比、模型当裁判
- GUI：脚本测试、探索式测试、GUI 探索、界面状态、崩溃 / 功能 / 显示缺陷
- 代码侧只留两个：单元测试、变异测试

## 论文笔记里记什么

开头那一块是短字段：题目、作者、年份、场合、链接、机构、分类、主数字、一句话、在综述里的角色。

下面几节才是长文：一段话介绍、原文摘要、动机、贡献、方法、实验、原文摘录、局限、和我们课题的关系。

摘录每篇 2 到 4 条，每条都要能指回原文位置。

```text
> 原文句子
> — Sec 3.2, p.5
```

优先摘这四类句子：问题定义、他们自己说的贡献、判定器是什么、主结果那一句。摘录用英文原文。`read_depth` 还是 `abstract` 时，摘录写「待从 PDF 核对」。

`code_url` 没有就空着。机构只记第一作者单位。引用量、基金号、页数、影响因子不记。
