---
title: "P36: A Survey on LLM-based Conversational User Simulation"
date: 2026-04-13
tags: [科研, 文献综述, 虚拟用户, 用户模拟, EACL 2026, LLM]
source: "EACL 2026 Long Paper | Adobe Research + Vanderbilt/Yale/USC 等"
paper_id: P36
venue: EACL 2026
pages: "26页正文 + 10页附录"
authors: "20+人（Adobe Research主导）"
---

# P36: A Survey on LLM-based Conversational User Simulation

## 核心贡献

四维分类体系，目前对 LLM 对话式用户模拟最系统的梳理：

- **Who（§3）**：模拟谁？General User → Persona → Role Play → Individual → Hybrid（粒度递增）
- **What（§4）**：模拟什么交互？Human-AI / Human-Human / AI-AI / Many-Human-AI / Hybrid
- **How（§5）**：怎么模拟？Prompt-based / RAG / Fine-tuning / RL-DPO / Hybrid
- **Applications（§8）**：用在哪？Recommendation / Summarization / Text Generation / QA / Others

## Who 维度详解

四层粒度模型（从粗到细）：

1. **General User（§3.1）** — 默认无特征用户。代表：M-DPO、SDPO、AgentQ
2. **Persona-level（§3.2）** — 人口统计学特征定义 ⭐ 核心研究区域
   - 方法：人口属性Prompting、心理测量建模（PsyPlay）、特质注入架构（HEXACO/Big Five）
   - 关键问题：公平性、偏见
3. **Role Play（§3.3）** — 基于角色/名人的模拟。代表：CharacterLLM、Ditto、RoleCraft
4. **Individual User（§3.4）** — 基于真实个人历史数据的最细粒度模拟
   - 方法：Profile注入、对话历史建模、多会话记忆（Mem0）
   - 数据集：PersonaChat、MSC、RealPersonaChat

## What 维度 — 交互模式

- Human-AI / Human-Human / AI-AI / Many-Human-AI / Hybrid

## How 维度 — 技术方法

| 方法 | 代表工作 |
|------|---------|
| Prompt-based | 最通用，上限受限于基础模型 |
| RAG | Always-on / Adaptive / Goal-driven |
| Fine-tuning | DAUS、SoulChat、PlatoLM、BiPO |
| RL/DPO | M-DPO、SDPO、DMPO、UGRO+PPO |

关键发现：Fine-tuning 在角色一致性和领域知识上优于 RAG；RL/DPO 是多轮对话优化的前沿方向。

## 评估方法

1. 传统指标：BLEU、ROUGE、Slot-F1（不够用）
2. LLM-as-Judge：定义维度→Rubric→CoT打分（当前主流）
3. Trustworthy & Causal Evaluation（新兴方向）

## Open Challenges

1. **一致性** — 多轮对话中角色偏移、事实矛盾
2. **多样性** — LLM倾向生成"礼貌、同质化"内容
3. **偏见与毒性** — Persona涉及敏感人口属性时风险突出

## 与我们研究的关联

| 我们的研究点 | 综述对应位置 |
|------------|------------|
| 虚拟用户态度一致性 | §9.1 Consistency + §3.2 Persona-level |
| CQCB 基准 | §6 Evaluation（LLM-as-Judge 范式） |
| ConsistAgent 框架 | §5.3-5.4（Fine-tuning + RL/DPO 路径） |
| Persona 生成 | §3.2（Demographic prompting + 心理测量） |
| 分布匹配 vs 行为真实性 | §9.2 Diversity（同质化问题） |

**一句话评价**：目前该领域最全面的 taxonomy 工作，四维分类体系定义清晰，open challenges 与我们的研究方向高度吻合。KDD 论文 Related Work 必引文献。

---

## 关联

- [[topics/virtual-user-research.md|虚拟用户研究]]
- [[sources/virtual-user-product-strategy.md|虚拟用户技术产品化策略]]
- [[sources/sbti-personality-algorithm.md|SBTI 人格算法]]
- [[concepts/llm-user-simulation.md|LLM 用户模拟]]
