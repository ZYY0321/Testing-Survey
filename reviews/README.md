# 审稿记录库

记录论文**投到哪、中没中、审稿人为什么给这个结论**。和 `notes/`（读论文内容）分开：这里关心的是"什么样的工作能中"，写自己的论文前对照用。

## 文件

| 文件 | 内容 |
|---|---|
| [gui-agent-ai-venues.md](gui-agent-ai-venues.md) | GUI 智能体 / 移动端智能体 / CUA，AI 会议（ICLR、NeurIPS、ACL）的投稿结果和审稿要点，2026-10-29 起 |
| `data/` | 从 OpenReview 拿到的原始记录（投稿状态字段、审稿分、审稿原文），一次一份，文件名带日期 |

## 目前能看出的规律（AI 会议，2026-10-29，样本小，只供参考）

按结果分组的平均审稿分（只算 OpenReview 上能看到的分）：

| 结果 | 篇数 | 平均分 |
|---|---|---|
| Oral | 1（ScaleCUA） | 7.3 |
| Spotlight | 6（4 篇有分） | 7.0 |
| 普通录用 | 23（16 篇有分） | 5.7 |
| 拒稿 | 13（11 篇有分） | 4.4 |
| 撤稿 | 9 | 3.7 |

**Spotlight / Oral 是哪些**：OS-ATLAS、AgentTrek、VideoGUI、AndroidControl（On the Effects of Data Scale on UI Control Agents）、OpenCUA、Spider2-V、ScaleCUA。它们几乎全是**社区能直接拿去用的开放资源**：大规模数据、benchmark、开源模型或基础框架。同时都带一个"新的认识"，比如数据规模和效果的关系、按层级分析失败在哪。没有一篇是"在已有流程上换个小技巧"。

**审稿人反复要求的三件事**：
1. **在公开 benchmark 上，和同一个基座比，并且要拉开差距。** GUI-ReWalk、GUI-Shepherd、SpiritSight、MobileGUI-RL 都因为这条被拒或撤稿。
2. **要证明数据真的让模型变好了，光有规模不够。** AutoGUI 有 70 万条，被拒；AgentTrek 只有几千条，是 Spotlight。
3. **新意要落在 GUI 本身的难点上。** 下面几类常被说成"已有流程换了名字"：
   - SFT 之后再做 RLVR（InfiGUI-R1、GUI-R1）；
   - 过程奖励 / 价值模型（GUI-Shepherd、VEM）；
   - 规划–执行–反思加失败恢复（VistaGUI、WorldGUI）；
   - 先探索、事后写指令（NNetNav、GUI-ReWalk）。

## 怎么维护

- 新看到一篇相关投稿，就在对应的表里加一行，写清投稿结果、审稿分和审稿要点，并附 OpenReview 链接。
- 原始记录用 OpenReview 的公开接口拉（`api2.openreview.net/notes/search`、`notes?forum=<id>`），存进 `data/`，文件名带日期。2026-10-29 晚上起，这个接口对部分网络要求先过人机验证，拉不下来时换个网络或用浏览器导出。
- 软件工程会议（ICSE、FSE、ASE、ISSTA）的审稿意见不公开，只能从录用论文本身总结，不放进这里的统计。
