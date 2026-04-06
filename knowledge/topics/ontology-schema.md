---
title: 本体 Schema 研究综述
type: topic
date: 2026-04-05
tags: [研究, Text-to-SQL, Schema Linking, Ontology, 知识图谱]
---

# 本体 Schema 研究综述

## 概述

Ontology-Schema 项目研究如何构建异构图谱驱动的 Schema + 业务知识联合召回系统，解决企业级 Text-to-SQL 中的语义鸿沟（业务术语≠数据库字段）和知识割裂（Schema 召回与知识召回独立处理）问题。

## 关键论文

| # | 论文 | 会议/期刊 | 年份 | 核心贡献 |
|---|------|----------|------|---------|
| P01 | LinkAlign | arXiv | 2025 | 多数据库 Schema Linking 三阶段框架 |
| P02 | SchemaGraphSQL | arXiv | 2025 | 图算法 Schema Linking，零样本 |
| P03 | DCG-SQL | arXiv | 2025 | Schema Link Graph 联合表示 |
| P04 | RSL-SQL | arXiv | 2024 | 双向 Schema Linking（问题→Schema + Schema→问题）|
| P05 | MAG-SQL | arXiv | 2024 | 多智能体 Soft Schema Linking |
| P06 | GraphMatcher | arXiv | 2024 | Graph Attention 用于 Ontology Matching |
| P07 | DataFactory | arXiv | 2026 | Database + KG 双团队协作 |
| P08 | RAT-SQL | ACL | 2020 | 关系感知 Schema 编码（开创性工作） |
| P09 | RESDSQL | AAAI | 2023 | 解耦 Schema Linking 和 SQL 生成 |

## 核心方法论

### 三层图谱结构（HKSG，本研究核心创新）
| 层级 | 内容 | 作用 |
|------|------|------|
| **L0 元模型层** | EntityType, RelationType, Attribute, Constraint | 约束 L1 和 L2 |
| **L1 Ontology 层** | 业务实体、关系、术语、计算公式 | 桥接业务与数据 |
| **L2 Schema 层** | 数据库表、字段、外键 | 物理存储映射 |

### 联合召回算法
1. 问题理解 → 实体识别 + 意图识别 + 术语映射
2. L1 召回（Ontology）→ 实体/关系/术语展开
3. L2 召回（Schema）→ 表映射 + 字段映射 + 外键补全
4. 元路径约束验证 → 语义约束路径剪枝
5. 最小完备集提取 → 完备性检查 + 最小化

### 研究空白（本研究切入点）
1. **缺乏 Ontology 层**：全部 13 篇论文均未系统引入
2. **召回割裂**：Schema 召回与知识召回独立处理
3. **缺乏结构约束**：依赖 LLM 验证而非图谱推理
4. **多智能体成本高**：需要 5-10+ 次 LLM 调用

## 与其他主题的关系

- [[topics/da-casebase.md|DA CaseBase 研究]]：Schema Linking 是 CaseBase 的前置步骤
- [[topics/da-disambiguation.md|DA 消歧研究]]：Ontology 层提供业务知识支撑消歧
- [[concepts/data-agent.md|Data Agent]]：联合召回是 Data Agent 的核心能力

## 研究状态

- **阶段**：P2 文献调研已完成（13 篇），P4 方案设计进行中
- **预期效果**：Recall@K 提升 10%+，EX 提升 5%+，Token 降低 30%+
