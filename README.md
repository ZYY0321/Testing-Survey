# AI for Testing 文献表

两人一起填。短字段进 `catalog.csv`，方便筛选。动机、贡献、原文摘录进 `notes/<id>.md`，避免一个格子里贴整页论文。`catalog.csv` 目前只有表头。

`catalog.csv` 用 UTF-8 BOM，Excel / Numbers 可以直接打开。改完用表格软件导出时保持 UTF-8。

## 怎么分工

每篇论文只有一个 `owner`。另一人读完把 `status` 改成 `checked`，并在笔记末尾写不同意的地方。不要两个人同时改同一行。

状态只用这几个词：`reading`、`extracted`（笔记填完）、`checked`（另一人核对过摘录）。

`read_depth` 只用：`unread`、`abstract`、`full`。摘要级的句子不能写成「论文证明了」。

## 表里这些词不要自己发明

多值用 `|` 连接，不要用逗号。

| 列 | 允许的值 |
|---|---|
| door | `gui` `code` `both` `survey` |
| question | `generate` `oracle` `adequacy` `eval` `repair` |
| platform | `android` `ios` `web` `desktop` `unit` `repo` `multi` |
| method | `random` `search` `rl` `llm-prompt` `multi-agent` `trained` `static-analysis` `hybrid` `benchmark` |
| oracle_type | `crash` `assertion` `metamorphic` `differential` `spec` `llm-judge` `human` |
| bug_type | `crash` `functional` `display` `flaky` `security` |
| venue_type | `conference` `journal` `workshop` `journal-first` `preprint` `thesis` |
| priority | `must` `should` `maybe` |

`question` 对应测试的三问：跑什么（`generate`）、怎么知道错了（`oracle`）、怎样算测够（`adequacy`）。`eval` 是评测协议本身，`repair` 是修 bug。一篇论文通常只主打一问，其余写进笔记，不要全标上。

## 每列记什么

必填，短：

- `id`：`第一作者姓年份关键词`，小写，作为笔记文件名。形式如 `author2026keyword`
- `title` `authors` `year` `venue` `venue_type` `url`
- `institution`：第一作者单位。其余单位差异大时写进笔记
- `code_url`：没有就空着，不要写「未开源」占格子
- `aka`：同一篇的别名、改名、arXiv 标题和会议标题不一致
- `door` `question` `platform` `method` `oracle_type` `bug_type`
- `benchmark`：数据集或应用数量，带数字
- `baselines`：论文真正对比了谁
- `headline_result`：一个最想引用的数字，带条件（在什么数据上、相对谁）
- `one_liner`：我们自己的一句话，不复述摘要
- `use_in_survey`：这篇在综述里的角色。铺垫、基线、对照、还是留下的空档
- `priority` `read_depth` `owner` `status` `note`

长文只进笔记：

- 一段话介绍
- 原文摘要
- 动机
- 贡献
- 方法：输入是什么、下一步怎么选、靠什么判定、何时停止
- 实验：数据、指标、主数字、对照
- 原文摘录
- 局限，包括论文自己写的，和我们不买账的
- 和我们课题的关系

## 原文摘录怎么记

每篇 2 到 4 条。每条都要能指回原文位置。

```text
> 原文句子
> — Sec 3.2, p.5
```

优先摘这四类句子：问题定义、他们自己说的贡献、判定器是什么、主结果那一句。不要摘背景综述。摘录用英文原文，不要先翻译再当原文。

`read_depth` 还是 `abstract` 时，摘录栏写「待从 PDF 核对」，不要凭记忆补句子。

## 故意不设的列

引用量、基金号、页数、影响因子。这些会过期，也不帮我们决定这篇怎么用。机构只记第一作者单位，通讯作者如果不是第一作者，写在笔记里。
