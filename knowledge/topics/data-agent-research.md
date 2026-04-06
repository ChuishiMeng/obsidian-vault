---
title: Data Agent 研究
aliases: [Data Agent Research]
tags: [主题, Data Agent, NL2SQL, 研究]
---

# Data Agent 研究

## 主题概述

Data Agent是AI Agent在数据分析领域的应用，能够将自然语言转换为SQL查询并执行分析。本主题收集相关研究、竞赛和实战经验。

## 关键文章列表

| 日期 | 标题 | 来源 |
|------|------|------|
| 2026-03-28 | [[sources/kdd-cup-2026-data-agents.md|KDD Cup 2026 Data Agents竞赛]] | 微信公众号 |
| 2026-03-28 | [[sources/kdd-cup-2026-plan.md|KDD Cup 2026参赛计划]] | 内部计划 |
| 2026-03-31 | [[sources/data-agent-hard-to-build.md|Data Agent挺难做的]] | 奇诺微信公众号 |

## 核心洞察

### 1. KDD Cup 2026 四大核心能力

1. **智能分解与规划** — 将高层级问题自主拆解为可执行计划
2. **灵活工具调用** — 精准选择Python、SQL、API等工具
3. **异构数据融合推理** — 结构化/非结构化/多模态数据交叉推理
4. **结果综合与决策** — 智能综合多步骤中间结果

### 2. Data Agent为什么难做？

作者花3个月周末从零搭建Data Agent，迭代三版后放弃：

- **第一版：语义层约束** — 约束太死，只能做L2层数据获取
- **第二版：Meta-Agent** — 复杂度爆炸，维护困难
- **第三版：Case-Based** — 查询多样性不足

### 3. 可行方案：Skills

把业务隐性知识固化进Skill，不追求通用Data Agent，只服务特定场景。

## 关联概念

- [[concepts/data-agent.md|Data Agent]]
- [[concepts/ai-agent.md|AI Agent]]

## 关联主题

- [[topics/ai-agent-trends.md|AI Agent趋势]]
