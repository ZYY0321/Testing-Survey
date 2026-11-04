类型：材料 ｜ 归属：2026-11-04，给数据构造 D2 参考 ｜ 状态：仅供参考

# 以前是怎么构造截断的（代码工坊，仅供参考）

**这只是之前为验收测试临时造的 6 处截断，手法很单一，仅供参考。** 新的数据集请按 [截断论文_ISSTA2027.md](../../截断论文_ISSTA2027.md) D2 的注入算子来做，也欢迎提别的方案。

## 用的是哪个 App

华为官方开源示例"HMOS 代码工坊"（Apache 2.0）：`gitcode.com/HarmonyOS_Samples/sample_in_harmonyos`，基于提交 `bcc6b03`（2026-06-11）。整个工程连编译产物有 6.4 GB，这里只放改动和结果。

还原：

```bash
git clone https://gitcode.com/HarmonyOS_Samples/sample_in_harmonyos.git
cd sample_in_harmonyos && git checkout bcc6b03
git apply 截断注入.patch     # 本目录下的补丁
```

然后按上游 README 第五节运行：DevEco Studio 打开工程、等 Sync 完成，运行入口选 phone（折叠屏也选 phone），签名后运行。不需要我们本地的整个工程（6.4 GB，其中 3.8 GB 是 `submodules/` 里集成的其他示例 App，2 GB 是编译产物）：不下载那些示例也能运行本体，只是"样例"模块里点不进具体示例；要全量功能，运行仓库自带的下载脚本 `hmosword-build`。

## 本目录有什么

| 文件 | 内容 |
|---|---|
| `截断注入.patch` | 全部源码改动（8 个文件，`.ets` 和一个 `.css`） |
| `构造缺陷位置.txt` | 6 处截断在 App 里的位置 |
| `样例/g01`–`g06` | 双折叠真机上采到的截图（`.jpg`）、布局树（`.xml`）、人工标注（`.json`，Labelme 格式，标签是"截断-文字 / 截断-组件"，`flags` 写被截的是哪条边） |

## 6 处分别在哪（按顺序对应，请核对截图）

| 样例 | 位置 | 改了哪个文件 | 标注 |
|---|---|---|---|
| g01 | 我的 → 内容上新，Sample 卡片底部 | `features/mine/.../component/NewContentItem.ets` | 截断-文字，下边 |
| g02 | 我的 → 设置，页面标题 | `features/mine/.../view/SettingView.ets` | 截断-文字，上边 |
| g03 | 我的 → 设置 → 关于，"HMOS代码工坊"字样 | `features/mine/.../view/AboutView.ets` | 标注文件里没有框（布局核对也找不到裁切边界） |
| g04 | 我的 → 用户体验调研，提交按钮 | `features/mine/.../view/QuestionnaireView.ets` | 截断-组件 + 截断-文字，下边 |
| g05 | 样例 → 中间分类条最右边 | `features/abilitycommon/.../view/HomeView.ets` | 截断-文字，左边和右边 |
| g06 | 实践 → 文章下滑后顶部标题 | `features/exploration/.../view/ArticleDetailView.ets`、`ArticleWebComponent.ets`、`article.css` | 截断-文字，上边 |

注意：当前工作区里 `QuestionnaireView.ets` 的两行改动（`.translate({ y: 20 })` 和 `.clip(true)`）已被注释掉，g04 的截图是注释掉之前采的。

## 当时的手法

几乎都是同一招：**把文字或按钮用 `translate` / `offset` 推出父容器的边界，再让父容器 `clip(true)` 把超出的部分裁掉。** 例如：

```ts
Text(...)
  .translate({ y: -55 })   // 往上推
// 父容器
.clip(true)                // 裁掉推出去的部分
```

## 为什么只供参考

- 真实 App 里的截断很少是"位移后被裁"，更常见的是：固定宽高装不下变长的内容、限制了行数却没加省略号、某个屏宽下布局没适配、系统字号放大。按这一招造的数据，检测方法可能只学会识别"整体错位"，到真实 App 上不灵。
- 只有真截断，没有成对的难负例（省略号加详情、横向列表露半张卡片、轮播露相邻卡片……），测不出误报。
- 只有 6 处，都在双折叠上采，没有单屏、双屏对照。

新方案见 [截断论文_ISSTA2027.md](../../截断论文_ISSTA2027.md) D2：7 种真缺陷、5 种成对难负例，一个 App 的注入放进同一个构建、用开关逐个打开，每处写真值记录。
