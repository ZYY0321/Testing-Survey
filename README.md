# AI for GUI Testing 文献调研

本仓库围绕 **GUI testing** 整理文献。阅读入口分为三层：

| 文件 | 用途 |
|---|---|
| [catalog.md](catalog.md) | 具体研究方向的单篇论文总表；每篇一行，适合横向比较 |
| [field.md](field.md) | 综述论文与领域地图；每篇综述单独成表，不把不同综述硬塞进同一行 |
| [glossary.md](glossary.md) | GUI testing 术语；阅读笔记遇到生词时查询 |
| `notes/<id>.md` | 单篇论文的完整分析、实验核对、原文摘录与版本说明 |
| [examples/](examples/README.md) | 大表写法示例与填写要求；新加一行前先对照这里 |

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
