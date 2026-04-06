---
title: 意图消歧
type: concept
date: 2026-04-05
tags: [概念, NLP, Data Agent, 消歧]
---

# 意图消歧（Intent Disambiguation）

## 定义

在 [[Data Agent]] 问答场景中，识别和消除用户自然语言查询中的歧义，确保系统理解用户真实意图。

## 三层歧义框架

| 层面 | 来源 | 示例 |
|------|------|------|
| **用户层** | 表达习惯、角色视角 | "查销售额"（总监→全局 vs 代表→个人） |
| **业务层** | 概念定义、计算逻辑 | "优质用户"（存款/理财/信贷各有不同定义） |
| **数据层** | Schema 映射 | "销售额"→order.amount? order_detail.price*qty? |

## 消歧方法

| 方法 | 论文 | 机制 |
|------|------|------|
| 两阶段消歧 | Disambiguate First (2025) | 问题→解释→SQL，非交互 |
| 交互式消歧 | AmbiSQL (2025) | 检测歧义→多选题→用户确认 |
| 最优问题选择 | EIG (2025) | 期望信息增益最大化，最小交互 |
| 歧义分类 | AMBROSIA (2024) | Scope/Attachment/Vagueness 语言学分类 |

## 核心洞察

歧义叠加效应：单一歧义 O(n) 种解释，k 个歧义 O(n^k) 种组合。"让模型猜测"不可靠。

## 相关概念

- [[Schema Linking]]
- [[topics/da-disambiguation.md|DA 消歧研究综述]]
