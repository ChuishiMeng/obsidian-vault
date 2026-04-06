---
title: DA-Disambiguation 项目
type: entity
date: 2026-04-05
tags: [项目, Data Agent, 消歧, Text-to-SQL]
---

# DA-Disambiguation 项目

## 基本信息

| 属性 | 值 |
|------|------|
| **全称** | Data Agent 问答歧义消解 |
| **类型** | 科研项目 |
| **领域** | [[Data Agent]] / 意图理解 |
| **状态** | P2 核心文献完成 |
| **来源** | Tracy 提供的研究材料 |

## 研究目标

构建语义驱动的意图理解智能体，解决用户自然语言与数据查询系统之间的语义对齐问题。

## 核心创新：三层歧义框架

| 层面 | 歧义来源 | 解决方案 |
|------|---------|---------|
| 用户层 | 表达习惯、角色视角、指代省略 | 用户画像学习 |
| 业务层 | 概念定义、计算逻辑、业务规则 | 业务知识图谱 |
| 数据层 | 表映射、字段对应、关联路径 | 增强元数据 |

## 关键论文

- P01 Disambiguate First (2025)：两阶段消歧
- P02 AMBROSIA (2024)：歧义分类基准
- P03 AmbiSQL (2025)：交互式消歧，检测率 87.2%
- P04 EIG (2025)：最优澄清问题选择

## 研究空白

- 用户画像驱动的消歧（Level B）
- 三层协同统一框架（Level C）
- 最小交互策略（Level A，借鉴 EIG）

## 相关项目

- [[entities/da-casebase.md|DA-CaseBase]]：互补（消歧 vs 复用）
- [[entities/ontology-schema.md|Ontology-Schema]]：基础设施支撑

## 相关主题

- [[topics/da-disambiguation.md|DA 消歧研究综述]]
