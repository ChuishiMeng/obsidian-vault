# E0 - 抢先掌握2026开发'神技'OpenClaw精讲

> 视频链接: https://www.bilibili.com/video/BV1hAcjzJEan/?p=1
> 时长: 10:40
> 提取时间: 2026-02-19

## 核心知识点

1. **OpenClaw爆火原因**: 易用性、高度普适性、自由组装Skills、自主进化
2. **八大核心功能模块**: Gateway、Channel、Session、Agent、Tools、Node、Sandbox、Skills
3. **三大核心特性**: 无限记忆、自主进化、灵活载入技能

## 详细内容总结

### 一、OpenClaw的核心定位

OpenClaw发布仅一个半月就登上Github热榜，成为史上最火的AI Agent应用。它定义了全新一代Agent的必备基础功能：
- 易用性
- 高度普适性  
- 可定制化功能
- 自主功能进化

### 二、八大核心功能模块

| 模块          | 功能说明                     |
| ----------- | ------------------------ |
| **Gateway** | 网关，管理所有渠道连接、路由、会话，相当于总指挥 |
| **Channel** | 消息平台集成，打通飞书、企微、微信等聊天软件   |
| **Session** | 保存长短期对话、记忆               |
| **Agent**   | 处理具体任务                   |
| **Tools**   | 原生自带的工具集                 |
| **Node**    | 远程连接OpenClaw的设备          |
| **Sandbox** | 沙盒环境，安全运行代码或命令行的隔离环境     |
| **Skills**  | 装载在OpenClaw上的各项技能        |

### 三、三大核心模块详解

#### 1. Tools 内置工具模块

参考顶级编程Agent设计，内置四大工具：
- **文件夹操作工具**: 读写操作本地文件和文件夹
- **命令行执行工具**: 自由调用计算机终端执行命令
- **网络搜索工具**: 使用Fetch工具或Brave Search进行网络搜索
- **浏览器自动化工具**: 打开并操作浏览器，获取网络信息，模拟人工操作

#### 2. Agent Skills 功能

**Skills本质**: Agent执行某项工作的具体操作指南

**技术实现原理**:
1. 每个Skills是一个保存在同名文件夹中的Markdown文档
2. 每个Skills.md增加元数据（名称、描述）
3. 开启对话时，将Skills的元数据拼接到系统消息中
4. 通过Function Calling按需加载Skills完整文档
5. Skills内容作为上下文，配合工具调用完成任务

**Skills进阶用法**:
- 可放置不同功能的执行脚本
- Agent可以自主创建/修改Skills（自主进化）
- 通过Memory.md长期记忆新技能

#### 3. Session 记忆管理系统

**两大核心策略**:

1. **本地优先的记忆存储**
   - 历史对话信息、Agent成长记忆、人格工作流、混搭风格语气等分类存储
   - 使用不同的MD文件管理不同类型信息：
     - `Soul.md`: Agent人格语气和能力辨识
     - `Agent.md`: Agent身份信息设定
     - `User.md`: 使用Agent的用户信息
     - `AGENTS.md`: 当前Agent的说明书
     - `Memory.md`: 跨会话的长期记忆

2. **记忆文本总结与检索（RAG）**
   - **上下文压缩**: 当上下文超过阈值，对历史70%的UserMessage和AgentMessage进行Summary总结
   - **长期记忆检索**: 当Memory.md超过阈值（如50k tokens），采用BM25+RAG混合检索
     - 关键词匹配权重30%
     - 向量相似度权重70%

### 四、关键代码/配置示例

#### Skills元数据格式示例

```markdown
---
name: weather-query
description: 查询天气信息
---

# 查询天气

使用Fetch URL工具，访问 https://wttr.in/{城市名} 获取天气信息
```

#### 记忆文件结构

```
workspace/
├── Soul.md          # Agent人格定义
├── Agent.md         # Agent身份设定
├── User.md          # 用户信息
├── AGENTS.md        # Agent说明书
├── Memory.md        # 长期记忆
├── memory/          # 日常记忆目录
│   └── YYYY-MM-DD.md
└── skills/          # Skills目录
    └── skill-name/
        └── SKILL.md
```

## 总结

OpenClaw的核心竞争力在于：
1. **工具生态**: 四大内置工具覆盖文件、命令、网络、浏览器
2. **Skills热插拔**: 通过Function Calling按需加载，支持自主创建和修改
3. **记忆系统**: 本地优先存储 + RAG检索，实现真正的无限上下文

这套架构设计是2026年大模型开发者必学的核心技术栈。

---

> 本文档基于视频转录内容整理，可能存在部分识别误差
