# GUI 智能体论文：AI 会议的投稿结果与审稿要点

数据来自 OpenReview 公开记录：ICLR 2025 / 2026、NeurIPS 2024 / 2025（含 Datasets and Benchmarks），另有少数 ACL。2026-10-29 整理，共 43 条投稿记录，录用 21、拒稿 12、撤稿 7，另有 2 篇只有预印本。原始记录在 `data/openreview_gui_agent_2026-10-29.json`。

注意："拒稿""撤稿"只是那一次投稿的结果，有的论文后来在别的会议中了。审稿分是 OpenReview 上能看到的那几条，不一定是全部。

| 论文名 | 年份 | 投的会议 | 结果 | 一句话做了什么 | 核心贡献类型 | 审稿要点 | 链接 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| OS-ATLAS: Foundation Action Model for Generalist GUI Agents | 2025 | ICLR | 录用（Spotlight） | 做跨平台 GUI grounding 数据合成工具，放出大规模开源语料，并训练 4B/8B 动作模型。 | 数据集；训练方法 | 本次看到 3 条审稿，评分 8、8、6。认可点是多平台开源截图语料、开源模型在 grounding 和原子动作上达到他们所写的 SOTA，以及预训练再微调的统一流程。批评包括桌面数据量偏少（审稿人提到 Table 1 里桌面约 54K）、合成数据正确性与 “thought” 格式没写清，以及 8B 在部分基准上不如 4B。一条评分 6 的审稿注明分数是讨论阶段回应之后给的。meta-review 未查到。 | https://openreview.net/forum?id=n9PDaFNi8t |
| AgentTrek: Agent Trajectory Synthesis via Guiding Replay with Web Tutorials | 2025 | ICLR | 录用（Spotlight） | 用网上教程引导回放，自动合成多步 GUI 轨迹，并用评估模型过滤。 | 数据集 | 评分 8、6、8。认可教程合成能降低人工标注成本、流程清楚、任务覆盖广，并写训练后的智能体在若干基准上表现好。批评：有效轨迹生成成功率只有 39.1%（审稿人写基于 GPT-4o Mini）；轨迹总数仍是几千条，和 Mind2Web 同量级，4 倍于 Mind2Web 训练数据时效果仍不够；Mind2Web 实验可能和测试网站/任务重叠；只评了网页、只报了两个基准。meta-review 未查到。 | https://openreview.net/forum?id=EEgYUccwsV |
| Learn-by-interact: A Data-Centric Framework For Self-Adaptive Agents in Realistic Environments | 2025 | ICLR | 录用（Poster） | 不靠人工标注，让智能体在真实环境里自己合成交互数据来适配。 | 数据集；训练方法 | 只检索到 1 条，评分 6。优点是 backward construction 在审稿人看来是新的，文章清楚。问题是摘要里写可适配任意环境，但审稿人认为需要环境文档，且合成用的模型必须已经熟悉该环境，否则很难写出有效指令和轨迹。meta-review 未查到。 | https://openreview.net/forum?id=3UKOzGWCVY |
| AndroidWorld: A Dynamic Benchmarking Environment for Autonomous Agents | 2025 | ICLR | 录用（Poster） | 在 20 个真实 Android 应用上提供 116 个带程序化奖励的动态任务环境。 | benchmark | 只检索到 1 条，评分 6。认可动态任务参数、可复现，以及基线分析指出了现有智能体的不足。审稿人也写总体成功率只有 30.6%，且没有细讲参数变化如何影响成绩。meta-review 未查到。 | https://openreview.net/forum?id=il5yUQsrjC |
| WebRL: Training LLM Web Agents via Self-Evolving Online Curriculum Reinforcement Learning | 2025 | ICLR | 录用（Poster） | 用在线课程强化学习训练开源网页智能体，减少对专有 API 的依赖。 | 训练方法 | 评分 6、8。高分审稿认为题目重要、相对基线的差距明显、分析够细。另一条承认在线 RL 省掉了 SFT 数据准备，也能在 WebArena-Lite 上扩大规模，但指出生成的指令仍要人工剔除不合理任务，因此 “self-evolving” 被削弱，而且和 DigiRL 的对比没有把课程学习单独拆干净。meta-review 未查到。 | https://openreview.net/forum?id=oVKEAFjEqv |
| DistRL: An Asynchronous Distributed Reinforcement Learning Framework for On-Device Control Agent | 2025 | ICLR | 录用（Poster） | 把手机上的数据采集和策略学习拆开，做异步分布式强化学习。 | 训练方法；系统 | 评分 6、5。认可异构设备上异步采集、样本效率和任务成功率，以及约 3 倍训练速度的说法。质疑包括超参多、Gemini 当奖励评估器是否可信、正文和附录的模型描述不一致（一处写 T5，附录写 GPT-2/RoBERTa）。meta-review 未查到。 | https://openreview.net/forum?id=LPG8pPSfQD |
| GUI-World: A Video Benchmark and Dataset for Multimodal GUI-oriented Understanding | 2025 | ICLR | 录用（Poster） | 做覆盖多种 GUI 场景的视频基准，用来测动态界面理解，并微调了一个视频模型 GUI-Vid。 | 数据集；benchmark | 检索到 2 条，评分 5、6。认可视频形态的 GUI 数据少、规模和场景宽，并评了多个多模态模型。一条明确写：没有说清这个数据集对真正的 GUI 智能体任务有多少帮助，例子更像界面理解而不是高层任务。meta-review 未查到。同一工作在 NeurIPS 2024 Datasets and Benchmarks 的投稿页是拒稿（见下表），那边的审稿正文未查到。 | https://openreview.net/forum?id=QarKTT5brZ |
| GUI-World: A Dataset for GUI-Orientated Multimodal Large Language Models | 2024 | NeurIPS Datasets and Benchmarks | 拒稿 | 同上，投的是更早一版数据集论文。 | 数据集；benchmark | venueid 为 `NeurIPS.cc/2024/Datasets_and_Benchmarks_Track/Rejected_Submission`。审稿意见未查到。 | https://openreview.net/forum?id=h8LuywKj6N |
| Ferret-UI 2: Mastering Universal User Interface Understanding Across Platforms | 2025 | ICLR | 录用（Poster） | 在 Ferret-UI 上扩展到 iPhone、Android、iPad、网页和 Apple TV 的界面理解模型。 | 数据集；系统 | 只检索到 1 条，评分 6。认可多平台和用 GPT-4o 加视觉提示造训练数据。审稿人同时写整篇更像沿 GUI-World 的流程收集数据再用强模型标注，兴奋感有限。meta-review 未查到。 | https://openreview.net/forum?id=GBfYgjOfSe |
| Agent S: An Open Agentic Framework that Uses Computers Like a Human | 2025 | ICLR | 录用（Poster） | 用外部知识、分层规划和经验记忆，通过 GUI 完成长程电脑任务，并定义 Agent-Computer Interface。 | 系统 | 只检索到 1 条，评分 6。认可经验增强的分层规划，以及 ACI 把感知和动作收成有界动作空间。批评没测任务量变大时的效率和速度，也没讨论网上知识稀缺或界面快速变化时会不会失效。meta-review 未查到。 | https://openreview.net/forum?id=lIVRgt4nLv |
| DigiRL: Training In-The-Wild Device-Control Agents with Autonomous Reinforcement Learning | 2024 | NeurIPS | 录用（poster） | 用离线到在线的自主强化学习，训练能在真实 GUI 上操作设备的视觉语言模型。 | 训练方法 | 只检索到 1 条，评分 5。审稿人承认问题难、相对现有 LLM 方法有明显提升，实验覆盖提示、学习、离线和 off-to-online。同时写方法新意有限，主要是 AWR、doubly-robust 等已有技术，改动不大。这条不能代表全部审稿。meta-review 未查到。 | https://openreview.net/forum?id=4XTvXMSZPO |
| OSWorld: Benchmarking Multimodal Agents for Open-Ended Tasks in Real Computer Environments | 2024 | NeurIPS Datasets and Benchmarks | 录用（Poster） | 在真实电脑环境里评测开放式多模态智能体，补上只有静态或单应用环境的缺口。 | benchmark | 审稿意见未查到。 | https://openreview.net/forum?id=tN61DTr4Ed |
| Synatra: Turning Indirect Knowledge into Direct Demonstrations for Digital Agents at Scale | 2024 | NeurIPS | 录用（poster） | 把网上的间接教程知识转成可直接用来训练数字智能体的示范。 | 数据集 | 只检索到 1 条，评分 6。认可动机和写法，方法简单有效。实验批评是对比不公平：很多基线没微调，也缺用更直接示范做微调的基线，以及可考虑的 RAG 方法。meta-review 未查到。 | https://openreview.net/forum?id=KjNEzWRIqn |
| VideoGUI: A Benchmark for GUI Automation from Instructional Videos | 2024 | NeurIPS Datasets and Benchmarks | 录用（Spotlight） | 用教学视频构建视觉向的 GUI 自动化基准，并做分层失败分析。 | benchmark | 检索到 3 条，评分 6、6、8，weaknesses 字段为空。优点写的是：创意软件里的拖拽等动作补上了现有基准的空白；标注有人工质检；分层评估能看出失败在哪一层。meta-review 未查到。 | https://openreview.net/forum?id=jSKtxmxc0M |
| Mobile-Agent-v2: Mobile Device Operation Assistant with Effective Navigation via Multi-Agent Collaboration | 2024 | NeurIPS | 录用（poster） | 用规划、执行和反思多个智能体加记忆单元，减轻手机导航丢焦点的问题。 | 系统 | 评分 7、4、6、5。认可多角色分工、记忆和反思，以及真机上的提升。反对意见写规划–执行–反思是常见做法、技术增量有限，缺和 AppAgent、CogAgent 的对比，也没写记忆怎么检索、多角色的开销。meta-review 未查到。 | https://openreview.net/forum?id=O0nBMRlkc8 |
| AutoGuide: Automated Generation and Selection of Context-Aware Guidelines for Large Language Model Agents | 2024 | NeurIPS | 录用（poster） | 从过去经验里抽出带上下文的操作指南，在网页等不熟悉领域里指导语言模型智能体。 | 训练方法；系统 | 评分 6、7、6。认可指南绑在具体情境上、消融说明指南选择有用、复杂基准上优于基线。批评是整体接近 ReAct 加检索，每个任务都要建指南，可能引入噪声，也没报 token 或调用次数。meta-review 未查到。 | https://openreview.net/forum?id=mRIQz8Zd6O |
| Learning to Contextualize Web Pages for Enhanced Decision Making by LLM Agents | 2025 | ICLR | 录用（Poster） | 单独训练一个小模型，把复杂网页收成更易决策的上下文，再交给语言模型智能体。 | 训练方法 | 评分 8、5。高分认可把“理解页面”和“做决策”拆开，用模拟数据微调小模型，奖励来自多个强模型的评判。另一条写成功率和跨模型泛化不错，但训练样本不到 2000，没见过的界面元素会失败，也不确定真实环境里有没有模拟器可用来采样。meta-review 未查到。 | https://openreview.net/forum?id=3Gzz7ZQLiz |
| UI-Genie: A Self-Improving Approach for Iteratively Boosting MLLM-based Mobile GUI Agents | 2025 | NeurIPS | 录用（poster） | 用图文交错的奖励模型给动作和轨迹打分，再靠自改进循环扩充高质量手机轨迹。 | 训练方法 | 审稿意见未查到。摘要写奖励模型解决轨迹对错难验证，自改进流程解决高质量数据扩不了。 | https://openreview.net/forum?id=3uUmJzSSOW |
| OpenCUA: Open Foundations for Computer-Use Agents | 2025 | NeurIPS | 录用（spotlight） | 做开放的计算机使用智能体框架，用来研究闭源系统不公开的能力边界。 | 系统 | 审稿意见未查到。 | https://openreview.net/forum?id=6iRZvJiC9Q |
| ProRe: A Proactive Reward System for GUI Agents via Reasoner–Actor Collaboration | 2026 | ICLR | 录用（Poster） | 在没有标准轨迹和应用数据库时，用推理者和执行者协作给出 GUI 奖励，替代静态的 LLM 评判。 | 训练方法 | 审稿意见未查到。 | https://openreview.net/forum?id=xtysskccFc |
| On the Effects of Data Scale on UI Control Agents | 2024 | NeurIPS Datasets and Benchmarks | 录用（Spotlight） | 发布 AndroidControl（摘要写 15,28… 条，后文在检索结果里被截断），研究只靠微调能不能做出可用的界面控制智能体。 | 数据集；分析 | 审稿意见未查到。 | https://openreview.net/forum?id=yUEBXN3cvX |
| OS-Genesis: Automating GUI Agent Trajectory Construction via Reverse Task Synthesis | 2025 | ACL 主会长文 | 录用 | 先与界面交互，再反向归纳任务，并用轨迹奖励模型筛数据，用来训练 GUI 智能体。 | 数据集；训练方法 | ACL Anthology 页面确认收在 ACL 2025 长文，页码 5555–5579。OpenReview 上能看到 ACL ARR 2024 December 投稿记录，审稿意见未查到。 | https://aclanthology.org/2025.acl-long.277/ ；https://openreview.net/forum?id=y2oVMMRzRE |
| GUI-R1: A Generalist R1-Style Vision-Language Action Model For GUI Agents | 2026 | ICLR | 拒稿 | 用约 3K 条轨迹和可验证奖励做强化学习微调，想用很少的数据超过大规模 SFT。 | 训练方法 | venueid 为拒稿。只检索到 1 条，评分 4。审稿人承认 3K 对比 OS-Atlas 一类千万级数据的效率，也承认统一动作空间和可验证奖励。拒稿点是奖励各项没有统一公式、数据筛选理由没写、缺少最大熵和另一组件的消融。 | https://openreview.net/forum?id=pZQvv5C7WL |
| Aguvis: Unified Pure Vision Agents for Autonomous GUI Interaction | 2025 | ICLR | 拒稿 | 只用截图、不用文本界面树，把已有数据组织起来训练跨平台纯视觉 GUI 智能体。 | 训练方法；系统 | 评分 5、6、5。都承认 ScreenSpot、Multimodal Mind2Web、Android Control 上的数字好看。三条的共同问题是新意不够：没有新数据集也没有新模块，和 AMEX、SeeClick 一样用纯视觉加 grounding/规划数据；数据构造细节缺失；用 VLM 造的思维链没有消融。 | https://openreview.net/forum?id=FHtHH4ulEQ |
| AutoGUI: Scaling GUI Grounding with Automatic Functionality Annotations from LLMs | 2025 | ICLR | 拒稿 | 用大模型自动给界面元素写功能描述，做成约 70.4 万条的标注数据。 | 数据集 | 评分 3、3、8、6。高分认可可扩展、带上下文的功能描述、网页加 Android。低分认为 FuncPred 测试集和训练分布相同，拿掉之后相对同基座 SeeClick 的提升不足以证明数据有用；泛化和 LLM 校验的可靠性也不足。 | https://openreview.net/forum?id=wl4c9jvcyY |
| Guiding VLM Agents with Process Rewards at Inference Time for GUI Navigation | 2025 | ICLR | 拒稿 | 推理时用过程奖励模型逐步改 GUI 动作，避免为 GUI 专门微调。 | 训练方法 | 只检索到 1 条，评分 3。审稿人同意实验能说明过程奖励有助于推理时选动作。主要问题是没讨论已有的过程奖励模型文献，技术像是把已有结论搬到 GUI，当前写法看不出新的认识。 | https://openreview.net/forum?id=jR6YMxVG9i |
| OmniParser for Pure Vision Based GUI Agent | 2025 | ICLR | 拒稿 | 训练图标检测和功能描述模型，把截图解析成结构化元素，再交给 GPT-4V 做动作。 | 系统 | 只检索到 1 条，评分 5。认可写法清楚、解析后 GPT-4V 的 grounding 变好。审稿人写新意不够：是把现有检测和描述模块拼起来，达不到 ICLR 对创新的要求；网页数据里文字多、图标少，细节不足。 | https://openreview.net/forum?id=C6hUK6Q1Pi |
| MobileGUI-RL: Advancing Mobile GUI Agent through Reinforcement Learning in Online Environment | 2026 | ICLR | 拒稿 | 在线环境里自己探索出课程任务，用轨迹感知的 GRPO 和组合奖励训练手机 GUI 智能体。 | 训练方法 | 评分 4、4、4。承认相对离线模仿是在线设定，7B/32B 在若干在线基准上有提升。三条都要求补评估器与成功率的相关性、任务怎么造、在线采样成本。一条写没有和 DigiRL、DistRL 比，在线强化学习的定位因此站不住；文本世界模型过滤和可扩展环境的实现也没写清。 | https://openreview.net/forum?id=LrXGarwVO8 |
| VistaGUI: Towards More Robust and Intelligent GUI Automation | 2026 | ICLR | 拒稿 | 把检索、多模态感知和失败后的状态恢复做成模块，用来减少 GUI 智能体中途卡住。 | 系统 | 评分 4、4、4。认可针对检索、感知和恢复三个失败点，并在 WindowsAgentArena 和 OSWorld-W 上有提升。三条都写成增量工程：规划–执行–反思的结构已有，Reflector 如何发现失败并恢复没有写清，动态和噪声场景测得不够。 | https://openreview.net/forum?id=ld2bbwEvSu |
| InfiGUI-R1: Advancing Multimodal GUI Agents from Reactive Actors to Deliberative Reasoners | 2026 | ICLR | 拒稿 | 先监督微调再做可验证奖励的强化学习，让 GUI 智能体从反应式动作变成带推理的决策。 | 训练方法 | 只检索到 1 条，评分 6，但 venueid 为拒稿。审稿人承认最终数字强、流程好复现、覆盖了手机/网页/桌面。新意被否定：SFT 后再 RLVR 是大模型的常规顺序，文中基线 UI-TARS 已是 SFT 再 DPO，机器人里的视觉-语言-动作模型也这么做。 | https://openreview.net/forum?id=wywgRd1MUQ |
| GUI-Shepherd: Reliable Process Reward and Verification for Long-Sequence GUI Tasks | 2026 | ICLR | 拒稿 | 用 5.2 万条带人工对错和 GPT-4o 理由的交互，训练长程 GUI 的过程奖励模型。 | 训练方法；数据集 | 评分 4、2。承认过程奖励用于在线强化学习、人机混合标注。问题是 AndroidWorld 上 40.5% 成功率，对比审稿人引用的 GUI-Owl-7B 66.4%；没有和 DigiRL 的在线/离线设定比；方法被看成标准强化学习目标，没有新的理论分析。 | https://openreview.net/forum?id=9hM4YRMhfT |
| InSTA: Towards Internet-Scale Training For Agents | 2026 | ICLR | 拒稿 | 让大模型给约 15 万个真实网站写任务并执行，想去掉人工示范来训练网页导航。 | 数据集；训练方法 | 只检索到 1 条，评分 4。认可 15 万站远超常见约 200 站的基准，以及事先做了安全过滤（审稿人写最高约 97% 准确）。主要缺口是反馈闭环在论文里只跑了一轮，更难任务的课程没有真正评到；附录里最常被解开的仍是简单信息检索。 | https://openreview.net/forum?id=zibL9wu2LI |
| CRAB: Cross-environment Agent Benchmark for Multimodal Language Model Agents | 2025 | ICLR | 拒稿 | 用跨桌面和手机的任务图评估多模态智能体，而不是只测单一环境。 | benchmark | 评分 6、5、3、2。认可跨环境任务和图评估能记录中间进度。反对集中在：任务只有约 120 条、应用覆盖窄；只报数字、没有解释跨平台到底难在哪；图评估器怎么建没说清；相关工作漏了 Mobile-ENV 和 GUI-World。一条评分 3 还写这更像工程问题而不是研究问题。 | https://openreview.net/forum?id=qqKJjwibsp |
| GUI-ReWalk: Massive Data Generation for GUI Agent via Stochastic Exploration and Intent-Aware Reasoning | 2026 | ICLR | 撤稿 | 先随机游走探索，再按意图推理生成约 5 万条、可跨应用的长轨迹。 | 数据集 | 撤稿前有 3 条审稿，评分 2、4、4。规模和约 20 步的长度被当成优点。决定性批评是：相对基座 Qwen2.5-VL-7B 的提升不大，有时更差；和 OS-Genesis-7B 的基座不同，对比不公平；实验主要在静态 grounding，而不是真实环境里的端到端任务成功；人工评估协议没写清；2025 年相近的数据合成工作没讨论。 | https://openreview.net/forum?id=FcZqZH0j1b |
| VEM: Environment-Free Exploration for Training GUI Agent with Value Environment Model | 2026 | ICLR | 撤稿 | 用离线数据预训练一个价值环境模型，在不连真实 GUI 的情况下做强化学习。 | 训练方法 | 评分 4、4、2。动机（省掉在线交互）被认可。三条一致认为这不是环境模型：Q 是二分类打分器，没有折扣回报或 TD，更像过程奖励模型；训练依赖 GPT-4o 标注，只是把环境成本换成 API 成本；策略更新是标准的价值引导策略梯度，而且可能利用冻结打分器。 | https://openreview.net/forum?id=CG1fqwnFGj |
| UItron: Foundational GUI Agent with Advanced Perception and Planning | 2026 | ICLR | 撤稿 | 做感知、规划和课程强化学习的三段训练，并建手机与 PC 的交互环境，强调中文应用。 | 训练方法；系统 | 评分 6、2、4、2、2。有审稿人认可数据工程和分阶段训练的消融。低分集中在：基座模型是哪一个完全没写；感知/规划数据的规模和构造没写；7B 相对 UI-TARS-7B 只是持平或更差；中文场景的优势可能只来自专门堆的数据；算法侧主要是现成的 SFT 和 GRPO。 | https://openreview.net/forum?id=Hl3HR6Scs2 |
| MMBench-GUI: Hierarchical Multi-Platform Evaluation Framework for GUI Agents | 2026 | ICLR | 撤稿 | 在 Windows、macOS、Linux、iOS、Android 和 Web 上做理解、grounding、自动化和协作四层评测，并加效率指标。 | benchmark | 只检索到 1 条，评分 2。审稿人承认平台全、macOS 的 70 个任务是新的、效率指标有用。核心反对是：统一之后没有新的认识，现有工作已经指出文中“发现”的问题，基准的实际用处不足。 | https://openreview.net/forum?id=GQugc1J2kW |
| SpiritSight Agent: Advanced GUI Agent with One Look | 2025 | ICLR | 撤稿 | 用课程学习和 Universal Block Parsing，训练只看截图的端到端 GUI 智能体。 | 训练方法；系统 | 浏览器论坛页上看到 4 条，评分 5、6、5、5，作者于 2024-11-14 撤稿。共同问题：基座是 InternVL，对比的 SeeClick、CogAgent 基座不同，无法证明提升不是骨干带来的；各下游集单独微调，跨平台泛化不成立；UBP 缺块大小等消融；人工抽查数据质量的过程没写；和 CogAgent、SeeClick 的数据相比，新数据的新意没立住。一条评分 6 认可纯视觉和层次化采集。 | https://openreview.net/forum?id=jY2ow7jRdZ |
| WorldGUI: An Interactive Benchmark for Desktop GUI Automation from Any Starting Point | 2026 | ICLR | 撤稿 | 让桌面任务从非默认初始状态开始评测，并用规划批评、步骤检查和动作纠正来补救。 | benchmark；系统 | 评分 6、4。认可非默认起点是真缺口。批评是验证–纠正和自我批评已出现在 Agent S、Agent S2 和更早的反思方法里，技术新意有限；依赖教学视频，但没说明视频质量和为什么不能直接用文本；摘要里 WindowsAgentArena 上 1.7% 的提升，审稿人认为和表不一致或差距太小。 | https://openreview.net/forum?id=oJZYmlVtwD |
| NNetscape Navigator: Complex Demonstrations for Web Agents Without a Demonstrator | 2025 | ICLR | 撤稿 | 先在浏览器里探索出轨迹，再用语言模型事后写成指令，从而不要人工示范。 | 数据集 | 只检索到 1 条，评分 3。审稿人承认相对零样本和 instruction-first 的 SFT，在 MiniWoB++ 和 WebArena 上有提升。新意被写成和 BAGEL 的 demonstration-first 几乎相同，而且没说清成绩是不是只来自数据量。 | https://openreview.net/forum?id=hHF5AayC7O |
| Explorer: Scaling Exploration-driven Web Trajectory Synthesis for Multimodal Web Agents | 2025 | ACL ARR 2025 February | 决定未查到 | 在真实网站上探索并改写任务，合成大规模多模态网页轨迹并训练小模型。 | 数据集；训练方法 | OpenReview 投稿页的 venue 是 “ACL ARR 2025 February Submission”，没有录用或拒稿标记，审稿意见未查到。arXiv HTML 写成功轨迹超过 9.4 万条、约 4.9 万个 URL、72 万张截图，成功轨迹平均成本 28 美分，并在 Mind2Web-Live、Multimodal-Mind2Web、MiniWoB++ 上训练了模型。会议最终名称未查到。 | https://openreview.net/forum?id=iJ3BFUXsKD ；https://arxiv.org/abs/2502.11357 |
| ZeroGUI: Automating Online GUI Learning at Zero Human Cost | 2025 | 未查到会议投稿页 | 只有预印本 | 用视觉语言模型自动出任务、自动判成功，再做两阶段在线强化学习，不靠人工标注。 | 训练方法 | OpenReview 用标题检索没有投稿记录。arXiv HTML 写在 OSWorld 上，ZeroGUI-UI-TARS-7B 相对提升 14%，ZeroGUI-Aguvis-7B 相对提升 63%。审稿意见未查到。 | https://arxiv.org/abs/2505.23762 |
| Vision-driven Automated Mobile GUI Testing via Multimodal Large Language Model | 2024 | 未查到 ICLR/NeurIPS 主会投稿 | 只有预印本 | 用多模态大模型看手机界面的视觉语义，做自动化 GUI 测试。 | 系统 | OpenReview 检索到的记录 venue 是 CoRR 2024。审稿意见未查到。摘要只看到问题设定，具体方法后文未在本次结果里展开。 | https://openreview.net/forum?id=prCoYC6A9z |
| ScaleCUA: Scaling Open-Source Computer Use Agents with Cross-Platform Data | 2026 | ICLR | 录用（Oral） | 收集跨 6 个操作系统的计算机使用数据，训练开源计算机使用智能体。 | 数据集；训练方法 | 评分 6、6、10。评分 10 写多个难基准上结果强，MMBench-GUI L1-Hard 为 94.4%，消融覆盖数据增强、轨迹加权、坐标格式、分辨率和数据规模，没有主要缺点。另两条认可约 1.5 万条弱语义自动轨迹加约 4 千条人工轨迹、覆盖桌面和手机，并承诺开放数据。批评是自动采集和人工采集被写成协同，实际是两条分开的管道。meta-review 未查到。 | https://openreview.net/forum?id=yBFUqdJFZn |
| AgentStore: Scalable Integration of Heterogeneous Agents As Specialized Generalist Computer Assistant | 2025 | ICLR | 撤稿 | 用一个元智能体把多个专用智能体路由到一起，处理开放的电脑任务。 | 系统 | 评分 3、6、5、3。认可 OSWorld 上数字强、集成多个专用智能体的想法。撤稿前的主要问题是：声称相对先前系统翻倍，但智能体数量和成本高得多，没有同为多智能体的基线；各专用智能体几乎只给了名字，看不出和提示词有何区别；成绩可能来自为基准里的应用定制智能体，而不是路由本身。 | https://openreview.net/forum?id=0sr8bS4S2H |
| Cradle: Empowering Foundation Agents towards General Computer Control | 2025 | ICLR | 拒稿 | 提出通用计算机控制设定，避免为每个虚拟环境手工设计观察和动作空间。 | 系统 | venueid 为拒稿。审稿意见未查到。 | https://openreview.net/forum?id=aIAFDFpNXz |

上表 46 行。按结果计：录用 22，拒稿 13，撤稿 8，只有预印本 2。Explorer 单独算「决定未查到」，不计入这四类。拒稿加撤稿共 21。GUI-World 占两行：NeurIPS 2024 Datasets and Benchmarks 拒稿，ICLR 2025 录用。ScaleCUA、AgentStore、Cradle 来自同一次检索的后半段。

GUI-ReWalk、AgentTrek、Learn-by-interact、OS-Genesis、Explorer、UI-Genie、ZeroGUI 是这次优先找的探索或奖励方向。GUI 显示缺陷/测试方向，在 ICLR 2025、ICLR 2026、NeurIPS 2024、NeurIPS 2025 的这次检索里没有看到带公开审稿的主会投稿；只看到上面这条 CoRR 预印本。

# 录用和拒稿差在哪里

下面每条都对应这次看到的审稿原文，不是会后的猜测。录用论文的审稿里也有批评，差别主要是这些批评有没有打掉核心主张。

1. 公开基准上的提升，必须和同基座、同设定的方法比。OS-ATLAS 的评分 8 写明开源模型在 grounding 和原子动作上超过先前方法，尽管另一条指出 8B 在 AndroidControl 和 GUI-Odyssey 上不如 4B，论文仍然是 Spotlight。WebRL 的评分 8 写相对基线的差距明显。反过来，SpiritSight 的四条审稿都卡在 InternVL 对比 Qwen-VL/CogVLM，以及每个下游集单独微调。GUI-ReWalk 的评分 2 写相对 Qwen2.5-VL-7B 增益不大甚至变差，和 OS-Genesis 的基座还不一样。GUI-Shepherd 的评分 4 直接拿 AndroidWorld 上 40.5% 对审稿人引用的 GUI-Owl-7B 66.4%。MobileGUI-RL 三条都是 4 分，其中一条点名没有和 DigiRL、DistRL 比。

2. 数据工作要证明“新数据改变了模型”，而不是只报规模。AgentTrek 虽被写成功率只有 39.1%、总量仍是几千条，但评分 8 的审稿认可用教程合成降低了人工成本，并在基准上有效，结果是 Spotlight。AutoGUI 有约 70.4 万条，两条评分 3 仍认为拿掉与训练同分布的 FuncPred 之后，相对同基座 SeeClick 的提升不够。Aguvis 的评分 5 写既没有新数据集也没有新模块。InSTA 的 15 万网站被当成贡献，但评分 4 写闭环只跑了一轮，解出来的多是简单检索。OS-ATLAS 被认可的是语料公开且模型结果跟着上去，不是规模本身。ScaleCUA 是 ICLR 2026 Oral，评分 10 写消融盖住了数据规模、分辨率和坐标格式，并给出 MMBench-GUI L1-Hard 94.4%。

3. 新意要落在 GUI 特有的难点上，已有流程换个名字不够。NNetNav 评分 3 写探索再事后贴标签和 BAGEL 的 demonstration-first 相同。过程奖励那篇评分 3 写没有讨论已有 PRM 文献。VEM 评分 2 和 4 写所谓价值环境模型只是二分类打分器，策略更新是标准做法，还把环境交互成本换成了 GPT-4o 标注。InfiGUI-R1 即使评分 6，仍被写成 SFT 再 RLVR 是常规顺序，UI-TARS 已经是 SFT 再 DPO，venueid 仍是拒稿。VistaGUI 和 WorldGUI 的评分 4 都说恢复、批评和反思模块在 Agent S / Agent S2 里已经有了。与此相对，Learn-by-interact 评分 6 明确写 backward construction 是新的；AndroidWorld 评分 6 认可带奖励的动态任务；VideoGUI 评分 8 认可教学视频和分层失败点是新的评测场景。

4. 实验和可复现细节没写清时，系统论文过不了，即使数字看起来好。UItron 有一条评分 6，但两条评分 2 都写没交代基座模型，感知和规划数据的构造也没写。OmniParser 评分 5 写模块拼接达不到该审稿人对 ICLR 创新的标准。CRAB 评分 6 认可跨环境，但评分 3 和 5 写 120 条任务太少、图评估器怎么建没解释。MMBench-GUI 评分 2 写平台全，但没有从统一评测里得到新认识。GUI-R1 评分 4 承认小数据效率，同时写奖励没有统一公式、缺消融。录用侧并非没有这类意见：DigiRL 那条评分 5 也说方法新意有限，DistRL 评分 5 指出正文和附录的模型描述互相矛盾。本次没看到这些论文的 meta-review，不能把单条低分说成“差点被拒”。能确认的是，被拒和撤稿样本里，低分审稿直接否定对比公平性、新意或可复现性，而不是只列写作问题。

和本方向更近的一条：探索合成如果停在静态 grounding、或验证器只是换名的打分模型，审稿人会拒。GUI-ReWalk 被要求看到在线任务成功率；VEM 被要求说明打分器不是被策略钻空子的冻结分类器；过程奖励论文被要求说明和已有 PRM 的差别。GUI 缺陷发现这一类，这次在上述主会的公开决定里没有看到可对照的审稿。

# 打开过的链接

Medium

- https://medium.com/@webagentlab
- https://medium.com/@webagentlab/webagent-review-january-2025-f22ef03506d5
- https://medium.com/@webagentlab/webagent-review-february-2025-e7bf76db67b1
- https://medium.com/@webagentlab/webagent-review-march-2025-6e238c44080c
- https://medium.com/@webagentlab/all-about-webagent-neurips-2024-highlights-38ece42c538a

会议与预印本

- https://aclanthology.org/2025.acl-long.277/
- https://arxiv.org/abs/2502.11357
- https://arxiv.org/abs/2505.23762

OpenReview 论坛页（浏览器打开并看到正文的：SpiritSight；其余决定与审稿字段来自同一次打开的 OpenReview 搜索结果，论坛链接如下）

- https://openreview.net/forum?id=jY2ow7jRdZ
- https://openreview.net/forum?id=n9PDaFNi8t
- https://openreview.net/forum?id=EEgYUccwsV
- https://openreview.net/forum?id=3UKOzGWCVY
- https://openreview.net/forum?id=il5yUQsrjC
- https://openreview.net/forum?id=oVKEAFjEqv
- https://openreview.net/forum?id=LPG8pPSfQD
- https://openreview.net/forum?id=QarKTT5brZ
- https://openreview.net/forum?id=h8LuywKj6N
- https://openreview.net/forum?id=GBfYgjOfSe
- https://openreview.net/forum?id=lIVRgt4nLv
- https://openreview.net/forum?id=4XTvXMSZPO
- https://openreview.net/forum?id=tN61DTr4Ed
- https://openreview.net/forum?id=KjNEzWRIqn
- https://openreview.net/forum?id=jSKtxmxc0M
- https://openreview.net/forum?id=O0nBMRlkc8
- https://openreview.net/forum?id=mRIQz8Zd6O
- https://openreview.net/forum?id=3Gzz7ZQLiz
- https://openreview.net/forum?id=3uUmJzSSOW
- https://openreview.net/forum?id=6iRZvJiC9Q
- https://openreview.net/forum?id=xtysskccFc
- https://openreview.net/forum?id=yUEBXN3cvX
- https://openreview.net/forum?id=y2oVMMRzRE
- https://openreview.net/forum?id=pZQvv5C7WL
- https://openreview.net/forum?id=FHtHH4ulEQ
- https://openreview.net/forum?id=wl4c9jvcyY
- https://openreview.net/forum?id=jR6YMxVG9i
- https://openreview.net/forum?id=C6hUK6Q1Pi
- https://openreview.net/forum?id=LrXGarwVO8
- https://openreview.net/forum?id=ld2bbwEvSu
- https://openreview.net/forum?id=wywgRd1MUQ
- https://openreview.net/forum?id=9hM4YRMhfT
- https://openreview.net/forum?id=zibL9wu2LI
- https://openreview.net/forum?id=qqKJjwibsp
- https://openreview.net/forum?id=FcZqZH0j1b
- https://openreview.net/forum?id=CG1fqwnFGj
- https://openreview.net/forum?id=Hl3HR6Scs2
- https://openreview.net/forum?id=GQugc1J2kW
- https://openreview.net/forum?id=oJZYmlVtwD
- https://openreview.net/forum?id=hHF5AayC7O
- https://openreview.net/forum?id=iJ3BFUXsKD
- https://openreview.net/forum?id=prCoYC6A9z
- https://openreview.net/forum?id=yBFUqdJFZn
- https://openreview.net/forum?id=0sr8bS4S2H
- https://openreview.net/forum?id=aIAFDFpNXz

OpenReview 搜索接口（返回了上表中的 venue、摘要和 Official Review 字段）

- https://api2.openreview.net/notes/search?term=GUI%20agent&limit=20&source=forum
- 以及按论文标题检索的 `https://api2.openreview.net/notes/search`（`source=all`），标题包括 OS-Atlas、Learn-by-interact、AndroidWorld、DigiRL、OSWorld、WebRL、AgentTrek、GUI-ReWalk、UI-Genie、DistRL、Aguvis、AutoGUI、VEM、Guiding VLM Agents with Process Rewards、OmniParser、BTL-UI、MobileGUI-RL、UItron、MMBench-GUI、VistaGUI、GUI-World、Ferret-UI、Agent S、WorldGUI、NNetNav、VideoGUI、InfiGUI-R1、Synatra、On the Effects of Data Scale、OpenCUA、GUI-Shepherd、InSTA、Learning to Contextualize Web Pages、Mobile-Agent-v2、AutoGuide、CRAB、SpiritSight、Explorer、OS-Genesis、ZeroGUI、GUI testing。BTL-UI 的论坛是 https://openreview.net/forum?id=B0Gfxhr8V5 ，venue 为 NeurIPS 2025 poster，但检索到的审稿 strengths/weaknesses 为空，故未写入上表。
