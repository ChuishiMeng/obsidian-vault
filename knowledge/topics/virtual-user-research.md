---
title: 虚拟用户研究综述
type: topic
date: 2026-04-05
tags: [研究, 虚拟用户, LLM, Persona, 问卷模拟, 市场调研]
---

# 虚拟用户研究综述

## 概述

VirtualUser 项目研究如何基于电商行为数据构建虚拟用户（Persona），用于市场调研问卷模拟，目标是替代传统问卷调研（时间从 4 周→1 天，成本降 90%），实现快速、低成本、可迭代的市场决策支持。

## 关键论文

| # | 论文 | 会议/期刊 | 年份 | 核心贡献 |
|---|------|----------|------|---------|
| P05 | Polypersona | arXiv | 2025 | Persona + 问卷模拟框架，一致性约束 |
| P13 | LLM-S³ | arXiv | 2025 | PAS/FAS 范式 + 系统评估基准 |
| P11 | Lost in Simulation | arXiv | 2026 | 可靠性警告：态度矛盾率 >40% |
| P04 | You Are What You Bought | arXiv | 2025 | 电商 Persona 构建（行为推断） |
| P06 | PersonaCite | arXiv | 2026 | RAG 增强 Persona 真实性 |
| P07 | German General Personas | arXiv | 2025 | 人口统计对齐生成 |
| P08 | PUB | arXiv | 2025 | 性格驱动（Big Five）模拟 |
| P09 | Generative Agents | arXiv | 2023 | 记忆流架构（Stanford 小镇） |
| P12 | LLM Simulations Not Reliable | arXiv | 2025 | 系统性问题：回归均值、分布偏差 |
| P17 | AlignSurvey | arXiv | 2025 | 虚拟用户问卷评估基准 |
| P18 | HumanStudy-Bench | arXiv | 2026 | 人类评估方法论 |
| P23 | AI-Human Hybrids | arXiv | 2025 | AI+人类混合市场研究 |
| P30 | Mixture of Personas | arXiv | 2024 | 多 Persona 权重组合 |
| P32 | PersonaTwin | arXiv | 2025 | 多层 Prompt 构建 Persona |
| P35 | Virtual Personas | arXiv | 2024 | 自动 Persona 生成方法 |

## 核心方法论

### Persona 构建方法
| 方法 | 优势 | 局限 | 代表论文 |
|------|------|------|---------|
| 行为推断 | 可解释、成本低 | 无态度建模 | P04 |
| RAG 增强 | 真实性高 | 依赖评论数据 | P06 PersonaCite |
| 人口对齐 | 分布准确 | 文化限制 | P07 |
| 性格驱动 | 心理学基础 | 无态度约束 | P08 PUB |

### 核心挑战与发现
1. **态度矛盾**：同一虚拟用户对相关问题回答不一致，矛盾率 >40%（P11）
2. **回归均值**：倾向于友好/社会期望回答，缺乏极端意见
3. **分布偏差**：系统性偏离真实人群分布
4. **评估困难**：无完美 baseline，需要多维度评估

### 本研究技术方案
- 态度因子建模 + 一致性约束（解决态度矛盾）
- 电商行为数据 → 态度推断 → Persona 生成
- 京东真实业务场景验证（新品定价、流失分析、文案测试）

## 与其他主题的关系

- [[topics/urban-computing.md|城市计算研究]]：虚拟用户可作为城市感知增强、行为模拟、决策辅助
- [[topics/da-disambiguation.md|DA 消歧研究]]：用户画像学习互相借鉴
- [[topics/data-agent-research.md|Data Agent 研究]]：虚拟用户是 Agent 的一种形态

## 研究状态

- **阶段**：P2 文献调研已完成（60 篇检索，15 篇深度解读）
- **目标会议**：KDD
- **京东独有优势**：海量行为数据 + 丰富用户画像 + 真实调研需求 + 验证环境
