---
title: Schema Linking
type: concept
date: 2026-04-05
tags: [概念, Text-to-SQL, Data Agent, 数据库]
---

# Schema Linking（模式链接）

## 定义

Text-to-SQL 系统中将自然语言问题中的词汇映射到数据库 Schema（表名、列名）的过程。是决定 Text-to-SQL 系统上限的关键步骤。

## 核心方法演进

| 时期 | 方法 | 代表论文 | 局限 |
|------|------|---------|------|
| 2020 | 关系感知编码 | RAT-SQL (ACL) | 仅编码 Schema |
| 2023 | 解耦 Linking + SQL 生成 | RESDSQL (AAAI) | 无业务知识 |
| 2024 | 双向链接（问题→Schema + Schema→问题） | RSL-SQL | 依赖 LLM |
| 2025 | 多数据库 + 图算法 | LinkAlign, SchemaGraphSQL | 无 Ontology 层 |
| 未来 | Schema + Ontology 联合召回 | 本研究 | - |

## 核心发现

1. **Recall 比 Precision 更重要**：遗漏比噪声更致命
2. **双向验证有效**：正向 + 反向提升召回
3. **图结构优于纯文本**：联合编码效果更好
4. **企业场景三大挑战**：表检索失败 51.3%、列映射错误 59.1%、隐式假设遗漏 50%

## 相关概念

- [[SQL 等价性判断]]
- [[topics/ontology-schema.md|本体 Schema 研究综述]]
- [[topics/da-casebase.md|DA CaseBase 研究综述]]
