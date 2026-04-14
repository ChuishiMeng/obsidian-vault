# Hermes Agent

## 基本信息

- **名称**：Hermes Agent
- **类型**：Self-Improving AI Agent
- **核心能力**：Self-Improving Learning Loop（自改进学习循环）
- **推荐来源**：Alex Finn YouTube 频道

## 核心机制

### Self-Improving Learning Loop

```
执行任务 → 收集反馈 → 分析失败 → 提炼教训 → 更新策略 → 再次执行
```

与传统 Agent 的区别：
- 传统 Agent：固定策略，无自我进化
- Hermes：每次执行后自动改进策略

## 适用场景

| 场景 | 推荐程度 | 原因 |
|------|----------|------|
| **KDD Cup 冲刺** | ⭐⭐⭐⭐⭐ | 反复调参、内置 ML 工具 |
| **数据处理流程** | ⭐⭐⭐⭐⭐ | 流程固定、可自动化 |
| **实验迭代** | ⭐⭐⭐⭐⭐ | 重复性任务、自动改进 |
| **文献调研** | ⭐⭐⭐ | 需精细控制，不适合全自动 |
| **论文撰写** | ⭐⭐ | 需人工审核，不适合全自动 |

## 与 OpenClaw 的关系

### Alex Finn 的建议

> "You should 100% be using them together."
> （并行使用，非替代）

### 推荐工作流

```
Telegram (OpenClaw) ←→ Hermes CLI 并排使用

OpenClaw 负责：
- 快速响应
- 任务分发
- 进度监控

Hermes 负责：
- 重复性任务执行
- 自动调参
- 实验迭代
```

## 安装与使用

### 安装路径

```bash
# 孟老师系统已安装
/Users/chuishi-agent/.local/bin/hermes
配置目录：~/.hermes
```

### CLI 调用示例

```bash
# 基础调用
hermes chat -q "列出 ~/.hermes 目录" -Q --provider zai

# 任务执行
hermes chat -q "处理 KDD Cup 数据集" -Q
```

## 对孟老师团队的价值

1. **KDD Cup 冲刺**：Phase 3-5 已完成，后续可用 Hermes 加速迭代
2. **virtual-users 数据处理**：ANES/GSS 数据预处理可用 Hermes 自动化
3. **ontology-schema 实验**：内置 ML 工具，适合实验迭代

## 参考资料

- [Alex Finn YouTube](https://youtube.com/@alexfinn)
- [Hermes 官网](https://hermes.dev)
- [[2026-04-11-Hermes-Agent-vs-OpenClaw-对比]]（Obsidian 笔记）

## 相关概念

- [[ai-agent-frameworks]]
- [[self-improving-agent]]
- [[openclaw-vs-hermes]]

---
*创建时间：2026-04-14*
*来源：Alex Finn 视频解读*