---
title: Data Agent
aliases: [数据智能体, NL2SQL Agent]
tags: [概念, AI, 数据分析, NL2SQL]
---

# Data Agent (数据智能体)

## 概念定义

**Data Agent**是专门用于数据分析的智能体，能够将自然语言问题转换为数据库查询（SQL），执行分析并返回业务洞察。

## 核心能力

1. **智能分解与规划** — 将高层级问题自主拆解为可执行计划
2. **灵活工具调用** — 精准选择Python、SQL、API等工具
3. **异构数据融合推理** — 结构化/非结构化/多模态数据交叉推理
4. **结果综合与决策** — 智能综合多步骤中间结果

## 为什么Data Agent难做？

根据实战经验，Data Agent面临以下挑战：

- **语义层约束太死** — 只能做L2层数据获取，无法做L3层分析诊断
- **Meta-Agent复杂度爆炸** — 多Agent分工导致维护困难
- **Case-Based查询多样性不足** — 历史查询无法覆盖新场景

## 可行方案

**Skills方案** — 把业务隐性知识固化进Skill，不追求通用Data Agent，只服务特定场景。

## 关联来源

- [[sources/data-agent-hard-to-build.md|Data Agent挺难做的]]
- [[sources/kdd-cup-2026-data-agents.md|KDD Cup 2026 Data Agents竞赛]]

## 关联主题

- [[topics/data-agent-research.md|Data Agent研究]]
