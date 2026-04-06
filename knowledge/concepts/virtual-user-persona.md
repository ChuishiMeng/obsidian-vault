---
title: 虚拟用户 Persona
type: concept
date: 2026-04-05
tags: [概念, LLM, Persona, 问卷模拟]
---

# 虚拟用户 Persona（Synthetic Persona / Virtual User）

## 定义

通过 LLM 构建的具有特定人口统计特征、行为模式、态度偏好的虚拟人格，用于模拟真实用户在问卷、市场调研等场景中的响应。

## Persona 构建方法

| 方法 | 数据来源 | 优势 | 局限 | 代表论文 |
|------|---------|------|------|---------|
| 行为推断 | 电商数据 | 可解释 | 无态度建模 | You Are What You Bought (2025) |
| RAG 增强 | 评论/社媒 | 真实性高 | 依赖数据 | PersonaCite (2026) |
| 人口对齐 | 统计数据 | 分布准确 | 文化限制 | German General Personas (2025) |
| 性格驱动 | Big Five | 心理学基础 | 无约束 | PUB (2025) |
| 自动生成 | LLM 直接生成 | 可扩展 | 质量不稳定 | Virtual Personas (2024) |

## 核心挑战

1. **态度一致性**：同一 Persona 对相关问题回答不一致（矛盾率 >40%）
2. **分布偏差**：系统性偏离真实人群（回归均值、倾向友好回答）
3. **跨文化差异**：LLM 倾向于英语文化背景
4. **评估困难**：无完美 baseline（真实用户本身也有随机性）

## 评估维度

| 维度 | 指标 | 方法 |
|------|------|------|
| 语言质量 | BLEU/ROUGE/BERTScore | 自动 |
| 分布匹配 | KL 散度 | 自动 |
| 问卷结构 | Survey Quality | 自动 |
| 真实性 | 人类评估 | HumanStudy-Bench (2026) |

## 相关概念

- [[topics/virtual-user-research.md|虚拟用户研究综述]]
- [[entities/virtual-user.md|虚拟用户]]
