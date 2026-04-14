---
title: "Everything Claude Code (ECC) 完整指南"
date: 2026-04-11
tags: [Claude Code, ECC, AI编程, 插件, Agent]
source: "B站视频 + GitHub"
updated: "2026-04-12 完整指南更新"
---

# Everything Claude Code (ECC) 完整指南

> 项目: https://github.com/EverythingClaudeCode (110K+ Stars)
> 作者: 黑客松霍圣者，历时10个月打磨
> 官网：https://ecc.tools/

## 什么是 ECC？

装在 `~/.claude` 目录下的 Claude Code 插件集合：
- 原版 Claude Code = 聪明但无约束的实习生
- ECC = 给实习生配了公司制度、操作手册、审查流程、监控系统

## 六大核心模块

| 模块 | 说明 | 数量 |
|------|------|------|
| **Commands** | `/` 触发的命令 | 60+ |
| **Agent Strategy** | 专用 AI 子代理 | 28→36个 |
| **Hooks** | 事件驱动的自动化脚本 | 8种事件类型 |
| **Skills** | 领域知识库 | ~125→150个 |
| **Rules** | 全局强制执行的编码规范 | 10种语言 |
| **MCP** | 外部服务集成 | - |

## 核心 Commands

| 命令 | 价值 |
|------|------|
| `/plan` ⭐ | 动代码前让 AI 把坑找出来 |
| `/tdd` | 测试驱动开发，先写测试再写实现 |
| `/code-review` | 像机场安检，安全+质量全面审查 |
| `/verify` | 对标中级验收标准 |
| `/security-scan` | 102条安全规则 + 1282个安全测试 |

## V2 持续学习系统（Instinct）

1. `/learn` → 分析当前会话，提取可复用模式
2. `/instinct-status` → 查看学到的内容（置信度分数）
3. `/evolve` → 合并升级为正式 skill 或 command

## 深度解读亮点（2026-04-08 补充）

- **pass@k / pass^k 验证指标**：k=3 时 pass@k=91%，pass^k=34%
- **AgentShield 安全集成**：102条规则 + 1282个安全测试
- **沙盒化 subagent**：独立工具权限，代码审查 agent 只能读不能写
- **评分**：鲁工 9/10

## 安装

```bash
git clone https://github.com/EverythingClaudeCode/ECC
cp ECC/skills ~/.claude/skills && cp ECC/commands ~/.claude/commands
cp ECC/hooks ~/.claude/hooks && cp ECC/rules ~/.claude/rules && cp ECC/agents ~/.claude/agents
```

## 28 个专用代理（2026-04-12 补充）

| 代理 | 职责 |
|------|------|
| Architect | 架构设计 |
| Security Reviewer | 安全审查 |
| Builder Resolver | 修构建错误 |
| Test Runner | 运行测试 |
| Code Reviewer | 代码审查 |

调用方式：直接指定或通过 `/code-review` 等命令触发。

## Hooks 自动化

8种事件类型的事件驱动自动化脚本，在后台默默工作把关。

## Skills 自动加载

~125-150个领域知识库，自动加载项目规范和上下文。

## 核心痛点解决

| 痛点 | ECC 解决方案 |
|------|-------------|
| 生成的代码没有测试 | `/tdd` 强制测试先行 |
| 经常忘了做安全审查 | Security Reviewer 代理 + Hooks |
| AI 不了解项目规范 | Skills 自动加载 + Rules 强制执行 |
| 方向走错浪费时间 | `/plan` 提前规划 |

## 与 OpenClaw 的对比

- Skills ≈ 领域知识库（两者相似）
- Hooks ≈ OpenClaw 的 cron/heartbeat
- Agents ≈ OpenClaw 的 subagent
- ECC 侧重 Claude Code 工程化约束，OpenClaw 侧重多平台消息桥接和自动化运维

## 关联

- [[entities/claude-code.md|Claude Code]]
- [[sources/ecc-deep-dive.md|ECC 深度解读]]
- [[concepts/ai-coding.md|AI 编程]]
- [[concepts/vibe-coding.md|Vibe Coding]]
