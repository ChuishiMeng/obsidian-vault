# Everything Claude Code (ECC) 完整指南

> 来源: https://b23.tv/vNva3Hi (B站视频)
> 项目: https://github.com/EverythingClaudeCode (110K+ Stars)
> 作者: 黑客松霍圣者，历时10个月打磨
> 提取时间: 2026-03-31

---

## 什么是 ECC？

**简单定义**：装在 `~/.claude` 目录下的 Claude Code 插件集合

**比喻**：
- 原版 Claude Code = 聪明但无约束的实习生
- ECC = 给实习生配了公司制度、操作手册、审查流程、监控系统

---

## 六大核心模块

| 模块                 | 说明                          | 数量     |
| ------------------ | --------------------------- | ------ |
| **Commands**       | `/` 触发的命令，覆盖开发全流程           | 60+    |
| **Agent Strategy** | 专用 AI 子代理，各司其职              | 28个    |
| **Hooks**          | 事件驱动的自动化脚本（默默工作）            | 8种事件类型 |
| **Skills**         | 领域知识库，自动加载规范                | ~125个  |
| **Rules**          | 全局强制执行的编码规范（不可绕过）           | -      |
| **MCP**            | 外部服务集成 (GitHub, Supabase 等) | -      |

**协作逻辑**：
```
Rules 划红线 → Commands 触发流程 → Agents 干活 → Skills 提供知识 → Hooks 全程把关
```

---

## 核心 Commands 详解

### 1. `/plan` ⭐ 最有价值

**核心价值**：在动代码之前，让 AI 把坑找出来

**对比测试**（开发博客网站）：
- 无 `/plan`：直接写代码，中途发现方向不对，删掉重来
- 有 `/plan`：细化更多步骤，提前发现问题

### 2. `/tdd` - 测试驱动开发

**流程**：
```
先写测试文件 → 再写实现代码 → 运行测试 → 完成开发
```

颠覆传统开发方法论，强制 Claude Code 按 TDD 模式编程

### 3. `/code-review` - 代码审查

像机场安检，把所有改动过一遍：
- 安全问题
- 质量问题
- 全部挑出来

### 4. `/verify` - 验收验证

对标中级验收标准，必须通过：
- 构建
- 测试
- 安全扫描

三个全部通过才算过关

---

## 28 个专用代理

| 代理 | 职责 |
|------|------|
| Architect | 架构设计 |
| Security Reviewer | 安全审查 |
| Builder Resolver | 修构建错误 |
| Test Runner | 运行测试 |
| Code Reviewer | 代码审查 |
| ... | ... |

**调用方式**：
- 直接指定：`使用 Security Reviewer 代理对 src/api/os.ts 进行完整审查`
- 通过命令：`/code-review` 触发 CodeReviewer 代理

---

## Hooks 自动化

### PostToolUse - 用完工具后自动跑脚本

**示例**：
- 编辑 TS 文件 → 自动跑 Prettier → 自动跑 TSC 类型检查
- 编辑 .py 文件 → 自动跑 Black → 自动跑 MyPy

### PreToolUse - 写文件之前检查

**防坑机制**：防止 AI 为了让错误消失，去改配置文件把检查关掉

### 其他 Hooks

| Hook | 功能 |
|------|------|
| Stop | 任务完成前验收 |
| SessionStart | 新会话加载记忆 |

---

## Skills 自动加载

**工作原理**：Claude Code 做任务时，自动加载相关 Skills 作为参考

**覆盖领域**：
- TypeScript, React, Python, Go
- Django 规范和安全检查清单
- Go 通用模式和测试规范

**上下文管理**：
- `/context` 查看当前加载了多少字符
- 超出预算时可扩容

---

## V2 持续学习系统（Instinct）

**核心思路**：从会话中提取可复用模式，进化成专属工作流

### 三步走

1. **提取模式**：`/learn` 分析当前会话，找出值得复用的内容
2. **查看状态**：`/instinct-status` 输出学到的东西（置信度分数）
3. **精华升级**：`/evolve` 合并升级，变成正式 skill 或 command

### Git 历史挖经验

```bash
/skill-create
```

分析 Git 提交历史，自动提取过去的编码模式，生成项目专属 skill

---

## 安装步骤

```bash
# 1. 克隆仓库
git clone https://github.com/EverythingClaudeCode/ECC

# 2. 复制配置文件到 ~/.claude
cp ECC/skills ~/.claude/skills
cp ECC/commands ~/.claude/commands
cp ECC/hooks ~/.claude/hooks
cp ECC/rules ~/.claude/rules
cp ECC/agents ~/.claude/agents
```

---

## 核心痛点解决

| 痛点 | ECC 解决方案 |
|------|--------------|
| 生成的代码没有测试 | `/tdd` 强制测试先行 |
| 经常忘了做安全审查 | Security Reviewer 代理 + Hooks |
| AI 不了解项目规范 | Skills 自动加载 + Rules 强制执行 |
| 方向走错浪费时间 | `/plan` 提前规划 |

---

## 与 OpenClaw 的对比思考

ECC 的思路和 OpenClaw 的 Skills 系统有相似之处：
- **Skills**：领域知识库
- **Hooks**：类似 OpenClaw 的 cron/heartbeat
- **Agents**：类似 OpenClaw 的 subagent

但 ECC 更侧重 Claude Code 的工程化约束，OpenClaw 更侧重多平台消息桥接和自动化运维。

---

*整理时间: 2026-03-31 21:30*