# Applied ML & Agentic AI Portfolio

[中文版](README_CN.md)

**I evaluate whether a model's or an agent's decision can be trusted when checking it is expensive**, and build the systems that act on that answer. The same discipline runs through two domains: ranking and ads policies, where a wrong offline number ships a regression, and production genomics, where a wrong call costs an orthogonal experiment or a clinical misread.

---

## Two Tracks

### [Recommendation, Ranking & Ads →](https://github.com/arcadianlyric/RecSys_AgenticAI_MLOps)
*Primary track: Applied Scientist, recommendation / ads / ranking*

- **A full decision pipeline on real logged data:** off-policy evaluation (RecSys_OPE), offline policy learning checked against a second log (RecSys_OPL), and A/B analysis with uplift targeting (RecSys_ABtest).
- **Knowing what offline evaluation cannot answer:** OPE confidence intervals that cover the truth 53% of the time, a +416% "win" that was split leakage, and an uplift gate that beats random targeting but loses to the production bandit.
- **Agents and LLM judges treated as estimators:** a ship/no-ship analyst scored on constructed traps, and LLM judges corrected with prediction-powered inference (AgenticEval).

### [Genomics, Agentic AI & AI for Science →](https://github.com/arcadianlyric/AppliedML_AgenticAI_BioMed)
*Biomedical ML / agentic AI roles*

- **Production genomics ML:** WGS pipeline, sequencing drift monitoring, DeepVariant adaptation, evidence-aware assembly and molecule-linkage variant polishing.
- **Evidence-grounded agents:** a multi-agent variant curator whose correctness checks run against ClinGen/ClinVar rather than its own judgment, and a multi-agent pipeline designer.
- **AI for Science research:** a risk–coverage audit of which validation experiments a model's confidence can replace, and where a residue of confidently-wrong calls makes that impossible.

---

## One Discipline, Two Domains

The two tracks are the same work applied to different decisions. Each row is one mechanism, with evidence from both sides.

| Mechanism | Recommendation / Ads | Genomics / AI4S |
|---|---|---|
| **A biased estimator becomes confidently wrong at scale** | Direct-method OPE: 95% CIs cover the truth 0% of the time (RecSys_OPE / RecSys_OPL); LLM judges: the same 0% (AgenticEval) | Model confidence recovers most missed variants at a 5–10% validation budget, but past 15% every remaining miss is a confident one (AI4S risk–coverage audit) |
| **A clean-looking result that answers the wrong question** | A random split made a policy look +416% better than production; under temporal splits it is about zero (RecSys_OPL) | A completeness classifier with AUC 0.9995 that predicted the wrong target, with the direction reversed (denovo_OLC) |
| **Correctness is checked outside the model's own loop** | The experiment-analyst agent may only state numbers that come from tool calls, and every number is audited (RecSys_ABtest Part C) | Variant interpretation is checked against a frozen ClinGen/ClinVar gold set after the generator's own citations proved unresolvable (PhasedVariants AgenticCurator) |
| **Deterministic rules where the decision is arithmetic** | Statistics computed by tools, LLM restricted to interpretation (RecSys_ABtest) | ClinGen tiers computed by a scoring engine, LLM restricted to evidence extraction (PhasedVariants); a one-threshold rule shipped instead of a model (denovo_OLC) |
| **Negative results shape the design** | Uplift gate loses to the production bandit; no offline policy beats it either (RecSys_ABtest, RecSys_OPL) | A "fix" that helped one chromosome and broke four was rejected and the anomaly disclosed (cLFR_SNVpolish) |

---

## Architecture, Requirements, and Critical Thinking

Agents can now deliver fast (refactor a codebase, retune a pipeline, and report whether a metric moved), and that speed is becoming a commodity: pointing an agent at a target and getting a quick answer is no longer what differentiates one contributor from another. What agents do not supply on their own sits upstream and downstream of that speed: deciding what the system should actually be optimizing before anything runs, and recognizing when a fast, confident-looking result is wrong. That is the same judgment a strong PM or a senior scientist brings to a roadmap review, and it is demonstrated directly in this portfolio, not just claimed:

| Capability | What It Looks Like Here | Evidence |
|---|---|---|
| **Problem framing / defining what to optimize** | Every project starts by deciding what the system should actually optimize rather than accepting the first metric on offer: an explicit pain-point or open-question framing, not a solution looking for a use case; designs get re-scoped once they turn out not to answer the real question | The 7-pain-point enterprise agentic AI framework (BioMed track); RecSys_OPE's "two open problems that make OPE hard in practice"; RecSys_OPL re-scoped from "train a better policy" to "can OPE certify a new policy at all", after showing a 10–20% lift would need about 20× the available data; PhasedVariants' Step 4 finding that generator and evaluator "share a model family and prompt lineage, so no number produced inside that loop can certify it", which triggered a redesign to route correctness checks to external sources |
| **Architecture & trade-off design** | Deliberate, argued decisions about where determinism, rules, ML, and LLM judgment each belong; not a default of "add an agent everywhere" | PhasedVariants' "Hybrid Rule-Based + Agentic" architecture-rationale table; denovo_OLC's evidence ladder (fast path → anchor extension → collective rescue, escalating only when needed) and its three evaluated ML-integration schemes; RecSys_ABtest's A0→A3 ablation ladder, which adds tools, planning and a different-vendor verifier one at a time and measures what each buys |
| **Critical thinking / catching a confidently-wrong result** | A fast, confident-looking result (the kind a model or an agent hands back after one pass) gets stress-tested before being trusted, not shipped on the strength of its own reported number; negative and disqualifying findings are disclosed and shape the final design rather than being filed away | denovo_OLC's six rejected model-in-the-loop paths, including a classifier with AUC 0.9995 that turned out to predict the wrong target with a reversed discrimination direction; RecSys_OPL's +416% policy "win" traced to split leakage; cLFR_SNVpolish's disclosed chr15/chr19 anomaly and a rejected "fix" that improved one chromosome while making the genome-wide result net negative; RecSys_OPE's causal-boundary test proving a bias correction that looks justified can still increase error; three of my own AI4S conclusions overturned by specific signals and recorded as such, including a hand-written abstention rule that recovered 5% of misses where plain confidence ranking recovered 93% |

These aren't incidental footnotes; they're the discipline that keeps a system honest after it ships, which is what separates a demo from a product.

---

## Project Index

| Project | Track | One line |
|---|---|---|
| [RecSys_OPE](https://github.com/arcadianlyric/RecSys_OPE) | RecSys | Off-policy evaluation: six estimators benchmarked against ground truth, and when position-bias correction helps vs only adds variance |
| [RecSys_OPL](https://github.com/arcadianlyric/RecSys_OPL) | RecSys | Off-policy learning on real logs, with OPE checked against a second log; split leakage, CI coverage, reward-model overoptimization |
| [RecSys_ABtest](https://github.com/arcadianlyric/RecSys_ABtest) | RecSys | A/B analysis toolkit, uplift/CATE, an agentic ship/no-ship analyst, and an uplift gate priced by OPE |
| [AgenticEval](https://github.com/arcadianlyric/AgenticEval) | RecSys / agents | LLM judges as biased estimators on SWE-bench trajectories, corrected with PPI (pilot scale) |
| [AgenticGEM](https://github.com/arcadianlyric/AgenticGEM_DataDrift_AutoRetrainer) | RecSys / agents | LangGraph drift → evaluate → retrain-decision loop for ads ranking |
| [Movie RecSys](https://github.com/arcadianlyric/RS_movies) | RecSys / infra | Offline / nearline / online three-tier recommendation serving |
| [MLOps Taxi Platform](https://github.com/arcadianlyric/Agentic_MLOps_Platform) | Infra | TFX, Feast, MLflow, Kafka, Prometheus/Grafana, Kubernetes |
| [DNBSEQ Complete WGS Pipeline](https://github.com/Complete-Genomics/DNBSEQ_Complete_WGS) | Genomics | Auditable, reproducible production WGS pipeline |
| [LFR Data Monitor](https://github.com/arcadianlyric/LFR_DataMonitor) | Genomics | Sequencing QC and drift detection before models silently degrade |
| [DeepVariant Fine-Tuning](https://github.com/arcadianlyric/GoogleDeepVariant_FineTuning) | Genomics | Detect → retrain → validate under sequencing distribution shift |
| [denovo_OLC](https://github.com/Complete-Genomics/cLFR_denovo_OLC) | Genomics | Evidence-aware assembly; six model-in-the-loop paths rejected, a cheaper rule shipped |
| [cLFR_SNVpolish](https://github.com/Complete-Genomics/cLFR_SNVpolish) | Genomics / AI4S | Molecule-linkage confidence model; basis of the AI4S risk–coverage audit |
| [ZeroShot Immune Feature Drift](https://github.com/arcadianlyric/ZeroShot_ImmuneFeatureDrift) | Genomics | scGPT zero-shot drift monitoring pipeline (awaiting real longitudinal data) |
| [PhasedVariants AgenticCurator](https://github.com/arcadianlyric/PhasedVariants_AgenticCurator) | Genomics / agents | Multi-agent variant interpretation checked against ClinGen/ClinVar |
| [Agentic bioArchitect](https://github.com/arcadianlyric/Agentic_bioArchitect) | Genomics / agents | Multi-agent design and generation of bioinformatics pipelines |
| [RS ColdStart GraphRAG LLM](https://github.com/arcadianlyric/RS_coldstart_graphRAG_LLM) | Earlier work | Multimodal GraphRAG for e-commerce cold start (2025 prototype) |

---

As AI writes more of the code, the gap between contributors moves to defining the right problem, architecting systems that hold up in production, and telling a result that is trustworthy from one that only looks trustworthy. That judgment is what this portfolio is built to demonstrate.
