---
title: DA 消歧研究综述
type: topic
date: 2026-04-05
tags: [研究, Data Agent, 消歧, Text-to-SQL, 意图理解]
---

# DA 消歧研究综述

## 概述

DA-Disambiguation 项目研究 Data Agent 问答场景中的意图消歧问题，提出三层歧义框架（用户层/业务层/数据层），探索语义对齐机制以解决用户自然语言与数据库查询之间的语义鸿沟。

## 关键论文

| # | 论文 | 会议/期刊 | 年份 | 核心贡献 |
|---|------|----------|------|---------|
| P01 | Disambiguate First, Parse Later | arXiv | 2025 | 两阶段消歧：问题→解释→SQL |
| P02 | AMBROSIA | arXiv | 2024 | 歧义分类基准：Scope/Attachment/Vagueness |
| P03 | AmbiSQL | arXiv | 2025 | 交互式消歧，歧义检测准确率 87.2%，SQL 提升 +50% |
| P04 | EIG (Expected Information Gain) | arXiv | 2025 | 最优澄清问题选择策略 |
| P05 | PRACTIQ (Conversational Dataset) | arXiv | 2025 | 对话式 Text-to-SQL 数据集 |
| P05 | PRACTIQ (歧义类型分类) | arXiv | 2025 | 歧义类型分类体系 |
| P13 | Knowledge Base Construction | arXiv | 2025 | Text-to-SQL 知识库构建 |
| P37 | Unanswerables in SQL | arXiv | 2025 | 不可回答 SQL 查询识别 |

## 核心方法论

### 三层歧义框架（本研究核心创新）
| 层面 | 定义 | 歧义来源 | 解决方案 |
|------|------|---------|---------|
| **用户层** | 个人语言特征 | 表达习惯、口语化、角色视角 | 用户画像学习、交互确认 |
| **业务层** | 领域业务知识 | 概念定义、计算逻辑、业务规则 | 业务知识图谱 |
| **数据层** | Schema 歧义 | 表映射、字段对应、关联路径 | 增强元数据 |

### 消歧技术路线
| 方法 | 时机 | 交互方式 | 代表论文 |
|------|------|---------|---------|
| 非交互消歧 | SQL 生成前 | 自动解释 | P01 |
| 交互式消歧 | SQL 生成前 | 多选题 | P03 AmbiSQL |
| 最优问题选择 | SQL 生成中 | 澄清问题 | P04 EIG |

### 歧义叠加效应
歧义不是孤立的：单一歧义 O(n) 种解释，k 个歧义 O(n^k) 种组合。这解释了为什么"让模型猜测"不可靠。

## 与其他主题的关系

- [[topics/da-casebase.md|DA CaseBase 研究]]：消歧解决意图模糊，CaseBase 解决已知意图的复用
- [[topics/ontology-schema.md|本体 Schema 研究]]：知识图谱是消歧的基础设施
- [[topics/virtual-user-research.md|虚拟用户研究]]：用户画像学习可借鉴 Persona 构建

## 研究状态

- **阶段**：P2 核心文献已完成（4 篇深度解读），扩展文献待解读
- **核心发现**：现有方法缺乏三层协同框架，用户画像驱动的消歧是研究空白
