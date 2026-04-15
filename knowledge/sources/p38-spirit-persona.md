---
title: P38 - SPIRIT：人口级 Persona 模拟框架
date: 2026-04-14
tags: [科研, 虚拟用户, Persona, 人口级模拟, SPIRIT]
paper_id: P38
arxiv: 2603.27056
source: ~/agentKB/Obsidian/AI-Workspace/Research/VirtualUser/review/P38-2026-Persona-Population-Scale.md
---

# P38 - Persona-Based Simulation at Population Scale

**核心问题**：仅用人口学信息构造 persona → 模型用刻板印象填充 → 不真实

---

## SPIRIT 框架（两阶段）

### 阶段一：Painter（画像推断）
- 输入：社交媒体历史帖子
- 输出：半结构化 JSON persona + 叙述性 persona
- Schema：Big Five + 世界信念（26维）+ 价值观 + 生活经历 + 观点信念
- 每属性附带置信度 + 理由，避免过度解读

### 阶段二：Reasoner（推理模拟）
- Persona 驱动 LLM 回答问卷
- 输出：选项 + 置信度 + 理由

---

## 核心发现

| 对比 | SPIRIT persona | 人口学 persona |
|------|----------------|----------------|
| 准确率 | 提升 8-9 个百分点 | 基线 |

- 稳定态度（军事/投票 >83%）推断好
- 私密属性（财务/技术 <26%）推断差
- 校准后 persona bank 在堕胎、移民议题上紧贴真实民调

---

## 对 virtual-users 的价值

1. **Persona Schema**：Big Five + 世界信念 + 价值观 → 虚拟用户画像模板
2. **校准方法**：Raking 校准到人口基准
3. **核心启示**：人口学画像不够用，必须有丰富的个体差异信号

---

## 关联

- [[topics/virtual-user-research.md|虚拟用户研究]]
- [[concepts/virtual-user-persona.md|虚拟用户 Persona]]
- [[sources/p36-llm-user-simulation-survey.md|P36 LLM 用户模拟综述]]

---

*编译时间：2026-04-15 03:00*