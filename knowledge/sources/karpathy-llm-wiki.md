---
title: Karpathy LLM Wiki
date: 2026-04-05
source: https://mp.weixin.qq.com/s/zOAsp5uZh_JTUb4VDliC0A
original: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
author: Andrej Karpathy
type: article
tags: [知识管理, LLM-Wiki, Agent, Karpathy]
---

# Karpathy LLM Wiki

## 核心思想

RAG 的问题：每次查询从零开始，没有积累。

LLM Wiki 方案：让 LLM **增量构建和维护持久化 Wiki**。

> Obsidian 是 IDE，大模型是程序员，Wiki 是代码库。

## 三层架构

1. **Raw Sources** - 原始素材（不可变，LLM 只读）
2. **Wiki** - LLM 生成的结构化知识（LLM 全权管理）
3. **Schema** - 规则文件（AGENTS.md / CLAUDE.md）

## 四大操作

- **Ingest**：新素材 → AI 编译 → 更新 10-15 页
- **Query**：读 index → 找页面 → 综合回答
- **File Back**：查询结果存回 Wiki，知识持续增长
- **Lint**：自检矛盾、孤立页、补全引用

## 关键数据

- Karpathy 自己：~100 篇素材，~40 万字
- index.md + 简短摘要够用，不需要复杂 RAG
- 推文浏览量：1347 万
- Gist Star：2100+

## 相关实体

- [[Karpathy, Andrej]] - 斯坦福/Tesla/OpenAI，AI 教育者
- [[Obsidian]] - Markdown 知识管理工具
- [[Farzapedia]] - Farza 的个人 Wiki 实现（400 篇文章）

## 相关概念

- RAG vs LLM Wiki
- Memex (Vannevar Bush, 1945)
- BYOAI (Bring Your Own AI)
- File over App
