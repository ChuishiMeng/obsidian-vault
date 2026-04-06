---
title: MCP vs CLI - AI Agent 接口之争
date: 2026-03-30
tags: [MCP, CLI, AI Agent, 接口标准]
source: 微信公众号文章解读
---

# MCP vs CLI - AI Agent 接口之争

## 核心事件

钉钉和飞书同周选择CLI而非MCP：
- 2026-03-17：钉钉「悟空」CLI开源（Go, Apache-2.0）
- 2026-03-28：飞书CLI开源（Go, MIT）

## ScaleKit Benchmark 数据

### Token成本对比

| 任务 | CLI | MCP | 差距 |
|------|-----|-----|------|
| 查仓库语言 | 1,365 | 44,026 | 32倍 |
| 查PR详情 | 1,648 | 32,279 | 20倍 |
| 月成本(1万次) | $3.2 | $55.2 | 17倍 |

### 核心观点

- CLI在token效率和可靠性上显著优于MCP
- MCP适合复杂工具发现，CLI适合高频轻量交互
- 两者可能长期共存

## 关联

- [[concepts/mcp.md|MCP]]
- [[concepts/ai-agent.md|AI Agent]]
