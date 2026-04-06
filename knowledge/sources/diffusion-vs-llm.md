---
title: Diffusion vs LLM - 模型体量差异解析
date: 2026-03-30
tags: [Diffusion, LLM, 模型架构, AI]
source: 技术讨论总结
---

# Diffusion vs LLM - 模型体量差异解析

## 核心结论

**Diffusion和LLM做完全不同的任务，信息密度和复杂度天差地别，所以模型体量差一到两个数量级。**

## 对比分析

| 维度 | LLM | Diffusion |
|------|-----|-----------|
| 任务 | 理解、推理、生成语言 | 噪声→图像 |
| 本质 | 通用智能基座 | 信号转换模型 |
| 需要 | 世界知识+逻辑+因果 | 图像结构+纹理+分布 |
| 比喻 | 大脑 | 画家 |

## 关联

- [[concepts/llm.md|大语言模型]]
- [[concepts/ai-agent.md|AI Agent]]
