---
title: MCP
aliases: [Model Context Protocol]
tags: [概念, AI, 接口标准, 工具集成]
---

# MCP (Model Context Protocol)

## 概念定义

**MCP**是AI Agent与外部工具/服务通信的协议标准，旨在统一AI工具集成的接口。

## 核心要点

### MCP vs CLI 之争

2026年3月，钉钉和飞书同周选择CLI而非MCP：
- 2026-03-17：钉钉「悟空」CLI开源（Go, Apache-2.0）
- 2026-03-28：飞书CLI开源（Go, MIT）

### ScaleKit Benchmark 数据

| 任务 | CLI Token | MCP Token | 差距 |
|------|----------|----------|------|
| 查仓库语言 | 1,365 | 44,026 | 32倍 |
| 查PR详情 | 1,648 | 32,279 | 20倍 |
| 月成本(1万次) | $3.2 | $55.2 | 17倍 |

### 核心观点

- **CLI** — 适合高频轻量交互，token效率高
- **MCP** — 适合复杂工具发现，但成本高
- 两者可能长期共存

## 关联来源

- [[sources/mcp-vs-cli.md|MCP vs CLI - AI Agent接口之争]]

## 关联概念

- [[concepts/ai-agent.md|AI Agent]]
