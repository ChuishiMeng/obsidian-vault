---
title: DA CaseBase 研究综述
type: topic
date: 2026-04-05
tags: [研究, Data Agent, NL2SQL, 语义缓存, 案例推理]
---

# DA CaseBase 研究综述

## 概述

DA-CaseBase 项目研究如何通过案例库机制（语义缓存 + 模板匹配 + LLM 生成三层架构）提升 [[Data Agent]] 问数准确度与效率，解决企业数据分析场景中 LLM 生成式方法准确率不稳定、成本高、知识无法沉淀三大瓶颈。

## 关键论文

| # | 论文 | 会议/期刊 | 年份 | 核心贡献 |
|---|------|----------|------|---------|
| P02 | CBR-to-SQL | arXiv | 2026 | 案例推理 + 两阶段分离（模板+实体），Acc_LF 82.8% |
| P06 | RubikSQL | arXiv | 2025 | 终身学习 + Agentic Knowledge Base 四阶段工作流 |
| P15 | Semantic Cache for OLAP | arXiv | 2026 | LLM 查询规范化，命中率从 28%→76% |
| P33 | SQL-Equivalence | arXiv | 2025 | SQL 等价性评估 Miniature+Mull 策略，准确率 92.3% |
| P34 | HES-SQL | arXiv | 2025 | 混合推理框架 + 骨架完整性评分 |
| P01 | LangCache | arXiv | 2025 | 领域专用 Embedding 语义缓存 |
| P04 | GPTCache | NLP-OSS | 2023 | 开源语义缓存框架，成本降低 90% |
| P03 | TailorSQL | arXiv | 2025 | 历史查询工作负载利用 + Document Store |
| P05 | SQLForge | arXiv | 2025 | 数据合成 + Fine-tuning，Spider Dev 85.7% |
| P10 | Agentic NL2SQL | arXiv | 2025 | Schema 裁剪降低 Token 87% |
| P19 | RSL-SQL | arXiv | 2024 | 双向 Schema Linking |
| P20 | X-SQL | arXiv | 2025 | 多 LLM 专家协作 Schema Linking |
| P21 | Enterprise Analytics KG | arXiv | 2025 | 知识图谱增强企业查询（微软实践） |
| P08 | Spider2 | arXiv | 2025 | 企业级工作流基准测试 |
| P17 | BEAVER | arXiv | 2024 | 真实企业场景基准，揭示表检索/列映射/隐式假设三大挑战 |
| P18 | OpenSearch-SQL | arXiv | 2025 | 动态 Few-shot + 一致性对齐 |

## 核心方法论

### 三层架构（本研究核心创新）
1. **Layer 1 语义缓存**：Embedding 相似度 + LLM 查询规范化，命中时 0 Token
2. **Layer 2 案例模板匹配**：Masked SQL 模板 + 实体落地，少量 Token
3. **Layer 3 LLM 生成**：Schema 裁剪 + 自适应推理，回退方案
4. **自动沉淀机制**：验证通过的查询自动入库，实时更新

### 关键技术
- [[语义缓存]]：GPTCache/LangCache 框架
- [[案例推理]]（CBR）：CBR-to-SQL 两阶段分离方法
- [[SQL 等价性判断]]：Miniature + Mull 策略
- [[Schema Linking]]：双向验证、多 LLM 协作

## 与其他主题的关系

- [[topics/da-disambiguation.md|DA 消歧研究]]：消歧解决意图模糊问题，CaseBase 解决已知意图的复用问题
- [[topics/ontology-schema.md|本体 Schema 研究]]：Schema Linking 是 CaseBase 的前置步骤
- [[topics/data-agent-research.md|Data Agent 研究]]：CaseBase 是 Data Agent 的核心子系统

## 研究状态

- **阶段**：P2 文献调研已完成（16 篇论文），进入 P3 研究空白分析
- **目标会议**：KDD / ICDE
- **核心差异化**：自动沉淀 + 实时更新 vs 现有方法的批量人工更新
