---
title: P23 - ATHENA：Ontology-driven NL→SQL 先驱系统
date: 2026-04-14
tags: [科研, Ontology, NL2SQL, OBDA, Schema-Linking]
paper_id: P23
conference: VLDB 2016
source: ~/agentKB/Obsidian/AI-Workspace/Research/ontology-schema/2026-04-14-P23-ATHENA解读.md
---

# P23 - ATHENA：Ontology-Driven NL Querying

**核心贡献**：NL→OQL→SQL 两阶段架构，实现物理独立性。Ontology 驱动的语义消歧。

---

## 技术架构

```
NL Query → Translation Index → NLQ Engine → OQL → SQL
```

- Ontology 结构：OWL2，概念(C) + 关系(R) + 属性(P)
- OQL：类 SQL 但操作 Ontology 概念
- Mapping：Ontology-to-Database，手工定义

---

## 实验结果

| 数据集 | Precision | Recall |
|--------|-----------|--------|
| GEO | 100% | 87.2% |
| FIN（75概念） | 99% | 88.9% |

---

## 与现代 LLM 方法的对比

| 维度 | ATHENA (2016) | LLM 方法 (2026) |
|------|---------------|----------------|
| NL 理解 | 规则+同义词 | LLM 语义理解 |
| Ontology 构建 | 完全手工 | LLM 半自动 |
| Schema 处理 | 全量无筛选 | 最小完备集提取 |
| 消歧 | 候选排序 | 元路径约束推理 |

---

## 可借鉴内容

1. 两阶段架构思路（NL→中间表示→SQL）
2. Ontology 作为语义层的建模方式
3. Translation Index 数据值索引思路

---

## 关联

- [[topics/ontology-schema.md|本体 Schema 研究]]
- [[sources/karpathy-llm-wiki.md|Karpathy LLM Wiki]]
- [[concepts/schema-linking.md|Schema Linking]]

---

*编译时间：2026-04-15 03:00*