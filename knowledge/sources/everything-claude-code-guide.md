---
title: Everything Claude Code (ECC) 完整指南
date: 2026-03-31
tags: [Claude Code, ECC, 开发工具, AI编程, 工程化框架]
source: https://b23.tv/vNva3Hi
updated: 2026-04-10
---

# Everything Claude Code (ECC) 完整指南

> 110K+ Stars 项目，黑客松霍圣者历时10个月打磨
> GitHub: 110K+ star

## ECC 是什么

装在 `~/.claude` 目录下的Claude Code插件集合 —— Claude Code 的**工程化框架**。

比喻：给"聪明的实习生"配一套完整的公司制度、操作手册、审查流程和监控系统。

## 六大核心模块

| 模块 | 数量 | 作用 |
|------|------|------|
| **Commands** | 60+ | 斜杠命令，覆盖开发全流程 |
| **Agent Strategy** | 28个 | 专用AI子代理（规划、安全审查、修构建错误等） |
| **Hooks** | 8种 | 事件驱动自动化（格式化、类型检查等） |
| **Skills** | 125个 | 领域知识库（TypeScript/React/Python/Go等） |
| **Rules** | 全局 | 编码规范，所有代理必须执行 |
| **MCP** | - | 外部服务集成（GitHub、Supabase等） |

**协作逻辑**: Rules 划红线 → Commands 触发流程 → Agents 干活 → Skills 提供知识 → Hooks 全程把关

## 核心 Commands

| 命令 | 作用 |
|------|------|
| `/plan` | 最被忽略但最有价值 —— 写代码前让AI帮你把坑找出来 |
| `/tdd` | 测试驱动开发，先写测试 → 再写实现 |
| `/code-review` | 机场安检式审查，挑出安全/质量问题 |
| `/verify` | 中级验收标准，必须通过构建+测试+安全扫描 |

## 关键 Hooks

| Hook | 机制 |
|------|------|
| `PostToolUse` | 编辑TS文件 → 自动Prettier+TSC类型检查；编辑PY文件 → 自动Black+MyPY |
| `PreToolUse` | 写文件前检查，防止AI偷偷修改lint配置规避检查 |

## V2 持续学习系统

- `/learn` - 从会话中提取可复用模式
- `/instinct-status` - 查看学习状态
- `/evolve` - 合并升级为正式skill/command
- `/skill-create` - 分析Git提交历史，自动提取编码模式

## 解决的痛点

| 痛点 | ECC 解决方案 |
|------|-------------|
| 代码没测试 | `/tdd` 强制测试驱动 |
| 忘做安全审查 | Security Reviewer Agent + `/code-review` |
| AI不了解项目规范 | Skills自动加载 + Rules强制执行 |
| AI偷偷改配置规避检查 | `PreToolUse` Hook阻止 |

## 关联

- [[entities/claude-code.md|Claude Code]]
- [[concepts/ai-coding.md|AI编程]]
- [[topics/openclaw-practices.md|OpenClaw实战经验]]
- [[concepts/mcp.md|MCP协议]]
