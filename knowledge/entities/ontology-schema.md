---
title: Ontology-Schema 项目
type: entity
date: 2026-04-05
tags: [项目, Text-to-SQL, Schema Linking, Ontology]
---

# Ontology-Schema 项目

## 基本信息

| 属性 | 值 |
|------|------|
| **全称** | Ontology-Schema 联合召回框架 |
| **类型** | 科研项目 |
| **领域** | [[Data Agent]] / Text-to-SQL |
| **状态** | P4 方案设计进行中 |
| **创建时间** | 2026-02-19 |

## 研究目标

构建异构图谱驱动的 Schema + 业务知识联合召回系统，解决企业级 Text-to-SQL 中的语义鸿沟和知识割裂问题。

## 核心创新：三层图谱结构（HKSG）

| 层级 | 内容 | 作用 |
|------|------|------|
| L0 元模型 | EntityType, RelationType, Constraint | 规范约束 |
| L1 Ontology | 业务实体、术语、计算公式 | 桥接业务与数据 |
| L2 Schema | 数据库表、字段、外键 | 物理映射 |

## 联合召回流程

问题理解 → L1 Ontology 召回 → L2 Schema 召回 → 元路径约束验证 → 最小完备集提取

## 研究空白（4 个切入点）

1. 缺乏 Ontology 层（13 篇论文均未引入）
2. Schema 召回与知识召回割裂
3. 缺乏结构化约束机制
4. 多智能体成本高（5-10+ 次 LLM 调用）

## 预期效果

- Recall@K 提升 10%+（vs RESDSQL, BOGIN）
- EX (Execution Accuracy) 提升 5%+（vs BIRD Baseline）
- Token 消耗降低 30%+

## 关键论文

- P01 LinkAlign (⭐⭐⭐⭐⭐)：三阶段框架
- P04 RSL-SQL (⭐⭐⭐⭐⭐)：双向链接
- P08 RAT-SQL (⭐⭐⭐⭐⭐)：开创性关系感知编码

## 相关项目

- [[entities/da-casebase.md|DA-CaseBase]]：Schema Linking 是前置步骤
- [[entities/da-disambiguation.md|DA-Disambiguation]]：Ontology 提供业务知识支撑

## 相关主题

- [[topics/ontology-schema.md|本体 Schema 研究综述]]
