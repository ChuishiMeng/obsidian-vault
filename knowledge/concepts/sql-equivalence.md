---
title: SQL 等价性判断
type: concept
date: 2026-04-05
tags: [概念, SQL, Data Agent, 评估]
---

# SQL 等价性判断（SQL Equivalence Checking）

## 定义

判断两个 SQL 查询是否在任何合法数据库实例上返回相同结果。这是 [[语义缓存]] 和 [[案例推理]] 系统中匹配历史案例的核心技术。

## 传统指标的局限

| 指标 | 问题 |
|------|------|
| Execution Accuracy | 假阳性：空结果集偶然匹配 |
| Exact Set Matching | 假阴性：格式差异导致不匹配 |

## Miniature + Mull 策略（2025）

1. **Miniature**：构造边界值测试数据（小样本），执行两个 SQL 对比结果
2. **Mull**：多角度质疑等价性（"如果 NULL 呢？""如果空表呢？"）
3. **多次运行投票**：综合判断

**效果**：准确率 92.3%

## 应用

- 语义缓存中的命中判定
- 案例库中的模板匹配验证
- SQL 质量评估

## 相关概念

- [[语义缓存]]
- [[案例推理]]
- [[Schema Linking]]
