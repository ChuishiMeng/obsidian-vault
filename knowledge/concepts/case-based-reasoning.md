---
title: 案例推理
type: concept
date: 2026-04-05
tags: [概念, NL2SQL, 机器学习, Data Agent]
---

# 案例推理（Case-Based Reasoning, CBR）

## 定义

一种人工智能范式，通过存储、检索和复用历史案例（问题-解决方案对）来解决新问题。在 Text-to-SQL 场景中，将历史 NL-SQL 对作为案例库，新查询通过匹配案例模板生成 SQL。

## CBR-to-SQL 核心方法（2026）

**两阶段分离**：
1. **离线模板构建**：将具体 SQL 中的实体值掩码（Mask），抽象为可泛化模板
2. **在线实体落地**：新问题匹配模板后，将实体值映射回 SQL

```
具体 SQL: SELECT COUNT(*) FROM diagnoses WHERE icd9='493' AND drug='Budesonide'
→ 模板:   SELECT COUNT(*) FROM {table} WHERE {col1}={val1} AND {col2}={val2}

新问题 "糖尿病用过二甲双胍" → 模板匹配 + 实体映射 → 最终 SQL
```

## 核心优势

- **知识沉淀**：历史查询可复用（vs 纯生成式无积累）
- **可解释性**：模板结构清晰可审计
- **效率**：模板命中时仅需少量 Token

## 关键挑战

- 模板泛化能力（如何覆盖更多变体）
- 冷启动问题（无历史数据时如何工作）
- 案例质量保证

## 相关概念

- [[语义缓存]]
- [[SQL 等价性判断]]
- [[topics/da-casebase.md|DA CaseBase 研究综述]]
