---
title: DA-CaseBase 项目
type: entity
date: 2026-04-05
tags: [项目, Data Agent, NL2SQL, 语义缓存]
---

# DA-CaseBase 项目

## 基本信息

| 属性 | 值 |
|------|------|
| **全称** | Data Agent Case Base |
| **类型** | 科研项目 |
| **领域** | 企业数据分析 / [[Data Agent]] |
| **负责人** | 孟老师 |
| **状态** | P2 文献调研完成 |
| **目标会议** | KDD / ICDE |

## 研究目标

通过语义缓存 + 模板匹配 + LLM 生成三层架构，提升 [[Data Agent]] 问数准确度与效率，实现案例自动沉淀和知识复用。

## 核心创新

1. **三层架构**：缓存→模板→生成渐进降级
2. **自动沉淀**：验证通过的查询实时入库（vs 现有方法的批量人工更新）
3. **Token 优化**：命中时 0 Token（vs 传统每次 500-2000）
4. **SQL 等价性判断**：Miniature + Mull 策略，准确率 92.3%

## 文献资源

- 深度解读论文：16 篇（⭐⭐⭐及以上）
- 核心论文：CBR-to-SQL (⭐⭐⭐⭐⭐)、RubikSQL、Semantic Cache for OLAP
- 评估基准：Spider2、BEAVER

## 相关项目

- [[entities/da-disambiguation.md|DA-Disambiguation]]：互补研究（消歧 vs 复用）
- [[entities/ontology-schema.md|Ontology-Schema]]：前置组件（Schema Linking）

## 相关主题

- [[topics/da-casebase.md|DA CaseBase 研究综述]]
- [[topics/data-agent-research.md|Data Agent 研究]]
