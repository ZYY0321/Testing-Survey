# UI 显示缺陷：相关论文与资源解释

本文件夹聚焦文字截断、裁切、重叠等显示质量测试及其支撑方法。每篇 note 独立说明问题、输入、处理过程、实验依据、限制和资源可获取性。它们是原创解释，不是论文译本；证据深度在各篇开头说明，未取得全文的记录不作为精读成果。

## 直接相关的方法与数据

| 文献 | 重点 | 笔记 |
| --- | --- | --- |
| SUDFinder，TSE 2026 | 系统设置诱发显示错误；XML 布局渲染、关系型判定、问题报告数据 | [详细解释](02-related-notes/SUDFinder.md) |
| Nighthawk，TSE | 视觉检测、定位与增强数据；本地工具包取得条件 | [详细解释](02-related-notes/Nighthawk.md) |
| Owl Eyes，ASE 2020 | 早期截图检测基线；真实截图与图像增强的区别 | [详细解释](02-related-notes/Owl-Eyes.md) |
| UIS-Hunter，ICSE Demo 2021 | 设计规范驱动的界面问题检查 | [详细解释](02-related-notes/UIS-Hunter.md) |
| Woodpecker，ICSE SRC 2022 / 2023 扩展材料 | 视觉框到控件树、源码与修复模板；两版数据分开 | [详细解释](02-related-notes/Woodpecker.md) |
| TouchAlign，FSE Industry 2026 | 主动点按探测真实触摸边界，与可见边界比较 | [详细解释](02-related-notes/touchalign.md) |

## 评测、任务定义与旁读

| 文献或资源 | 用途 | 笔记 |
| --- | --- | --- |
| GUITestScape / GUIJudge | 拆开页面到达、交互触发和异常判定，分析测试失败原因 | [详细解释](02-related-notes/guitestscape-guijudge.md) |
| GUI Testing Arena / UIDefectMocker | 区分真实运行缺陷、程序注入和截图合成 | [详细解释](02-related-notes/gui-testing-arena-uidm.md) |
| AutoConsis，ICSE SEIP 2024 | 跨页面业务信息一致性及比较前提；与局部视觉截断区分 | [详细解释](02-related-notes/autoconsis.md) |
| UIClip，UIST 2024 | 界面设计质量表示学习，非截断判错器 | [详细解释](02-related-notes/uiclip.md) |
| UICrit，UIST 2024 | 人工设计评论及区域标注，非真实布局缺陷树数据 | [详细解释](02-related-notes/uicrit.md) |
| MLLM as a UI Judge，2025 | 主观设计偏好评价及其与客观缺陷检测的边界 | [详细解释](02-related-notes/mllm-as-ui-judge.md) |
| SoK: An Exhaustive Taxonomy of Display Issues，IUI 2024 | 显示问题分类及工具、数据覆盖图谱 | [详细解释](02-related-notes/sok-display-taxonomy.md) |

## 使用边界

截图有问题框，不表示同时提供运行时控件树；读取树辅助图像合成，也不表示应用布局真实改变。研究交互取证时，还需确认应用可运行、操作可重放、截图与树同步、正常对照及目标内容可恢复性。

单屏分类、缺陷框定位、应用级发现、开发者确认和修复数使用不同分母，不能横向拼成排行榜。工具在论文发表时公开，也不保证当前下载入口仍可用。

本批内容单独组织，未修改主仓库 catalog。论文译文、原文 PDF 和个人阅读安排不在本文件夹中发布。
