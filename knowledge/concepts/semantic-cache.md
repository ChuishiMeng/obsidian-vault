---
title: 语义缓存
type: concept
date: 2026-04-05
tags: [概念, Data Agent, NL2SQL, LLM]
---

# 语义缓存（Semantic Cache）

## 定义

通过 Embedding 向量相似度匹配，将语义相同的 LLM 查询请求命中历史缓存，避免重复调用 LLM，降低成本和延迟。

## 核心方法

| 方法 | 代表论文 | 命中率 | 成本降低 |
|------|---------|--------|---------|
| Embedding 相似度 | GPTCache (2023) | 60-80% | 90% |
| 领域专用 Embedding | LangCache (2025) | Precision 0.92 | - |
| LLM 查询规范化 | Semantic Cache OLAP (2026) | 28%→76% | - |

## 关键挑战

- **冷启动**：新系统无历史数据
- **语义边界模糊**："销售额前10" vs "销售额后10" 语义不同但相似度高
- **等价性判断**：需要判断两个 SQL 是否等价（→[[SQL 等价性判断]]）

## 应用场景

[[Data Agent]] 的三层架构 Layer 1：命中时 0 Token、<1s 响应

## 相关概念

- [[案例推理]]
- [[SQL 等价性判断]]
- [[topics/da-casebase.md|DA CaseBase 研究综述]]
