# Applied ML 与 Agentic AI 项目集

[English](README.md)

**我做的事是：在验证成本很高的地方，判断一个模型或 agent 的决策能不能信**，并搭建基于这个判断去行动的系统。同一套方法贯穿两个领域：排序和广告策略，一个错误的离线数字会让一次退化上线；生产级基因组学，一个错误判断的代价是一次正交实验或一次临床误读。

---

## 两条方向

### [推荐、排序与广告 →](https://github.com/arcadianlyric/RecSys_AgenticAI_MLOps)
*主方向：推荐 / 广告 / 排序 Applied Scientist*

- **在真实日志数据上走完一条完整决策链：** off-policy evaluation（RecSys_OPE）、用第二份日志检验的离策学习（RecSys_OPL）、A/B 分析与 uplift 定向（RecSys_ABtest）。
- **知道离线评估回答不了什么：** OPE 置信区间实际只有 53% 的时候覆盖真值；一个 +416% 的"胜利"其实是切分泄漏；uplift 门控打得过随机定向，却输给生产 bandit。
- **把 agent 和 LLM judge 当作估计器：** 用构造陷阱打分的上线决策分析师，以及用 prediction-powered inference 校正的 LLM judge（AgenticEval）。

### [基因组学、Agentic AI 与 AI for Science →](https://github.com/arcadianlyric/AppliedML_AgenticAI_BioMed)
*生物医学 ML / agentic AI 岗位*

- **生产级基因组 ML：** WGS pipeline、测序漂移监控、DeepVariant 适配、证据感知组装和分子连锁变异抛光。
- **证据锚定的 agent：** 正确性校验交给 ClinGen/ClinVar、而不是模型自己判断的多智能体变异解读系统，以及多智能体 pipeline 设计器。
- **AI for Science 研究：** 一项 risk–coverage 审计，回答模型置信度能替代哪些验证实验，以及在哪里因为一批"自信但错"的判断而无法替代。

---

## 一套方法，两个领域

两条方向是同一件事，只是作用在不同的决策上。每一行是一个机制，两边各给一条证据。

| 机制 | 推荐 / 广告 | 基因组学 / AI4S |
|---|---|---|
| **有偏估计器在大样本下变得"自信但错"** | direct-method OPE 的 95% 置信区间覆盖真值的比例是 0%（RecSys_OPE / RecSys_OPL）；LLM judge 同样是 0%（AgenticEval） | 用模型置信度排序，5–10% 的验证预算就能捞回大部分漏判变异；但预算超过 15% 后，剩下的每一个漏判都是高置信度的（AI4S risk–coverage 审计） |
| **结果看起来漂亮，回答的却是错的问题** | 随机切分让一个策略看起来比生产策略好 +416%，按时间切分后约等于零（RecSys_OPL） | 一个 AUC 0.9995 的完整性分类器，预测的目标是错的，而且判别方向相反（denovo_OLC） |
| **正确性校验放在模型自身循环之外** | 实验分析 agent 只能陈述来自 tool call 的数字，每个数字都被审计（RecSys_ABtest Part C） | 发现生成器自报的引用无法解析后，变异解读改为对照一份冻结的 ClinGen/ClinVar 金标准集校验（PhasedVariants AgenticCurator） |
| **决策本质是算术的地方用确定性规则** | 统计量由工具计算，LLM 只负责解读（RecSys_ABtest） | ClinGen 分级由打分引擎计算，LLM 只负责抽取证据（PhasedVariants）；上线的是一条单阈值规则而不是模型（denovo_OLC） |
| **负面结果反过来塑造设计** | uplift 门控输给生产 bandit，离线学到的策略也都没打赢它（RecSys_ABtest、RecSys_OPL） | 一个改善一条染色体、弄坏四条的"修复"被否决，异常如实公开（cLFR_SNVpolish） |

---

## 架构、需求洞察与批判性思维

Agent 现在已经能"快速交付"——重构一段代码、重调一个 pipeline、跑一遍并报告指标有没有变好——而这种速度正在快速商品化：让 agent 对着一个目标跑一遍、拿到一个答案，已经不再是区分一个人和另一个人的地方。Agent 自己给不了的东西，在这个速度的**上游和下游**：先判断这个系统到底该优化什么，而不是接受它给出的第一个指标；以及识别一个跑得快、看起来很自信的结果什么时候其实是错的。这正是一个优秀 PM 或资深科学家在评审 roadmap 时所依赖的判断力，而这个 portfolio 是直接把它做出来给你看，而不是停留在自我宣称：

| 能力 | 在这个 Portfolio 里的体现 | 证据 |
|---|---|---|
| **需求洞察 / 定义该优化什么** | 每个项目都先判断系统到底该优化什么，而不是接受摆在面前的第一个指标：从明确的痛点或未解决问题出发，而不是先有方案再找场景；一旦发现原有设计答不了真正的问题，就会重新定义范围 | BioMed 方向的 7 个企业级 agentic AI 痛点框架；RecSys_OPE 提出的"让 OPE 在实践中变难的两个开放问题"；RecSys_OPL 在证明为 10–20% 的 lift 背书需要约 20 倍现有数据之后，把问题从"训出更好的策略"重新定义为"OPE 到底能不能为新策略背书"；PhasedVariants Step 4 发现"生成器和评估器共享同一个模型家族和 prompt 谱系，因此这个循环内部产出的任何数字都无法自证"，这个发现直接触发了架构重设计，把正确性校验路由到外部信源 |
| **架构与取舍设计** | 对确定性规则、ML、LLM 判断各自该用在哪里，做出经过论证的主动选择，而不是默认"到处加 agent" | PhasedVariants 的"混合规则 + Agentic"架构论证表；denovo_OLC 的分级证据阶梯（快速路径 → 锚点延伸 → 集体救援，只在必要时才升级）及其评估过的三种 ML 集成方案；RecSys_ABtest 的 A0→A3 消融阶梯，逐个加入工具、planning 和异厂商 verifier，并测量每一步带来了什么 |
| **批判性思维 / 识别"自信但错"的结果** | 一个跑得快、看起来很自信的结果，无论是模型还是 agent 跑一遍给出来的，在被信任之前都会先被压力测试，而不是因为它自己报出来的数字好看就直接上线；负面和证伪性的发现会被公开并反过来影响最终设计，而不是被搁置不提 | denovo_OLC 六条被否决的"让模型直接介入决策"路径，其中一个分类器 AUC 高达 0.9995，却预测了错误的目标且判别方向相反；RecSys_OPL 把一个 +416% 的策略"胜利"追溯为切分泄漏；cLFR_SNVpolish 公开了 chr15/chr19 异常，并否决了一个能改善单条染色体、却让全基因组结果净变差的"修复方案"；RecSys_OPE 的因果边界测试证明一个看似正当的偏差修正也可能让误差变大；我自己在 AI4S 研究里的三个结论被具体信号推翻并如实记录，其中一条手写的弃权规则只捞回 5% 的漏判，而单纯按置信度排序能捞回 93% |

这些不是无关紧要的旁注，它们是系统上线后仍能保持诚实所需要的纪律，也正是 demo 和产品的分界线。

---

## 项目索引

| 项目 | 方向 | 一句话 |
|---|---|---|
| [RecSys_OPE](https://github.com/arcadianlyric/RecSys_OPE) | 推荐 | Off-policy evaluation：6 种估计器对照 ground truth 的基准测试，以及位置偏差校正什么时候有用、什么时候只增加方差 |
| [RecSys_OPL](https://github.com/arcadianlyric/RecSys_OPL) | 推荐 | 真实日志上的离策学习，用第二份日志检验 OPE；切分泄漏、置信区间覆盖率、reward model 过度优化 |
| [RecSys_ABtest](https://github.com/arcadianlyric/RecSys_ABtest) | 推荐 | A/B 分析工具箱、uplift/CATE、agentic 上线决策分析师，以及用 OPE 定价的 uplift 门控 |
| [AgenticEval](https://github.com/arcadianlyric/AgenticEval) | 推荐 / agent | 在 SWE-bench 轨迹上把 LLM judge 当作有偏估计器，用 PPI 校正（试点规模） |
| [AgenticGEM](https://github.com/arcadianlyric/AgenticGEM_DataDrift_AutoRetrainer) | 推荐 / agent | 广告排序的 LangGraph 漂移 → 评估 → 重训决策循环 |
| [Movie RecSys](https://github.com/arcadianlyric/RS_movies) | 推荐 / 基础设施 | offline / nearline / online 三层推荐服务架构 |
| [MLOps Taxi Platform](https://github.com/arcadianlyric/Agentic_MLOps_Platform) | 基础设施 | TFX、Feast、MLflow、Kafka、Prometheus/Grafana、Kubernetes |
| [DNBSEQ Complete WGS Pipeline](https://github.com/Complete-Genomics/DNBSEQ_Complete_WGS) | 基因组学 | 可审计、可复现的生产级 WGS pipeline |
| [LFR Data Monitor](https://github.com/arcadianlyric/LFR_DataMonitor) | 基因组学 | 在模型静默退化之前做测序 QC 和漂移检测 |
| [DeepVariant Fine-Tuning](https://github.com/arcadianlyric/GoogleDeepVariant_FineTuning) | 基因组学 | 测序分布偏移下的 detect → retrain → validate |
| [denovo_OLC](https://github.com/Complete-Genomics/cLFR_denovo_OLC) | 基因组学 | 证据感知组装；六条模型介入路径被否决，上线更便宜的规则 |
| [cLFR_SNVpolish](https://github.com/Complete-Genomics/cLFR_SNVpolish) | 基因组学 / AI4S | 分子连锁置信度模型；AI4S risk–coverage 审计的基础 |
| [ZeroShot Immune Feature Drift](https://github.com/arcadianlyric/ZeroShot_ImmuneFeatureDrift) | 基因组学 | scGPT zero-shot 漂移监控 pipeline（等待真实纵向数据） |
| [PhasedVariants AgenticCurator](https://github.com/arcadianlyric/PhasedVariants_AgenticCurator) | 基因组学 / agent | 对照 ClinGen/ClinVar 校验的多智能体变异解读 |
| [Agentic bioArchitect](https://github.com/arcadianlyric/Agentic_bioArchitect) | 基因组学 / agent | 多智能体设计并生成生信 pipeline |
| [RS ColdStart GraphRAG LLM](https://github.com/arcadianlyric/RS_coldstart_graphRAG_LLM) | 早期作品 | 用多模态 GraphRAG 解决电商冷启动（2025 年原型） |

---

当 AI 越来越能自己写代码，真正拉开差距的能力正在转向：定义对的问题、架构出真正能扛住生产环境的系统，以及分辨一个结果是真的可信还是只是看起来可信。这正是这个 portfolio 想要证明的判断力。
