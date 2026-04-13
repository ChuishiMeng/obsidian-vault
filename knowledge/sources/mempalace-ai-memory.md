---
title: "MemPalace：AI 记忆系统"
date: 2026-04-08
tags: [AI记忆, 记忆宫殿, MCP, 长期记忆, 开源项目]
source: "新智元"
---

# MemPalace：生化危机女主用 Claude 手搓满分 AI 记忆系统

> 来源：新智元 | 日期：2026-04-08
> GitHub：https://github.com/milla-jovovich/mempalace（17.9K Stars）

## 故事

《生化危机》《第五元素》女主 **Milla Jovovich**，用 Claude Code 写出了 MemPalace。在 LongMemEval 基准上 **500 题全对，全球首个满分**。

## 架构

借鉴古希腊「记忆宫殿」，把 AI 记忆结构化为虚拟空间：

| 层级 | 概念 | 作用 |
|------|------|------|
| **翼楼 (Wing)** | 项目/人 | 顶层分类 |
| **房间 (Room)** | 主题 | 按主题归类 |
| **走廊 (Hall)** | 记忆类型 | 决策/里程碑/偏好/建议/发现 |
| **隧道 (Tunnel)** | 跨翼楼关联 | 同名房间自动打通 |

## 核心数据

- 全库搜索召回率 60.9% → 加结构过滤后 **94.8%**
- 本地 ChromaDB 存储，一年 **0.7 美元**
- 支持 MCP，一行命令接入

## AAAK：AI 专用压缩方言

把 1000 token 压缩到 ~120 token（但有损，实际效果有争议）

## ⚠️ 社区 48 小时扒皮

| 宣传 | 实际 |
|------|------|
| 「30 倍无损压缩」 | 实际反而多了 token |
| AAAK 无损 | 有损（AAAK 84.2% vs raw 96.6%） |
| 「+34% 宫殿增益」 | 标准 ChromaDB 元数据过滤 |

作者体面回应：没删评论没辩解，直接贴公开信逐条认错。社区因此更认可。

## 意义

结构化记忆思路（翼楼→房间→走廊→隧道）值得借鉴。跟 [[concepts/agent-memory.md|Agent 记忆系统]] 和 OpenClaw MEMORY.md 方案是不同路线的解法。

## 关联

- [[concepts/agent-memory.md|Agent 记忆系统]]
- [[topics/ai-agent-trends.md|AI Agent 趋势]]
- [[sources/elseland-ai-open-world.md|Elseland AI开放世界]]
