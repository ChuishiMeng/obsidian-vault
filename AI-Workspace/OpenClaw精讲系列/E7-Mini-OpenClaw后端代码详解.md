# E7 - Mini-OpenClaw后端代码详解

> 视频链接: https://www.bilibili.com/video/BV1hAcjzJEan/?p=8
> 时长: 08:35
> 提取时间: 2026-02-19

## 核心知识点

1. **项目架构**: 轻量级Mini版，去除Gateway，保留核心功能
2. **开发方式**: 与AI编程工具（如CursorCode）协作开发
3. **模块划分**: Tools、Skills、Storage、Graph四大核心模块

## 详细内容总结

### 一、项目概述

Mini OpenClaw是原版OpenClaw的精简版本：
- **架构更轻量**: 移除了Gateway网关层
- **核心保留**: 保留Skills、Memory、Tools等核心功能
- **开发模式**: 完全通过CursorCode等AI编程工具协作完成

**开发理念**: 非传统二次开发，而是通过完整描述需求，引导AI工具逐步完成开发。

### 二、项目目录结构

```
mini-openclaw/
├── config.py              # 配置变量
├── requirements.txt       # Python依赖
│
├── workspace/             # 工作空间（核心）
│   ├── AGENTS.md          # Agent说明书
│   ├── IDENTITY.md        # 身份定义
│   ├── SOUL.md            # 人格设定
│   └── USER.md            # 用户信息
│
├── tools/                 # 内置工具模块
│   ├── fetch_url.py       # 网络请求工具
│   ├── python_repl.py     # Python代码执行器
│   ├── file_tool.py       # 文件操作工具
│   ├── search_knowledge.py # RAG检索工具
│   └── terminal_tool.py   # 命令行工具
│
├── skills/                # 技能目录
│   └── get_weather/
│       └── SKILL.md       # 天气查询技能
│
├── skills_scanner/        # Skills扫描器
│   └── scanner.py         # 生成skills-snapshot
│
├── storage/               # 存储模块
│   ├── memory_index/      # Memory索引（RAG用）
│   └── knowledge/         # 知识库（Summary结果）
│
├── session/               # 会话管理
│   ├── default.json       # 默认会话模板
│   └── *.json             # 历史会话文件
│
├── memory/                # 记忆系统
│   ├── MEMORY.md          # 长期记忆
│   └── log/               # 记忆修改日志
│
├── api/                   # API接口层
│   └── *.py               # 对话、设置、Token计算等
│
└── graph/                 # 核心Agent逻辑
    ├── agent.py           # 使用createAgent构建
    ├── prompt_builder.py  # System Prompt构建
    └── session_manager.py # 会话管理器
```

### 三、核心模块详解

#### 1. Tools 工具模块

| 工具 | 功能 | 实现方式 |
|------|------|----------|
| `fetch_url.py` | 获取网络信息 | HTTP请求 |
| `python_repl.py` | Python代码执行 | LangChain内置 |
| `file_tool.py` | 文件增删改查 | 标准文件操作 |
| `search_knowledge.py` | 混合检索 | LlamaIndex (BM25+向量) |
| `terminal_tool.py` | 命令行操作 | LangChain内置 |

**关键认知**: 接入命令行工具 = 半个无限能力（可自由操作电脑）

#### 2. Skills Scanner 模块

**功能**: 启动会话前扫描所有Skills，生成Snapshot

```python
# 扫描流程
skills/ → 读取所有SKILL.md → 提取元数据 → 拼接成System Message
```

**输出**: 注入到System Message的skills-snapshot部分

#### 3. Storage 存储模块

- **memory_index/**: Memory文件的RAG索引
  - 当Memory超过阈值时，使用混合检索
  - 基于LlamaIndex实现

- **knowledge/**: 会话Summary结果
  - 历史对话过长时的压缩摘要
  - 保存会话核心信息

#### 4. Graph 核心模块

使用LangChain 1.0+ 的 `createAgent` API：

```python
# 核心组件
agent = createAgent(
    tools=[...],           # 内置工具列表
    memory=memory_handler, # 实时读取Memory
    system_prompt=prompt_builder.build()
)
```

**prompt_builder职责**:
- 拼接SOUL.md、IDENTITY.md、AGENTS.md、USER.md
- 注入skills-snapshot
- 计算总Token数

**session_manager职责**:
- 读取/保存会话JSON
- Token计算与控制
- 提供会话Summary能力

### 四、数据流向图

```
┌─────────────────────────────────────────────────────────────┐
│                       启动阶段                               │
├─────────────────────────────────────────────────────────────┤
│  skills/ ──→ scanner ──→ skills-snapshot                    │
│  workspace/*.md ──→ prompt_builder ──→ System Prompt        │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                       运行阶段                               │
├─────────────────────────────────────────────────────────────┤
│  User Message ──→ Agent ──→ Tool Call ──→ Tool Response    │
│                           ↓                                  │
│                    Session Manager                           │
│                    (保存对话历史)                             │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                       记忆管理                               │
├─────────────────────────────────────────────────────────────┤
│  历史过长? ──→ Summary ──→ knowledge/                       │
│  Memory过大? ──→ RAG检索 ──→ memory_index/                  │
└─────────────────────────────────────────────────────────────┘
```

### 五、与原版OpenClaw的差异

| 特性 | 原版OpenClaw | Mini OpenClaw |
|------|-------------|---------------|
| Gateway网关 | ✅ 有 | ❌ 无 |
| 多Channel支持 | ✅ 有 | ❌ 无 |
| Skills系统 | ✅ 有 | ✅ 有（简化版） |
| Memory系统 | ✅ 有 | ✅ 有 |
| 内置Tools | ✅ 完整 | ✅ 核心工具 |
| Node设备管理 | ✅ 有 | ❌ 无 |
| Sandbox沙盒 | ✅ 有 | ❌ 无 |

### 六、开发建议

使用AI编程工具协作开发时：
1. **清晰描述需求**: 架构规划、核心功能要提前讲清楚
2. **持续修正**: AI生成的代码需要人工review和修正
3. **架构对齐**: 不断与AI工具同步项目架构理解
4. **效率提升**: 相比纯手写开发，效率显著提高

## 总结

Mini OpenClaw后端代码结构清晰：

1. **轻量设计**: 移除Gateway，专注核心Agent能力
2. **模块化**: Tools、Skills、Storage、Graph各司其职
3. **标准实现**: 基于LangChain createAgent，符合主流范式
4. **AI协作**: 全程可借助AI编程工具完成开发

这套架构是理解OpenClaw核心机制的绝佳学习材料。

---

> 本文档基于视频转录内容整理，可能存在部分识别误差
