---
title: Data Agent 挺难做的
date: 2026-03-31
tags: [Data Agent, NL2SQL, 产品设计, 经验教训]
source: https://mp.weixin.qq.com/s/T6lmy0rRCBWvtFkltptGBA
---

# Data Agent 挺难做的

> 来源：奇诺微信公众号，作者花3个月从零搭建Data Agent的实战经历

## 核心结论

作者迭代三版Data Agent后放弃，最终转向Skills方案——**把业务隐性知识固化进Skill，只服务自己，反而解决了最难的问题**。

## 三版迭代

### 第一版：语义层约束（12天）
- 用YAML预定义指标、维度、别名
- 问题：约束太死，只能做L2层数据获取

### 第二版：Meta-Agent（4天）
- 多Agent分工协作
- 问题：复杂度爆炸，维护困难

### 第三版：Case-Based（2天）
- 用历史查询做Few-shot
- 问题：查询多样性不足

## 最终方案：Skills
把业务隐性知识固化进Skill，不追求通用Data Agent，只服务自己。

## 关联

- [[concepts/data-agent.md|Data Agent]]
- [[concepts/nl2sql.md|NL2SQL]]
- [[topics/data-agent-research.md|Data Agent 研究]]
