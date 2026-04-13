---
title: "Everything Claude Code 深度解读"
date: 2026-04-08
tags: [Claude Code, ECC, AI编程, Agent]
source: "AI编程实验室 · 鲁工（九年AI算法老兵）"
---

# Everything Claude Code 深度解读

> 来源：AI编程实验室 · 鲁工
> 日期：2026-04-08
- GitHub：https://github.com/affaan-m/everything-claude-code
- 评分：**9/10**

## 规模

- **36 个专用 subagent**，150+ skills，68 个命令，覆盖 10 种编程语言 rules
- 作者 Affaan Mushtaq，Anthropic 黑客松获奖者，10 个月实战打磨
- 跨平台：Claude Code、Codex、Cursor、OpenCode、Gemini 都能用
- 官网：https://ecc.tools/

## 三个亮点

### 1. pass@k / pass^k 验证指标
- pass@k：跑 k 次至少一次通过的概率
- pass^k：k 次全部通过的概率
- 数据：k=3 时 pass@k = 91%，但 pass^k 只有 34%
- 意义：判断 agent 输出是稳定可靠还是碰运气

### 2. AgentShield 安全集成
- 内置 102 条安全规则 + 1282 个安全测试
- `/security-scan` 命令，覆盖 OWASP 常见漏洞

### 3. 沙盒化 subagent
- 每个 subagent 有独立的工具权限限制
- 代码审查 agent 只能读不能写，防止多 agent 并行时误操作

## vs Superpowers 对比

| 维度 | ECC | Superpowers |
|------|-----|-------------|
| 定位 | 装备齐全的工具箱 | 资深架构师 |
| 核心 | 场景化 agent + skills | 开发方法论 + 流程约束 |
| 建议 | 两个都装，Superpowers 管流程，ECC 管工具 |

## 关联

- [[entities/claude-code.md|Claude Code]]
- [[sources/everything-claude-code-guide.md|ECC 完整指南]]
- [[concepts/ai-coding.md|AI 编程]]
- [[concepts/vibe-coding.md|Vibe Coding]]
