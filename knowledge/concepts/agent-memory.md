---
title: Agent 记忆系统
aliases: [Agent Memory, 智能体记忆]
tags: [概念, AI, 记忆, 架构]
---

# Agent 记忆系统 (Agent Memory)

## 概念定义

**Agent记忆系统**是AI智能体存储、检索和管理信息的架构，包括短期记忆（当前对话上下文）和长期记忆（持久化知识库）。

## 核心要点

### 记忆类型

1. **短期记忆** — 当前会话的上下文，通常受token限制
2. **长期记忆** — 持久化存储的知识，可跨会话检索
3. **工作记忆** — 当前任务相关的临时信息

### 设计理念（OpenClaw）

- **本地优先** — 数据存储在本地，透明可控
- **完全透明** — 用户可查看和编辑所有记忆
- **向量检索** — 语义搜索相关记忆
- **分层管理** — 按重要性和时效性分层

## 为什么记忆最重要？

> **记忆才是影响Agent性能最核心的因素**

没有记忆的Agent每次对话都是新的，无法积累经验和上下文。

## 关联来源

- [[sources/openclaw-lecture-series.md|OpenClaw精讲系列]]
- [[sources/agent-as-os.md|我把Agent当操作系统用了一天]]

## 关联实体

- [[entities/openclaw.md|OpenClaw]]
