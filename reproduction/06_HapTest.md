# HapTest：鸿蒙源码组30项实测、覆盖率审计与探索失败案例

更新：2026-11-02。全部实验已停止；本文是HapTest唯一综合复现报告。论文学习见 [HapTest note](../notes/liu2025haptest.md)。

## 1. 完成范围与阅读入口

**已完成10个开源应用×3次的30个冻结计划项，并保留3份商业应用历史运行；不是完整复现全部RQ，也未得到独立确认的新UX缺陷。** 最终开源组为 `open-rq1-transport`：29轮真正进入600秒计时；其中17轮exit0且预算结束一致、4轮原生ExitEvent提前退出、8轮计时内exit1，另1项在准备阶段ECONNRESET；0看门狗超时。“30项终态”不等于30次全部跑满或全部通过。

优先打开：[开源组逐轮指标CSV](http://127.0.0.1:8770/harmony-file?path=open-rq1-transport/run-metrics.csv)、[三重复描述统计](http://127.0.0.1:8770/harmony-file?path=open-rq1-transport/repeat-descriptive-summary.json)、[停止与范围完整说明（本地文件）](D:/Projects/实验室/Harmony-Reproduction-Handoff-20261102/交接说明_请先阅读.txt)。[源码与工具目录（本地文件）](D:/Projects/实验室/Harmony-Reproduction-Handoff-20261102/workspace/HapTest)及 [最终运行目录（本地文件）](D:/Projects/实验室/Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport)保留原件。旧 `open-rq1-full` 两次Wechat超时和一次准备失败是旧失败接口组，不与最终组池化，也未被删除。

## 2. 原论文在做什么

HapTest为OpenHarmony搭建可复用动态测试框架。给定源码工程，它插入代码计数，构建HAP，再在设备上执行点击、长按、滚动、输入等事件；采集截图、控件树、覆盖与系统日志，更新页面转移图PTG，再由策略选下一步。没有源码的商业App仍可执行与取图，但不能凭空获得全源码覆盖分母。

这是一套执行、观察、建图、插桩基础设施。论文开源组用random随机策略，商业组用greedy_dfs贪心深度优先；不用大模型。当前上游README的可选LLM/static_guided接口是后来代码能力，不能反向写成论文原配置。[官方仓库](https://github.com/SMAT-Lab/HapTest)可以核查这些选项；[作者论文PDF](https://jacquesklein2302.github.io/papers/2025-FSE-Industry2025-HapTest.pdf)给出原实验协议。它发表在FSE2025 Industry Papers、FSE Companion，不是主研究赛道或仅凭Companion推定的Demo。

### 论文与本次的差别

| 维度 | 论文 | 本次 |
|---|---|---|
| 设备系统 | Mate60，OpenHarmony5.0.0 | Mate XT，系统参数6.1.0.29/API23 |
| 开源应用 | 10款，600秒×3，random | 10款×3计划项，29进计时；保留早停、失败及缺失 |
| 商业应用 | 20款，3600秒×3，greedy_dfs | 京东、QQ音乐、淘宝3份历史终态；其他未跑项由用户取消 |
| 开源源码 | 作者论文版本 | 真实源码构建/插桩/签名；两款按论文日期选最近旧提交，仍不保证等于作者SHA |
| 模型 | 没有LLM策略 | 原random/greedy_dfs，无本次模型API调用 |
| 核心输出 | 覆盖、PTG、稳定性故障 | 原覆盖文件、原生转移、图、日志，加独立只读审计 |
| 缺陷判断 | 崩溃、Freeze日志 | 没有已独立确认新缺陷；工具异常/旧faultlog不直接算App bug |

本次版本为HEAD `45c85df005b9f68ff16a7a8d7b5193c533f5f3dd`。原生agent1.2.2与FuncDroid的agent1.1.0共享设备临时路径，因此按序使用设备，不能并发混用。

## 3. 从构建到真实运行：处理了什么，保留了什么

源码组包含Wechat_HarmonyOS、Msea_HarmonyOS、CanvasComponent、SwiperArkTS、VideoPlayer、OxHornCampus、MultiShopping、Healthy_life、OhBill、DistributedContacts。分别取得源码、建立独立ASCII构建副本、适配新SDK、BJC插桩、签名、安装，冻结HAP hash及配置后运行。两款选旧版本在首次有效试验前确定，没有看结果后挑版本；源码与构建副本是不同证据，不能互相覆盖。

构建副本对齐Hvigor/schema/API配置，补必要类型断言/注解并保留JavaScript擦除审计；独立SDK在UI语法变换后注册原BJC transformer、显式full模式。transformer与模板保留原字节，系统SDK未动。这个桥接是平台迁移，不能宣称构建二进制与作者完全相同；原BJC按函数名合并计数等疑点未修。

Windows接口问题最初使有效探索受阻。最终只改3处受版本控制源码：①hdc在Windows直接spawn，在远端shell边界合并参数；②event_action.ts记录toContentSig时改读transition.to，策略使用对象不变；③hypium_rpc.ts抛Invalid frame header前加前64字节诊断，原异常仍抛。见 [记录与传输patch（本地文件）](D:/Projects/实验室/Harmony-Reproduction-Handoff-20261102/workspace/preparation/haptest-recording.patch)。没有改随机排序、贪心打分、回溯、滚动参数或崩溃规则。

当前设备Ark inspector缺connected通知，依据官方源码/真实帧实现独立主机握手适配，保留原失败接口组。静态工具的Windows路径比较统一斜杠；Healthy_life离线报表目录别名处理解决ENOENT，原DOT不改。适配成功只说明接口能传输与产生真图，不等于探索策略或图的语义已经正确。

运行结束后按原结果留存，没有把失败的轮次补跑到成功，也没有为漂亮曲线修正下降值、按最近计数填满预算末尾。用户后来取消后续商业组和重放，本次整理不恢复实验。

## 4. 覆盖率数值及其真正分母

### 逐应用三次运行的终态与产物统计

下表各单元按r1/r2/r3排列；墙钟包含准备与断开收尾，不能当有效探索时长。分类依据独立[退出审计](http://127.0.0.1:8770/harmony-file?path=open-rq1-transport/termination-audits.json)。

| 应用 | r1/r2/r3终态 | 墙钟秒 | 转换文件数 | 运行计数文件数 |
|---|---|---|---|---|
| Wechat_HarmonyOS | 预算终态 / 预算终态 / 预算终态 | 615.234 / 610.265 / 620.250 | 48 / 54 / 46 | 12 / 21 / 8 |
| Msea_HarmonyOS | 预算终态 / 预算终态 / 原生早退 | 615.266 / 615.250 / 440.203 | 60 / 52 / 57 | 30 / 18 / 44 |
| CanvasComponent | 预算终态 / 原生早退 / 原生早退 | 615.250 / 385.156 / 450.188 | 121 / 76 / 89 | 121 / 76 / 89 |
| SwiperArkTS | 计时内失败 / 计时内失败 / 计时内失败 | 550.234 / 185.093 / 225.109 | 102 / 32 / 41 | 102 / 32 / 41 |
| VideoPlayer | 预算终态 / 预算终态 / 预算终态 | 615.297 / 620.250 / 611.125 | 84 / 94 / 66 | 65 / 83 / 41 |
| OxHornCampus | 预算终态 / 预算终态 / 预算终态 | 620.234 / 620.250 / 620.265 | 106 / 106 / 106 | 106 / 106 / 106 |
| MultiShopping | 计时内失败 / 准备失败 / 计时内失败 | 180.078 / 10.000 / 435.203 | 31 / 0 / 76 | 31 / 0 / 76 |
| Healthy_life | 计时内失败 / 计时内失败 / 计时内失败 | 100.063 / 65.031 / 60.047 | 18 / 11 / 11 | 6 / 0 / 0 |
| OhBill | 原生早退 / 预算终态 / 预算终态 | 145.062 / 610.265 / 610.297 | 27 / 112 / 113 | 26 / 112 / 113 |
| DistributedContacts | 预算终态 / 预算终态 / 预算终态 | 610.313 / 620.297 / 610.312 | 48 / 53 / 49 | 9 / 17 / 9 |

30项合计1889个原生转换文件、1500份运行计数文件；墙钟求和13691.887秒（约3.80小时）仅为各计划项消耗的描述，不是30轮应有18000秒预算已全部有效跑完。3778个预期from/to图槽位=1889×2，其中3762有可用引用、16原生缺路径。开源策略本次LLM调用为0。商业历史单列，不加入这些合计。



下面是**原生最终报告中已加载源码的行覆盖百分比**的有值观测均值±样本标准差。不是严格600秒终点值，也不是全源码的固定分母；缺失不填0。每轮原数与分母在CSV中。

| 应用 | 三轮行覆盖原值（%） | 有值均值±SD（%） | 解释 |
|---|---|---|---|
| Wechat | 28.6 / 33.4 / 28.6 | 30.20±2.77 | 分母1280/734/1280，不能简单当相同全源码比例 |
| Msea | 63.2 / 62.6 / 56.6 | 60.80±3.65 | r3原生提前退出；各轮加载分母不同 |
| Canvas | 88.3 / 88.3 / 88.3 | 88.30±0 | 相同最终比例不表示动作/时间完全相同 |
| Swiper | 92.8 / 91.6 / 92.3 | 92.23±0.60 | 三轮都因滚动参数错误提前终止 |
| VideoPlayer | 61.4 / 65.8 / 45.4 | 57.53±10.74 | r3分母339，其余904；并非同一固定分母的稳定差异 |
| OxHornCampus | 39.3 / 39.3 / 39.3 | 39.30±0 | 不能因SD0推断全功能已覆盖 |
| MultiShopping | 46.0 / 缺失 / 73.0 | 59.50±19.09，n=2 | r2准备失败，不是0覆盖 |
| Healthy_life | 21.6 / 缺失 / 缺失 | 21.60，n=1，无SD | r2/r3无目标观测及计数 |
| OhBill | 54.1 / 62.2 / 66.8 | 61.03±6.43 | 提前退出轮与预算轮混存，只作描述 |
| DistributedContacts | 31.3 / 28.5 / 28.5 | 29.43±1.62 | 低覆盖仍需结合实际页面和前置分析 |

[原有描述图及来源](assets/06-ef5eec89fe31.png)显示原值、SD、缺失和退出类型。它的用途是说明观测分布，不能拿十个均值组成同质排行榜或总体显著性。

### 为什么不能直接重画论文Figure4

论文Figure4横轴为事件执行步数，比较语句/函数/分支/UIAbility覆盖。本次原coverage经SerializeUtils后丢成空对象：3691个空coverage、71个无coverage槽位、0非空槽位。虽然独立cov文件存在，但不能证明它精确属于哪一次事件，不能按文件序号或最近时间把数值塞回事件。原生最终报告和严格600秒内最后计数分别留存；最新真实计数到预算末尾的时间差也逐轮保留。Wechat r1最后预算内计数为457.277秒，距末尾142.723秒，不能把它前填成600秒成绩。

论文描述开源行覆盖约40%–90%、UIAbility接近/达到100%。本次Wechat约30%、Swiper约92%只是观察差异，受系统、版本、退出、计数分母影响；没有理由把差距全部归因随机策略变差或“复现恢复率”。

## 5. 页面、UIAbility、路由、功能与UX条件：五种量不要混

UIAbility是鸿蒙应用组件，一个Ability可包含多个路由页；PTG运行签名还会把内容和弹层变化拆成节点。静态原图包含Entry、Component、对话框/测试节点等混合口径，不能直接作为完整功能分母。论文RQ2又按Ability聚合后比较动态图和静态图，与原始页面数并非同一粒度。

本次额外分开保存manifest声明Ability、源码注册路由、运行签名投影。10款共38个注册路由身份有三重复表：27轮存在可映射目标观测，另1准备失败、2无目标观测单列不可用。[逐路由重复表](http://127.0.0.1:8770/harmony-file?path=open-rq1-transport/registered-route-repeatability.csv)使“访问了哪几个”可核验，而不是只看数量。

**Wechat是数量掩盖差异的实证例子**：三轮都访问3个注册路由，但并集4、交集2，两两Jaccard为0.5/0.5/1.0。Jaccard指共同身份数/并集身份数。三轮计数都为3不代表访问的是同三页；合并三轮的4个也不能当每轮4个。它支持评测应记录身份和重复性，尚不能证明哪条遗漏路由一定有UX bug。路由分母来自源码可声明集合，不是人工穷尽的业务功能真值。

商业App没有源码插桩分母。论文Activity在Android上有意义，鸿蒙不能换个名称就称等价Activity覆盖；应额外定义可比较的业务功能和状态条件集合。

## 6. 有上下文的正反实例与明确结论

### 6.1 正例：能收集真实状态与可映射路由

Msea r1有60条转换、30次目标after观测、30次外部/非目标观测，61张唯一图可解码；目标注册路由3/6。它证明底层事件、图像、计数及源码路由之间能建立部分真实联系，也同时说明不能把60条转换全部当目标业务测试。VideoPlayer r2有94转换、83份目标运行计数，95张图；其中93条完成观测在预算内、1条是收尾，应保留时间边界。

### 6.2 可定位bad case：滚动动作参数使深层探索提前终止

Swiper三轮及MultiShopping两轮有观测运行中，原生滚动步长60超过47px组件的可滚动距离38px，引发框架异常退出。没有夹紧参数或增加重试。**可以明确结论：当前公开实现存在在该组件几何条件下无法继续探索的动作生成/执行缺口。** 不可结论：待测App滚动有bug、所有HapTest滚动都会失败、已证明漏掉某个真实UX问题。

此处对A/B特别有用：框架自身操作无效会让检测器既到不了更深内容，也无法用滚动作为“合法隐藏”的反证。需要先判定手势实际有效，再解释内容没出现。原件及逐图草稿见 [Swiper r1审阅包（本地文件）](D:/Projects/实验室/Harmony-Reproduction-Handoff-20261102/workspace/private-runs/full-trace-review/swiper-r1-full-20261102)及 [MultiShopping r1（本地文件）](D:/Projects/实验室/Harmony-Reproduction-Handoff-20261102/workspace/private-runs/full-trace-review/multishopping-r1-full-20261102)。

### 6.3 无目标观察不能当零覆盖或App crash

Healthy_life三轮耗尽框架重启次数，权限/启动页/背景状态混杂，r2/r3无目标观测与计数。应结论为探索未获得可用目标数据，根因尚不能归为App崩溃；不能把缺失覆盖填0，也不能将系统通知权限弹窗当新增业务页。[审阅包（本地文件）](D:/Projects/实验室/Harmony-Reproduction-Handoff-20261102/workspace/private-runs/full-trace-review/healthy-life-r23-full-20261102)保留这种失败。

### 6.4 原生正常退出仍可能没有完成预算

Canvas、Msea、OhBill部分轮原生ExitEvent提前结束，没有补满预算重跑。若比较固定预算发现量，应保留实际时长和退出机制，而不能只筛exit0轮或把早退看成全覆盖。观察多张重复图也不构成独立重放。

### 6.5 商业三份记录的实际边界

京东因原滚动步长异常中断，使用此前外部run归档，原退出码未知，不能补0；QQ音乐314转换、约19分钟原ExitEvent提前结束，exit0；淘宝6转换、约30秒RPC断连exit1，画面主要隐私同意/拒绝及退出外部状态，未到购物首页。系统已有的大量faultlog不是新触发缺陷证据，RPC错误也不是产品crash真值。商业组没有达到20应用×3，不能和FuncDroid四单轮形成公平效能排名。

## 7. 图像审阅做了多少，为什么不是UX准确率

29轮有图运行的1648张轮内去重图、3762个可用引用已有逐图Codex草稿，另16个from/to槽位原生未给截图路径、不能视觉判断；已给出的图引用本身未发现缺失。准备失败没有运行图。该数字说明审阅与原件关联范围，不表示1648个语义独立状态或人工确认样本；人类独立真值仍未建立。

原工具只做稳定性故障判定，没有自动文字截断/重叠/遮挡oracle，也没有完整折叠开合干预。画面路过UX风险状态可以标注为机会；要给“UX探索率”，必须预定义缺陷或条件分母并核实已触发。当前没有该分母，不能拿92%代码覆盖、100%单Ability覆盖或截图数替代UX探索率。

论文商业结果为20款中11款共26稳定性问题，表中13 ArkTS崩溃、10 C++崩溃、3 Freeze。它不是26个都经开发者修复的根因，更不是26个视觉UX问题。本次没有独立确认量，不能据此声称“未复现任何论文bug所以方法失效”。

## 8. 对A/B和论文叙事的用途

**A：测试用例生成/探索。** HapTest适合作为低模型成本的启发式基础对照。当前数据已经给出三个可写的实证观察：入口/路由数量掩盖身份差异；固定预算会被原生早停与执行参数异常截断；非目标App/系统页会膨胀未过滤转换数。方法改进应分别测动作有效率、目标状态比例、深层路由触达与重复间交集，而不是把所有问题合称覆盖不足。现有材料未证明新策略能增加真实缺陷，不能将bad case本身写成新方法收益。

**B：可靠UX验证。** 可借执行、截图/树与图的证据底座，配同一检测器作为控制变量。Swiper例子直接提示“执行了scroll事件”不等于“目标容器收到有效滑动”；当复核失败时应保留unknown，先排除滚动参数、错误容器或退出App，再判断真截断。若我们做受控故障注入，可在正常版本固定路由和容器身份，再植入不响应/内容不可达故障，分别评价触发与判定；这属于后续设计，尚未执行。

**研究写作定位。** 可引用为平台迁移pilot、执行可靠性与覆盖定义的动机案例；源码组有三重复描述数据，可以报告每轮值与缺失，尚不支持与FuncDroid跨工具显著性或UX检出优劣。增加图节点不是独立贡献，需证明身份抽象或前置恢复的改动减少哪些明确损失。HapTest论文已经讨论深层分支长尾，不应声称首次发现“Ability全覆盖但深层状态遗漏”。

## 9. 对我们项目目标的参考价值与不可直接借用指标

以下阈值属于我们自己的UX项目，不能据此判定他人论文合格与否。表中“未测”只说明本次证据不能代替我们的目标评测。

| 我们的目标 | 当前证据及结论 |
|---|---|
| 低级指令到页≥95%，高级≥80% | 原策略无这两种语言输入，本次未建立成对目标任务；不适用直接验收。可保留其自动探索轨迹作页面可达性背景 |
| 到页后正确发现UX问题≥95% | 无UX oracle及独立正常/异常真值；未测，不能用crash或代码覆盖替代 |
| 单步平均≤3s、最大≤5s | 没有同定义响应时间。预算/转换数混含等待、重启和收尾，不能替代单步延时；无需模型不表示必然达标 |
| 排除网络资源后，三层页面检测端到端≤10s | 没有固定三层目标、检测器及输入输出时间对；未测。Swiper参数错误属于应保留的执行失败，不能一律当外部资源错误剔除 |

后续评价需先固定状态及标签，再同时报导航成功、触发、检测precision/recall、动作耗时与端到端耗时。当前最清楚的结论是：HapTest已提供真实可分析的鸿蒙探索数据，但接口跑通、Ability入口触达和高局部代码比例，都不能证明UX条件完整或检测结果正确。


数据读取说明：文中示例图随报告保存，可在GitHub直接阅读；`127.0.0.1:8770`链接指向本机只读查看器，远程阅读者需按[总目录](catalog.md)的数据仓库说明下载对应项目并启动查看器。本地文件链接用于源码与准备记录溯源，不声称是在线资源。


## 本次实图UX专项复查（新看原像素，2026-11-02）

此前本轮代理实际新看只有京东water输入图1张、高德协议候选2张，共3次view_image；上文其他历史逐图草稿不应被误读为本轮已经全部重看。收到专项要求后先冻结代表样本，再逐对调用view_image查看原像素，核对原动作；没有调用设备、模型或补拍。本项目新看12对、24次原图展示，23个路径、23种SHA256内容。主样本固定每App r1首个目标→目标且前后原图均存在的事件，共10对；另取首个真实ScrollEvent的Swiper对图，再针对已知失败查看最后成功序列化事件对图。后两项是机制诊断，不能并入随机抽样。

账本：[本地冻结方案](../tmp/harmony-ux-review-20261102/plan.json)、[逐项新观察、动作与hash](../tmp/harmony-ux-review-20261102/review-ledger.json)。这两份是本地审计附件；主要结论和原图链接已全部写入本文，阅读不依赖另开总结文档。

### 按原动作核对的观察

| 样本 | 新实看前后画面与到达判断 | UX解释与边界 | 原图 |
|---|---|---|---|
| H-Wechat_HarmonyOS | 会话列表前后相同，多条消息以省略号结尾。 | ScrollEvent未见列表位移，也可能列表已完整容纳；预览省略不等于异常省略。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/cn.icheny.wechat_r1/2026-11-02-15-11-24/temp/screenCap_70239997166.png) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/cn.icheny.wechat_r1/2026-11-02-15-11-24/temp/screenCap_70246215643.png) |
| H-Msea_HarmonyOS | 虫部落页点击底栏通知后到通知页，中心有登录按钮。 | Tab导航可见，空列表有登录前提，不能当内容未显示缺陷。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.eternaljust.msea.huawei_r1/2026-11-02-15-42-11/temp/screenCap_72090808621.png) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.eternaljust.msea.huawei_r1/2026-11-02-15-42-11/temp/screenCap_72096118372.png) |
| H-CanvasComponent | 点击开始前后转盘角度变化，奖项与中心按钮可见。 | 动画状态改变可见；没有奖励概率/最终结果规格，不判业务缺陷。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.canvascomponent_r1/2026-11-02-16-10-03/temp/screenCap_73759245887.png) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.canvascomponent_r1/2026-11-02-16-10-03/temp/screenCap_73764125446.png) |
| H-SwiperArkTS | 点击电影后顶部选中由全部改电影，列表从最近播放变最新，部分卡片摘要有省略号。 | Tab切换成功；摘要普通省略尚无异常证据。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.swiperarkts_r1/2026-11-02-16-34-15/temp/screenCap_75212293782.png) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.swiperarkts_r1/2026-11-02-16-34-15/temp/screenCap_75217562173.png) |
| H-VideoPlayer | 本地视频列表为空，扫描按钮可见；滚动前后同屏。 | 没有媒体文件前提，不能把空白列表当未显示内容，也不能声称视频播放已覆盖。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.videoplayer_r1/2026-11-02-16-50-17/temp/screenCap_76173058315.png) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.videoplayer_r1/2026-11-02-16-50-17/temp/screenCap_76177858501.png) |
| H-OxHornCampus | 启动欢迎页变引导图片轮播的过渡帧，右上跳过2。 | 画面变化可能自然加载/轮播，不能全部归给点击；尚未证明核心功能到达。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.oxhorncampus_r1/2026-11-02-17-21-05/temp/screenCap_78034024608.png) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.oxhorncampus_r1/2026-11-02-17-21-05/temp/screenCap_78039318862.png) |
| H-MultiShopping | 启动图变商品首页，两列卡片，笔记本标题带省略号，底部还有未完全显示商品。 | 首页触达，普通摘要省略与下方视口截断不直接当bug。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.multishopping_r1/2026-11-02-17-52-08/temp/screenCap_79884277324.png) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.multishopping_r1/2026-11-02-17-52-08/temp/screenCap_79889482520.png) |
| H-Healthy_life | 启动页上弹窗写“请在健康生活权限管理中开启XX权限”“该功能支持XX…（功能介绍）”，前后相同。 | 占位说明确实可见，是额外语义UX候选；这是教学样例应用，需核规格是否本来保留模板，不能确认产品缺陷。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.healthy_life_r1/2026-11-02-18-50-01/temp/screenCap_83367208733.png) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.healthy_life_r1/2026-11-02-18-50-01/temp/screenCap_83372361779.png) |
| H-OhBill | 空账本首页点击经济图表附近后仍为空账本首页，显示暂无数据。 | 没有记账数据，不能判图表内容丢失；点击目标到达未证实。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.ericple.ohbill_r1/2026-11-02-18-53-47/temp/screenCap_83587556869.png) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.ericple.ohbill_r1/2026-11-02-18-53-47/temp/screenCap_83592684353.png) |
| H-DistributedContacts | 联系人空列表点加号后进入新建联系人，姓名地址手机邮箱备注均为空。 | 成功到达表单；未探索填值、键盘或保存行为。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.distributedcontacts_r1/2026-11-02-19-16-34/temp/screenCap_84959462353.png) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.distributedcontacts_r1/2026-11-02-19-16-34/temp/screenCap_84964246717.png) |
| H-Swiper-scroll | 第一条ScrollEvent前后轮播从大山深处改荒漠，列表保持。 | 横向轮播发生，但可能混合自动轮播；这不是最终stepLen失败事件。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.swiperarkts_r1/2026-11-02-16-34-15/temp/screenCap_75217562173.png) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.swiperarkts_r1/2026-11-02-16-34-15/temp/screenCap_75223122697.png) |
| H-Swiper-failure-context | 先前轮播图完整，最后成功ScrollEvent后只剩顶部一条横幅，下面最新列表露出更多；下一原动作对已裁到47px高组件仍用step60，日志stepLen out of range。 | 能看到视口裁切与框架错误参数的上下文；失败动作没有后图，绝不将最后成功图当失败后图。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.swiperarkts_r1/2026-11-02-16-34-15/temp/screenCap_75740485265.png) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/open-rq1-transport/com.example.swiperarkts_r1/2026-11-02-16-34-15/temp/screenCap_75746436181.png) |

### UI-Lens五类：只统计本次所见现象，不推算全量UX探索率

| 类别 | 本次12对中的观察 | 能支持的结论 |
|---|---|---|
| text_overflow | 未见明确字越容器异常 | 仅限新看的代表帧 |
| undisplayed_content | 未确认；VideoPlayer空媒体、Msea登录门槛、OhBill暂无数据都缺业务前置 | 空页不能自动当内容未显示缺陷 |
| cropped_content | 3对有视口/轮播过渡/列表裁切 | Swiper最后图只剩47px横幅，与原布局/错误日志吻合；裁切本身随页面正常滚动，不等于App bug |
| container_overlap | Healthy_life 1对有弹窗覆盖 | 这是正常模态结构，不是异常重叠真值 |
| abnormal_text_ellipsis | 5对看见普通预览/摘要省略号 | 尚无“过早/不合理省略”的独立证据，异常确认0 |

五类异常均未独立确认；另有**1项不属于这五类的语义UX候选**：Healthy_life提示包含“XX权限”“XX…（功能介绍）”。文字在原图清楚可见，但这是源码教学样例，可能有意保留模板；需核规格再决定是否为缺陷。不能将它归成异常省略号。HapTest原策略没有视觉UX oracle，不能把本次AI看到的现象归给原工具自动检测。

Swiper补查区分了两个结果：首个ScrollEvent能看到轮播改变；末次成功事件后横幅因页面上移只剩顶部条带，下一次仍对47px高组件传step60，原日志报stepLen out of range。**失败动作没有后图**；账本的最后成功图只是失败前上下文，不能冒充失败动作的after。这使A方向的执行参数缺口与B方向的正常裁切/证据不足得到明确分离。

上述选择不是概率抽样，且以“对”为统计单位，同一现象可跨两帧重复；图hash去重只用于说明实看内容数，不替代视觉检查、状态语义去重或独立人工真值。不能把候选数/12、见到五类的个数/5或所见截图/全部图当作UX探索率、召回率或论文检测精度。


## 《待跑的实验》问题与现有数据的逐项回答

对应 [原实验要求](../方向与实验/待跑的实验.md)R2、“不用大模型的对照”及SE2-b；后续用户取消项不恢复为待自动执行计划。

| 原计划问题 | 当前数据的答案 | 缺失证据与结论边界 |
|---|---|---|
| 不用大模型能否在鸿蒙跑 | 原random/greedy_dfs真实执行；源码30计划29进计时，17预算一致exit0、4原生早退、8计时内失败、1准备失败 | 无模型不等于无运行失败，也不代表原语言任务导航能力 |
| 覆盖了哪些功能页面 | 10App源码组有路由/Ability/签名分别计数；新实图有Msea通知、Swiper电影Tab、Canvas转盘、联系人新建、购物首页及媒体空列表 | 注册路由不是人工业务功能，启动/引导/外部页不等于核心功能覆盖；不可仅比节点数 |
| 弹层、输入、满列表到达多少 | 新12对明确见Healthy权限说明弹层、联系人空表单、Wechat/Swiper/MultiShopping密集列表 | 主样本首对偏向启动/浅层；不能外推无输入状态或全量比例，密集列表不等于边界“满” |
| 找到了什么bug | 原滚动参数在裁切组件上触发stepLen错误可定位；新实图另见Healthy XX占位文案候选 | 前者是测试框架动作缺口，后者规格未核；均不能作为已确认App UX bug |
| 有哪些覆盖指标 | 源码组有原加载文件行/函数/分支计数、声明Ability/路由与运行签名；第4节逐轮报告 | 商业源码不可得；无全量UX条件分母；事件coverage已丢空，不能重造Figure4步数曲线 |
| 不用模型的对照有什么用 | 可固定HDC/截图链，作为模型语义策略之外的随机/贪心探索基线，量化动作有效性、路由身份与失败类型 | 当前商业3份历史与Func四轮不构成同App同初态同预算的完整对照；不能声称效率或检出优劣 |
| 能否发现多少开合问题，是否漏掉特定状态 | 当前仅观察普通执行截图，没有本组开合干预或正常/故障配对 | 开合数和开合问题数未测，不填0；需恢复相同状态、做真实开合并独立审阅后再比较 |

源码三重复已经能说明某些观测波动、身份差异与退出机制；它不是原计划“同3–5款商业App、各1小时、两重复、挂同一开合检测”的替代结果。下一步研究价值在于测清执行失败、正常裁切和真正UX异常之间的链条，而不是从当前数据推导不存在的开合检出率。
