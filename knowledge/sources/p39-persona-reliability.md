---
title: P39 - Persona Prompting 的可靠性评估
date: 2026-04-14
tags: [科研, 虚拟用户, Persona, 问卷调查, 可靠性评估]
paper_id: P39
arxiv: 2602.18462
source: ~/agentKB/Obsidian/AI-Workspace/Research/VirtualUser/review/P39-2026-Synthetic-Survey-Respondents-Reliability.md
---

# P39 - Persona-Conditioned LLMs as Synthetic Survey Respondents

**核心问题**：多属性 Persona Prompting 真能提升可靠性吗？还是反而引入扭曲？

---

## 实验设计

- 数据：WVS-7（世界价值观调查），美国 2,596 名受访者 × 31 道题
- 模型：Llama-2-13B + Qwen3-4B
- Persona：8 个社会人口属性
- 对照：Persona-based (PB) vs Vanilla (V) vs Random (R)

---

## 核心发现：Persona Prompting 效果不一致！

| 条件 | Llama-2 HS | Qwen3 HS |
|------|-----------|----------|
| Random | 0.273 | 0.273 |
| Vanilla（无 Persona） | **0.370** | 0.391 |
| Persona（有 Persona） | 0.366 | **0.398** |

- Persona vs Vanilla **差异统计上不显著**
- Llama-2 加 Persona 反而略差
- 小群体受冲击最大：少数群体被 Persona 扭曲最严重

---

## 关键警示

⚠️ Persona 不是万能药，必须设 Vanilla 基线对照
⚠️ 小群体误差被聚合指标"稀释" → 必须做子群体级分析

---

## 对 virtual-users 的价值

1. HS + SS 双重指标值得采用
2. 温度 0.3、单次生成、严格解析流程可参考
3. 需验证每个属性的独立贡献（消融实验）

---

## 关联

- [[topics/virtual-user-research.md|虚拟用户研究]]
- [[concepts/virtual-user-persona.md|虚拟用户 Persona]]
- [[sources/p38-spirit-persona.md|P38 SPIRIT 框架]]

---

*编译时间：2026-04-15 03:00*