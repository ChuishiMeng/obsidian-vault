---
title: "LLM 对话式用户模拟"
date: 2026-04-13
tags: [概念, LLM, 用户模拟, Persona, 虚拟用户]
---

# LLM 对话式用户模拟

用大语言模型模拟真实用户的行为和交互模式，用于对话系统评测、推荐系统测试、用户研究等场景。

## 四维分类体系（P36 Survey, EACL 2026）

- **Who**：模拟粒度 — General User → Persona → Role Play → Individual → Hybrid
- **What**：交互模式 — Human-AI / Human-Human / AI-AI / Many-Human-AI
- **How**：技术方法 — Prompt-based / RAG / Fine-tuning / RL-DPO
- **Why**：应用场景 — Recommendation / QA / Text Generation

## 核心挑战

1. **一致性**（Consistency）：多轮对话中角色偏移
2. **多样性**（Diversity）：LLM倾向生成同质化内容
3. **偏见与毒性**（Bias & Toxicity）：敏感人口属性风险

## 与传统用户研究的关系

LLM 模拟不是替代真实用户研究，而是补充手段。适合大规模预筛、A/B测试预评估、边缘case覆盖。

---

## 关联

- [[sources/p36-llm-user-simulation-survey.md|P36 综述]]
- [[topics/virtual-user-research.md|虚拟用户研究]]
- [[sources/sbti-personality-algorithm.md|SBTI 人格算法]]
- [[sources/virtual-user-product-strategy.md|虚拟用户技术产品化策略]]
