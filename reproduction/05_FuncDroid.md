# FuncDroid：鸿蒙真机四应用单轮探索、候选缺陷与跨功能测试边界

更新：2026-11-02；实验已全部停止。本文合并方法、实际复现、问题处理、结果及AB研究分析；原始日志继续保留，不需再把几十篇阶段文档当作阅读入口。

## 1. 先读结论与入口

**已在鸿蒙真机完成京东、QQ音乐、淘宝、高德各一轮的实际探索与模型调用，但没有完整复现论文。** 京东、QQ音乐、高德达到约60分钟原生探索预算，随后后台页面审计未排空，最终exit1、产物incomplete；淘宝约33分钟自然结束，exit0，却没有完成一个工具标记的功能测试。自动报告只有京东、高德各1个高置信候选，均未独立重放确认。不能将“高置信”写成真实bug，更不能将有图、进程退出或模型自评完成写成UX验收通过。

- [论文学习note](https://github.com/ZYY0321/Testing-Survey/blob/main/notes/he2026funcdroid.md)：讲完整论文方法、表格与原例。
- [本次四应用最终机器记录](http://127.0.0.1:8770/harmony-file?path=user-stop-after-amap-20261102/four-app-results.json)。
- [正式批次指标CSV](http://127.0.0.1:8770/harmony-file?path=paper-batch-20261102-v3/run-metrics.csv)。该批次还包含其他工具身份，读取时筛FuncDroid四轮。
- [正式批次原件目录（本地文件）](D:/Projects/实验室/Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3)：每轮 `native/ffg.json`、`llm_responses.jsonl`、`llm_usage.json`、`console.log` 与截图。
- [完整交接说明（本地文件）](D:/Projects/实验室/Harmony-Reproduction-Handoff-20261102/交接说明_请先阅读.txt)：定位平台版本、适配和停止范围。

鸿蒙证据已从另一台机器交接到当前文件夹，不能再沿用“没有鸿蒙设备、尚未实验”的旧描述。旧探针、早批次和重复诊断不并入这四轮。后续131个未启动项已由用户取消；不能把计划表当作完成量。

## 2. 论文想解决什么，应该怎样复现

FuncDroid认为，分别执行“添加提醒”和“删除提醒”，容易漏掉“添加→删除→重新添加相同时间”的错误。它从界面轨迹构造功能流图FFG：节点代表功能目标、相关数据与轨迹；边代表从一个功能到另一个功能的可执行条件。长时视角LTV通过测试校正功能边界及条件，短时视角STV改变共享数据或操作方式，寻找跨功能错误。模型结合常见错误知识推断预期，形成非崩溃候选；这种模型预期仍不等同应用规格。

例如论文血压应用中，BMI提醒被错误显示成血糖提醒；删除后再次添加相同时间仍提示重复。前者是类型/显示内容一致性，后者是跨入口的历史数据状态错误。它们支持“功能都访问过仍可能漏掉组合”的动机，不能概括为纯视觉截断缺陷。完整原例和方法条件在 [note](https://github.com/ZYY0321/Testing-Survey/blob/main/notes/he2026funcdroid.md)；[公开实现README](https://github.com/SQUARE-RG/FuncDroid)另外区分了论文研究设计与当前可运行实现。

| 项目 | 论文原协议 | 我们实际执行 |
|---|---|---|
| 平台与应用 | Themis筛选后50个Android应用版本；52款商业App | 鸿蒙原生商业App京东、QQ音乐、淘宝、高德，四应用各1轮 |
| 设备 | 开源Android7.1、商业Android15模拟器 | Mate XT GRL-AL10真机，系统参数OpenHarmony6.1.0.29/API23 |
| 模型 | GPT-4o，基线也统一模型 | actor、judge均qwen3.8-max，enable_thinking=false |
| 状态准备 | 预置文件、注册/登录脚本，重置账号数据 | 保留已有账号/云状态；没有把Android历史bug移植为鸿蒙真值 |
| 时长与重复 | 每工具每应用60min，3次取最好 | 60min预算，4个单次观测；淘宝提前结束，三个收尾失败 |
| 实现 | 完整FFG条件流、LTV/STV及原论文消融 | 运行公开仓库场景规划→执行→图更新→后台判错；公开实现缺完整符号条件/DNF及独立LTV/STV引擎，未补写 |
| 评价 | Activity/代码覆盖、已知崩溃、非崩溃与基线 | 工具自建节点/转换、调用与图像、候选人工空值；无同设备基线、无完整分母 |

这属于**公开实现的平台与模型迁移试验**。不能说完整论文核心全部已忠实实现，也不能用四轮与论文52款商业App比较谁更强。

## 3. 环境适配、实际过程和故障处理

源码版本：FuncDroid HEAD `496656b3fd84c827c88be53c0ab609a5b6b00206`；Python3.11；设备执行用hmdriver2 agent1.1.0、uitest6.0.2.3，连接HDC。设备不同接口曾分别显示6.1与5.0(Beta1)，原值留档，没有改系统使它们看起来一致。

先进行连接/截图/前台应用核验，再依序运行正式批次；没有同时让两工具争用真机。受版本控制源码只改两处：`automator/h2.py` 按Mission ID拆分前台任务，防止宽泛正则把背景京东误认成前台QQ音乐；`workflow/llm.py`删除不兼容reasoning_effort并传Qwen所需参数。原diff为2文件6插入1删除，见 [兼容patch（本地文件）](D:/Projects/实验室/Harmony-Reproduction-Handoff-20261102/workspace/preparation/funcdroid-compat.patch)。没有为提高分数修改场景、导航、图合并或判错策略。

Windows中文绝对路径使OpenCV读取失败，外部启动器改用同目录ASCII相对路径，图像字节不改。框架原有坐标越界、解析失败、未排空审计队列等仍记录为原结果，没有关闭判错或强行补足未完成测试。

高德轮20:03:07至20:05:26有41条Arrearage欠费拒绝，用户充值后20:05:31出现真实正常回答。没有重启本轮、延长预算或另造一轮替代；该外部干扰需与模型定位/策略错误分开。日志最终审计读取726条记录，响应正文682条；请求727、原生失败78等来自不同计数位置，不满足简单减法恒等式，不能补造缺失HTTP或usage。完整记录在 [高德轮目录（本地文件）](D:/Projects/实验室/Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.amap.hmapp_r1)。

最后按用户决定高德结束后停止。已保留STOP和operator-run-control，未执行后续安装或候选重放。此处整理不启动设备、模型或新实验。

## 4. 四轮数值，怎样读才准确

| 应用 | 原生探索秒 / 墙钟秒 | 迭代 | 功能 / 页面 / 页面转换 / 资源依赖 | JPG数 | 模型响应 / 原生total_tokens | 退出 / 自动高置信候选 |
|---|---|---:|---|---:|---|---|
| 京东 | 3601.517 / 3910.953 | 40 | 11 / 19 / 174 / 5 | 403 | 595 / 2,851,119 | 1 / 1 |
| QQ音乐 | 3600.928 / 3911.031 | 45 | 22 / 26 / 234 / 1 | 402 | 679 / 3,194,456 | 1 / 0 |
| 淘宝 | 2006.871 / 2170.656 | 57 | 3 / 6 / 60 / 0 | 181 | 285 / 1,326,948 | 0 / 0 |
| 高德 | 3602.174 / 3911.000 | 62 | 15 / 23 / 173 / 6 | 428 | 682 / 3,262,090 | 1 / 1 |

四轮响应正文合计2241条、原生total_tokens合计10,634,613，截图文件合计1414张；这些是不同App的工作量总计，不是独立任务或唯一状态数。原生探索总和12811.490秒、墙钟总和13903.640秒（约3.86小时）；两者包括的收尾不同，不能拿总量作单步响应时间。最终产物状态为京东/QQ音乐/高德incomplete，淘宝complete但有审计提交拒绝警告，不能以“归档文件存在”抹去运行不完整。

原始依据为最终四轮记录及交接说明第七节。JPG含同屏前后帧，不能作为独立测试数；QQ音乐22功能不一定比京东11功能覆盖更完整，因为两者没有统一人工功能分母。资源依赖只是当前实现的字段数量，不能改称论文已验证条件流数。cached_tokens是usage子项，不能再加到total_tokens上；没有实际账单，不把token换成已核费用。

工具最终功能状态也揭示“建图≠测完”：京东 completed0、in_progress9、deferred2；QQ音乐 completed1、in_progress20、not_started1；淘宝 completed0、unavailable2、deferred1；高德 completed1、in_progress9、not_started2、deferred3。跨功能状态分别为京东done1/failed3、QQ音乐done1/failed0、淘宝0/0、高德done0/failed5。这些是工具状态标签，不能说1项done就是一个独立确认成功测试。

论文Table1为Activity0.55、代码0.40、49崩溃+13非崩溃；最好基线表中0.43/0.38、25+5。正文基线崩溃24与表25冲突须保留。商业52款报告18个，其中2修复、2确认，其余状态另列。本次没有可对齐代码/Activity分母、三重复最好值和已知bug集，所以只能对比协议与证据成熟度，不能计算“我们恢复了论文效果的X%”。

## 5. 真实正反例：到哪里、看到什么、为什么仍需验证

### 5.1 有用的正例：页面之外的输入与列表条件

京东固定20图AI草稿中，样本1为商品与分类密集首页；样本3进入即时送药；样本5对应 `00086_test_step_005_before_action.jpg`，搜索框填入water且键盘展开。这些不是凭节点名推测，而是已经归档的画面观察，说明探索能进入“有输入+键盘”的状态；这样的状态可供UX检测器观察遮挡风险。它并未证明键盘遮挡真的发生，也未对所有可填表单测覆盖。

[固定样本与判断](http://127.0.0.1:8770/harmony-file?path=formal-funcdroid-jd-review/reviewed-samples-draft.json)中还明确把全屏启动宣传画视为启动/加载，不算独立功能。QQ音乐等距20图AI检查中8图可见弹层，未见非空输入；这是单轮抽样描述，不能外推“40%页面都有弹层”或“完全不探索输入”。

可直接查看这张[已填water且键盘打开的原图](assets/05-5aef700e9198.jpg)。本次整理再次实看该图，确认搜索建议、非空输入与键盘同时可见；没有据此增加任何实验动作。

### 5.2 京东自动候选：没有商家toast与旧列表共存

原工具BUG-001看到了“没有符合条件的商家”toast，同时原商家列表还可见。模型认为筛选结果冲突，但也可能是筛选失败后合法保留旧结果。缺少toast消退后的稳定画面、产品筛选规格及独立重放，当前结论是**待验证候选**。这例可说明瞬时提示与持久状态混合，会使模型推断错误预期；不能当作已经确认的产品错误。

### 5.3 高德自动候选：把导航预期当产品规格

拒绝协议弹窗的“查看协议”被点击后，回到含正文与蓝色协议链接的欢迎页。原模型假定一定要跳独立详情页，当前没有规格支持这一假定；返回可读文本页也可能是合法实现。五个图引用实际上只有三种字节图，重复帧不构成五次独立复现。trace通用CAPTCHA文案也不能证明画面出现了验证码。

证据：[候选图hash与初审](http://127.0.0.1:8770/harmony-file?path=posthoc-candidates/amap-consent-view-agreement/native-candidate-review-snapshot.json)、[原候选目录（本地文件）](D:/Projects/实验室/Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.amap.hmapp_r1/native/bugs/BUG-001)。这是**oracle预期缺依据的bad case**，暂不能升级为已证误报，因为没有独立产品真值。

对照原图：[拒绝后弹窗](assets/05-53b6c1e84db7.jpg)、[后续协议欢迎页](assets/05-82d2460897a4.jpg)。本次整理再次实看这两图，弹窗中的“查看协议”及背景/后续页的蓝色协议链接可见；不能从图推出必须导航到哪一个独立页面。

### 5.4 QQ音乐事后候选与自动发现必须分开

全轨迹AI草稿发现切歌后菜单标题、歌手、专辑可能不同步；原自动bugs.json为空。原生后来做过cross func_9→func_18检查，但实际检查的seq237播放器和seq239新菜单信息一致；先前seq232候选帧发生在更早。不能写成“工具看到了同一张不一致图仍判通过”，也不能将事后AI发现归给工具自动检出。证据在 [事后候选目录（本地文件）](D:/Projects/实验室/Harmony-Reproduction-Handoff-20261102/workspace/private-runs/posthoc-candidates/qqmusic-menu-metadata)。用户取消的三次重放计划不是结果。

### 5.5 淘宝早退：自然结束并不表示覆盖完整

淘宝六个页面主要涉及登录、手机号、协议、找回及验证门槛。exit0与workflow_complete只说明工作流停止，功能completed仍为0；产物complete也带“页面审计拒绝22次提交、库存可能不完整”警告。这是探索/环境前置的具体瓶颈，不能把账号门槛造成的业务不可达解释为淘宝没有更多功能或没有缺陷。

## 6. UX探索能力、覆盖率与当前能下的结论

**结论：工具能进入若干与UX相关的状态，但我们没有测得UX探索率，也没有确认UX缺陷。** 这里应把四层分开：①执行/截图存在；②到达功能状态；③触发规定UX条件；④用独立真值判对UX问题。当前有①、部分可解释②，固定样本提供输入/弹层等③的存在证据；缺少全量条件分母及④。

鸿蒙不能直接照搬Android Activity指标。闭源四应用没有全功能目录，原生功能节点由模型生成，直接拿“访问功能/自己发现功能”当分母会造成自证高覆盖。合适的下一步是在固定正常版本或人工spec上定义“页面×弹层/填值/键盘/长列表×折叠状态”等条件，分别报可达比例、实际触发比例和独立确认问题数。所有分母必须冻结在看结果前，不能把未到状态排除掉。

过去六份商业终态1184张轮内去重图及73条场景文本已有Codex草稿；这些不是1184个人类独立样本，也不是73个通过测试。高德尚未全轨迹逐图复核，仅候选局部检查。当前没有论文双人真值与kappa，不能把AI整理包装为95%检测准确率。

## 7. 对A/B研究与论文写作的具体作用

**对A（测试用例生成）**：可作为在同一鸿蒙执行层上进行功能导向探索的现实基线，但需固定版本、账号、预算、同一后置UX检测器，先补相同App重复。现有四轮足以提出具体瓶颈：登录前置压缩业务空间；功能节点增长与completed脱节；跨功能尝试失败；60min后后台判错仍未完成。下一步可分别衡量“可执行前置识别”“测试进度保存”“候选优先级/审计预算”减少多少无效动作，不能泛称加一张图即可创新。

**对B（可靠UX oracle）**：京东与高德候选说明，判错至少需要区分稳定状态、操作真正生效、业务预期来源。适合构造候选审阅数据，把“有现象”“有替代正常解释”“需补证动作”“已独立确认”分开。若研究主动滑动或展开，必须保持同一候选对象和初态，证明内容是否可获得；滑动后现象消失不等于修复产品。

**可写的已证观察**：在四应用单次平台迁移pilot中，工具产生了可追溯图、轨迹与2个未确认自动候选；三轮探索预算完成但后台验证收尾失败，另一轮受前置门槛提前结束。**可写的研究假设**：显式区分前置不可达与候选证据不足，可能提高有效测试产出；要靠后续受控实验验证。**不可写**：“FuncDroid检测准确率低”“我们的算法优于论文”“跨功能测试尚无人研究”“2个新bug”。

## 8. 对我们项目目标的参考价值与不可直接借用指标

以下数值是我们自己UX项目的设计目标，不是这篇论文应当达到的要求；此处仅说明哪些已有指标可借鉴，哪些需要我们重新测量。

| 目标 | 能提供什么参考 | 所缺证据/应怎样测 |
|---|---|---|
| 低级指令到页≥95%，高级≥80% | 不能 | 当前是开放探索，不是固定成对指令任务；需冻结目标及初态，独立判断页面、状态与前置都正确 |
| 到达后发现UX问题准确率≥95% | 不能 | 两个自动候选未确认且不是标准截断集；需已知正常/缺陷对象及独立标签，分别报precision/recall与条件分母 |
| 平均动作响应≤3s、最大≤5s | 未测对应指标 | JSONL elapsed是调用级，不等同完整单步；需观测→决策→执行→后态的边界，单独报告API和设备时间，不能拿60min/迭代数代替 |
| 三层界面端到端≤10s，排除网络资源原因 | 不能 | 没有固定三层任务、输入/最终输出时间对，模型与审计异步；欠费要显式剔除并仍报告发生量，不能删除失败后补成达标 |

这些数字是我们的目标，不能要求论文为未评价任务提供同名分数。当前材料适合作为方法边界与动机案例、基线部署经验、候选证据链；还不是最终论文主实验或产品验收报告。


数据读取说明：文中示例图随报告保存，可在GitHub直接阅读；`127.0.0.1:8770`链接指向本机只读查看器，远程阅读者需按[总目录](catalog.md)的数据仓库说明下载对应项目并启动查看器。本地文件链接用于源码与准备记录溯源，不声称是在线资源。


## 本次实图UX专项复查（新看原像素，2026-11-02）

此前本轮代理实际新看只有京东water输入图1张、高德协议候选2张，共3次view_image；上文其他历史逐图草稿不应被误读为本轮已经全部重看。收到专项要求后先冻结代表样本，再逐对调用view_image查看原像素，核对原动作；没有调用设备、模型或补拍。本项目新看12对、24次原图展示，24个路径、23种SHA256内容；每App3对。规则为原截图索引中相邻、同phase、后条明确observes_result_of_step等于前条step的对图，依次取首个输入、滚动、点击类别；缺类别取中部可用对，明确不假称该类别存在。淘宝无滚动类别，实际看返回动作；QQ音乐无文本输入类别，实际看拖进度条。

账本：[本地冻结方案](https://github.com/SEKIROYUSHI/gui-testing-reproduction-20261102/blob/main/audit/harmony-ux-review-20261102/plan.json)、[逐项新观察、动作与hash](https://github.com/SEKIROYUSHI/gui-testing-reproduction-20261102/blob/main/audit/harmony-ux-review-20261102/review-ledger.json)。这两份是本地审计附件；主要结论和原图链接已全部写入本文，阅读不依赖另开总结文档。

### 按原动作核对的观察

| 样本 | 新实看前后画面与到达判断 | UX解释与边界 | 原图 |
|---|---|---|---|
| F-高德-input | 地点报错输入从占位文字变公园，搜索结果列表改变且键盘仍打开。 | 到达非空输入与键盘状态，文本输入效果可见；不是完成地点报错。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.amap.hmapp_r1/native/trace_screenshots/00227_test_step_003_before_action.jpg) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.amap.hmapp_r1/native/trace_screenshots/00228_test_step_004_before_action.jpg) |
| F-高德-scroll | 地图首页变为分享底部面板，含微信/保存图片/反馈上报；背景变暗。 | 分享面板可见，但原动作描述为scroll找分享，画面不能证明滚动本身正确命中预期容器。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.amap.hmapp_r1/native/trace_screenshots/00132_test_step_001_before_action.jpg) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.amap.hmapp_r1/native/trace_screenshots/00133_test_step_002_before_action.jpg) |
| F-高德-click | 点击服务条款前后仍是同一协议欢迎页，链接仍可见。 | 本对没有证实独立条款页到达，可能操作无效/加载/定位问题，不能归产品缺陷。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.amap.hmapp_r1/native/trace_screenshots/00001_test_step_001_before_action.jpg) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.amap.hmapp_r1/native/trace_screenshots/00002_test_step_002_before_action.jpg) |
| F-京东-input | 搜索输入从占位/历史推荐变water并出现waterman等建议；键盘打开。 | 输入目标已到达；未对所有搜索结果或布局做验收。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.jd.hm.mall_r1/native/trace_screenshots/00085_test_step_004_before_action.jpg) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.jd.hm.mall_r1/native/trace_screenshots/00086_test_step_005_before_action.jpg) |
| F-京东-scroll | 首页栏目滚动到商品卡网格，产品标题Mac mini M4/M4 Pr、华为（HUAWEI）国等在卡片右边裁止。 | 滚动触达更多列表。标题完整可获得性未验证，列为显示现象候选，不能判断真截断。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.jd.hm.mall_r1/native/trace_screenshots/00018_test_step_003_before_action.jpg) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.jd.hm.mall_r1/native/trace_screenshots/00019_test_step_004_before_action.jpg) |
| F-京东-click | 点击京东超市记录前后均为首页，促销广告内容局部变化。 | 未证实进入京东超市；动态广告变化不等于目标达成。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.jd.hm.mall_r1/native/trace_screenshots/00006_test_step_001_before_action.jpg) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.jd.hm.mall_r1/native/trace_screenshots/00007_test_step_002_before_action.jpg) |
| F-淘宝-input | 手机号输入由占位变123，按钮由淡橙变橙色，数字键盘打开，协议末行受键盘边界裁切。 | 输入效果可见；按钮变色不能证明非法号码被业务接受，未执行发送验证。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.taobao.taobao4hmos_r1/native/trace_screenshots/00030_test_step_001_before_action.jpg) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.taobao.taobao4hmos_r1/native/trace_screenshots/00031_test_step_002_before_action.jpg) |
| F-淘宝-scroll | 身份验证空表单按返回后到手机号登录页，无键盘。 | 这是预定无scroll类别时的回退样本，不能说淘宝完成滚动；实际到达页与原描述账户找回需区分。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.taobao.taobao4hmos_r1/native/trace_screenshots/00072_test_step_003_before_action.jpg) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.taobao.taobao4hmos_r1/native/trace_screenshots/00073_test_step_004_before_action.jpg) |
| F-淘宝-click | 通知权限弹窗点击不允许前后仍显示同一弹窗。 | 弹层状态确已探索，拒绝效果本对未见，不能据此判断系统权限bug。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.taobao.taobao4hmos_r1/native/trace_screenshots/00001_test_step_001_before_action.jpg) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.taobao.taobao4hmos_r1/native/trace_screenshots/00002_test_step_002_before_action.jpg) |
| F-QQ音乐-input | 无输入类别的回退样本：拖进度条前00:18、后01:37，曲名仍为别卿书；唱片角度/歌词改变。 | 可见进度跳转，是有效媒体状态操作；不能列为非空文本输入。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.tencent.hm.qqmusic_r1/native/trace_screenshots/00205_test_step_005_before_action.jpg) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.tencent.hm.qqmusic_r1/native/trace_screenshots/00206_test_step_006_before_action.jpg) |
| F-QQ音乐-scroll | 顶部推荐意图滑动前后均仍选AI作歌，正文空输入0/600；底部迷你播放器歌名只显示部分且前后片段不同。 | 推荐页未被证实到达；迷你播放器可能是滚动/淡出标题，需连续观察，不当异常省略号。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.tencent.hm.qqmusic_r1/native/trace_screenshots/00059_test_step_007_before_action.jpg) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.tencent.hm.qqmusic_r1/native/trace_screenshots/00060_test_step_008_before_action.jpg) |
| F-QQ音乐-click | 开屏广告有静音图标，后图为我的页和迷你播放器。 | 已离开广告，但静音是否生效无音频证据，不能说静音测试通过。 | [前](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.tencent.hm.qqmusic_r1/native/trace_screenshots/00003_test_step_001_before_action.jpg) / [后](../../Harmony-Reproduction-Handoff-20261102/workspace/private-runs/paper-batch-20261102-v3/FuncDroid_com.tencent.hm.qqmusic_r1/native/trace_screenshots/00004_test_step_002_before_action.jpg) |

### UI-Lens五类：只统计本次所见现象，不推算全量UX探索率

| 类别 | 本次12对中的观察 | 能支持的结论 |
|---|---|---|
| text_overflow 文字超出容器 | 未见可明确证实的异常 | 不能据代表样本断言全轨迹没有 |
| undisplayed_content 内容未显示 | 未确认；数次目标导航没有达到 | 先排除Agent点错/加载/前置，再讨论产品内容缺失 |
| cropped_content 内容裁切 | 5对有裁切外观，其中4对为键盘/动态标题等正常或未定现象，京东商品标题1对列显示候选 | 已触达相关观察机会；没有详情/滑检，未确认真截断 |
| container_overlap 容器重叠 | 2对有分享或权限弹层覆盖背景 | 正常模态不是容器异常重叠缺陷 |
| abnormal_text_ellipsis 异常省略号 | 未确认 | 迷你播放器只显部分标题且随时间改变，不能直接叫异常省略 |

新增**显示现象候选1项、独立确认UX缺陷0项**。候选指京东商品标题在卡片右侧裁止，可能是合法紧凑摘要，需要详情或展开对照；不是新产品bug。原工具没有本次五类的标签/真值评测，不能称“工具已自动检测这1项”。高德分享面板、京东输入/滚动和QQ音乐进度条变化是实际触达的正例；京东超市、QQ推荐页和高德条款点击未证目标到达，是A方向执行验证应保留的反例。

上述选择不是概率抽样，且以“对”为统计单位，同一现象可跨两帧重复；图hash去重只用于说明实看内容数，不替代视觉检查、状态语义去重或独立人工真值。不能把候选数/12、见到五类的个数/5或所见截图/全部图当作UX探索率、召回率或论文检测精度。


## 《待跑的实验》问题与现有数据的逐项回答

本节对应仓库 [原实验要求](https://github.com/ZYY0321/Testing-Survey/blob/main/方向与实验/待跑的实验.md)第零步R1、SE2-b及功能状态分析。文件中的旧模型/平台要求若与后来用户决定冲突，以本次真实协议为准；不为完成AI清单补改论文方法。

| 原计划要回答的问题 | 现有数据给出的答案 | 仍缺什么，因而不能回答什么 |
|---|---|---|
| 能否在鸿蒙商业App上实际探索 | 四应用各1轮真实跑过，逐轮统计见第4节；三个预算探索后收尾失败、淘宝早退 | 不能称四轮都完整成功；缺三重复与统一初态 |
| 找到了哪些功能页面 | 工具分别输出11/22/3/15功能、19/26/6/23页面；新实图核到京东搜索/商品流、高德地点报错/分享/协议、淘宝手机号/身份验证、QQ播放器/AI作歌/我的页 | 自建节点不是人工功能分母，不能称功能覆盖率；节点合并/拆分准确性未量化 |
| 状态里有多少弹层、输入、满列表 | 新12对中明确见输入water/公园/123与键盘；分享和系统权限模态；京东商品卡列表 | 本次有目的代表选择，不能推全轨迹各状态比例；“列表密集”不等于已填满最大容量 |
| 报的功能bug是什么 | 京东无商家toast与旧列表共存、高德查看协议导航预期，各1自动候选；QQ菜单不同步是事后候选，不能算自动发现 | 三项都未独立重放确认，缺规格和稳定观察，不能报新bug数或检测精确率 |
| 和不用模型的探索器比怎样 | HapTest提供同平台另一类轨迹和失败机制，可作后续对照来源 | 两工具商业组当前App/时长/退出不同，不能计算公平优势、显著性或单位时间检出提升 |
| 相同预算下能发现多少开合问题 | 当前没有在这些轨迹上执行受控折叠/展开检测 | 开合测试数/开合问题数未测，不能填0；需同状态恢复、真实开合和独立标签 |
| 是否找到它们漏掉的特定UX状态 | 新实图说明工具确能进入输入、弹层、列表、媒体状态；也出现目标未到达 | 没有我们方法同初态对照及已知UX缺陷位置，不能说某类状态被系统性漏掉或我们的方案更强 |

可交付给后续论文实验的部分，是原动作—可观察状态—候选依据与替代解释；计划中的完整公平比较、开合检出和双人真值仍未开展，不能把这一整理任务当成这些实验已经补完。
