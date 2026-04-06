# MCP vs CLI - AI Agent 接口之争

> 来源：微信公众号文章解读 + 搜索补充
> 时间：2026-03-30

---

## 核心事件

**钉钉和飞书同周选择 CLI 而非 MCP**

- 2026-03-17：钉钉「悟空」平台 CLI 开源（Go, Apache-2.0）
- 2026-03-28：飞书 CLI 开源（Go, MIT）

---

## ScaleKit Benchmark 数据

### Token 成本对比

| 任务 | CLI Token | MCP Token | 差距 |
|------|----------|----------|------|
| 查仓库语言 | 1,365 | 44,026 | **32 倍** |
| 查 PR 详情 | 1,648 | 32,279 | **20 倍** |
| 查仓库元数据 | 9,386 | 82,835 | **9 倍** |
| 月成本（1万次） | $3.2 | $55.2 | **17 倍** |

### 可靠性对比

| 指标 | CLI | MCP |
|------|-----|-----|
| 成功率 | **100%** | **72%** |
| 失败率 | 0% | **28%**（TCP 超时） |

---

## MCP 核心问题：Schema Bloat

### 问题描述

- GitHub MCP 有 43 个工具定义
- 每次对话全塞进上下文
- 单个工具定义占用 4,026 tokens
- 比喻：买瓶水要先听完整个商品目录

### CLI 优势

- `--help` 按需读取
- 不塞所有文档进上下文
- LLM 训练数据里有数十亿行 shell 命令

---

## 钉钉 vs 飞书 CLI 对比

### 基本信息

| 特性 | 钉钉 CLI | 飞书 CLI |
|------|---------|---------|
| 命令名 | `dws` | `lark-cli` |
| 语言 | Go (8MB) | Go (14MB, npm wrapper) |
| 协议 | Apache-2.0 | MIT |
| GitHub Stars | 815 | 1,313 |

### 飞书三层架构

```
Level 1: Shortcuts (+命令) - 智能默认值
Level 2: API Commands - 100+ 命令
Level 3: Raw API - 2500+ 端点直接调用
```

### 功能差异

| 钉钉独有 | 飞书独有 |
|---------|---------|
| OA 审批 | 邮件客户端 |
| 考勤管理 | 文档 Markdown 互转 |
| DING 消息 | 知识库管理 |
| `--mock` 模拟 | `--page-all` 翻页 |
| `--yes` 跳确认 | `--as` 身份切换 |

---

## 核心结论

### 一句话总结

> **CLI 是 LLM 的母语，MCP 是后天学的外语**

### 适用场景

| 场景 | 推荐 |
|------|------|
| 企业内部 Agent | **CLI** |
| 开放生态接入 | MCP |
| 快速开发调试 | **CLI** |
| 跨平台标准化 | MCP |

### MCP 未来定位

MCP 不会死，但会退居二线：
- 开放生态的接入协议层
- 类似 SOAP 退到企业集成角落

---

## 行业共识

### 街头投票（旧金山）

CLI: 17 票 | MCP: 3 票

### 代表人物观点

| 人物 | 观点 |
|------|------|
| Greg Brockman (OpenAI) | 站 CLI |
| Garry Tan (YC CEO) | "MCP sucks" |
| Denis Yarats (Perplexity CTO) | 远离 MCP，72% 上下文被占用 |

---

## 对 OpenClaw 的启示

### 当前实践验证

OpenClaw 的 skill 设计（CLI wrapper）是正确方向：
- GitHub skill 用 `gh` CLI
- 各服务优先 CLI 接口

### 建议

1. 继续优先 CLI 方式
2. MCP 作为开放生态备选
3. 关注 CLI-Anything、OpenCLI 等项目

---

## 相关链接

- 飞书 CLI: https://github.com/larksuite/cli
- 钉钉 CLI: https://github.com/open-dingtalk/dingtalk-workspace-cli
- ScaleKit Benchmark: https://www.scalekit.com/blog/mcp-vs-cli-use

---

*保存时间: 2026-03-30 11:15 Asia/Shanghai*