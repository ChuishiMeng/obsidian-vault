# E9 - Mini-OpenClaw Readme文档与项目部署

> 视频链接: https://www.bilibili.com/video/BV1hAcjzJEan/?p=10
> 时长: 22:03
> 提取时间: 2026-02-19
> 标签: #OpenClaw #Mini-OpenClaw #项目文档 #部署

---

## 一、Readme 文档的重要性

### 1.1 文档生成流程

```
开发需求文档 → Web CoPilot 开发 → 多轮调试优化 → 生成 Readme 文档
```

**核心价值**：
- 记录最终版本（可能与原始需求有出入）
- 包含完整技术选型、项目结构、环境配置
- 便于团队协作与项目交接
- 方便后续维护与迭代

### 1.2 文档核心内容

| 模块 | 内容 |
|------|------|
| **技术选型** | 后端框架、大模型、RAG引擎、前端技术栈 |
| **项目结构** | 目录组织、各模块职责 |
| **环境配置** | API Key、依赖安装、启动命令 |
| **架构说明** | 后端架构、前端架构、API 设计 |

---

## 二、技术选型详解

### 2.1 后端技术栈

| 组件 | 选型 | 说明 |
|------|------|------|
| **后端框架** | FastAPI | Python 异步 Web 框架 |
| **LLM 框架** | LangChain 1.2+ | 支持最新 Chat Completion API |
| **大模型** | DeepSeek | 国产大模型，性价比高 |
| **RAG 引擎** | LangChain 1.x + LangChain Index | 混合检索 |
| **Embedding** | OpenAI text-embedding | 向量化模型 |
| **Token 计算** | TikToken | 精确计算 Token 消耗 |

**重要提示**：严禁使用旧版 LangChain API！

### 2.2 前端技术栈

| 组件 | 选型 |
|------|------|
| **UI 框架** | 响应式设计 |
| **状态管理** | 现代状态管理库 |

> 前端对大模型来说生成相对简单，主要关注样式和交互调整

---

## 三、项目结构解析

```
mini-openclaw/
├── backend/                    # 后端代码
│   ├── app.py                 # FastAPI 入口
│   ├── config.py              # 配置管理
│   ├── api/                   # API 路由层
│   ├── graph/                 # Agent 执行核心逻辑
│   │   └── agent.py           # Agent 运行主逻辑 ⭐
│   ├── tools/                 # 内置工具模块
│   ├── workspace/             # System Prompt 组件
│   ├── skills/                # 技能模块目录
│   ├── memory/                # 记忆存储
│   ├── knowledge/             # 知识库
│   └── skill_snapshots.md     # 技能快照
│
└── frontend/                   # 前端代码
    └── ...                    # UI 相关文件
```

### 3.1 核心文件说明

| 文件/目录 | 职责 |
|-----------|------|
| `app.py` | FastAPI 服务入口 |
| `config.py` | 全局配置、环境变量 |
| `api/` | RESTful API 路由 |
| `graph/agent.py` | **Agent 核心运行逻辑** |
| `tools/` | 文件操作、命令行、Fetch 等工具 |
| `workspace/` | Soul.md、Memory.md 等 Prompt 组件 |
| `skills/` | 可扩展的技能包 |

---

## 四、项目功能演示

### 4.1 智能天气查询示例

```
用户: 查询北京天气
Agent 思考过程:
1. 检查技能列表 → 发现 GetWeather Skill
2. 尝试读取 Skill 文档 → 路径问题
3. 自动调试 → 使用命令行查找正确路径
4. 成功读取 → 调用 Fetch URL 工具
5. 返回天气信息
```

**关键技术点**：
- 自动迭代调试（Auto-Repair）
- Function Calling 动态读取 Skill
- 工具链组合使用

### 4.2 自我进化能力

**场景**：Agent 发现自己常犯路径错误

```
用户提示: "你每次调用天气查询都会找错路径，能不能给自己做个提示？"
Agent 行为:
1. 检查长期记忆 (memory.md)
2. 更新 memory.md，添加路径信息
3. 下次运行时读取更新后的记忆
4. 不再重复相同错误
```

---

## 五、记忆管理功能

### 5.1 上下文压缩

**功能按钮**：
- 🔧 **历史压缩**：压缩前 50% 对话，用 Summary 替换原始消息
- 📦 **Memory RAG**：对 memory.md 进行向量化，使用 BM25+RAG 混合检索

### 5.2 压缩策略

```
对话超过阈值 → 对较早 50% UserMessage 进行 Summary → 删除原始消息 → 节省 Token
```

### 5.3 长期记忆检索

当 memory.md 超过 50k tokens：
- 不再全量加载
- 启用 **BM25 + 向量检索** 混合模式
- 权重：关键词 30% + 向量相似度 70%

---

## 六、Skills 系统设计

### 6.1 Skill 文件结构

```markdown
---
name: GetWeather
description: 获取指定城市的天气信息
---

# GetWeather Skill

## 功能
获取天气信息

## 使用方法
1. 让用户提供地点
2. 使用 Fetch URL 工具调用天气 API
3. 返回天气信息
```

### 6.2 调用流程

```
1. System Prompt 感知 Skills 列表（通过元数据）
2. Function Calling 决策需要哪个 Skill
3. 读取完整 Markdown 文档
4. 按文档指导执行操作
```

**为什么需要 Skill 而不是写在 System Prompt？**
- 工具用法无限多，无法全部预置
- 按需加载，减少 Token 消耗
- 灵活扩展，用户可自定义

---

## 七、Web CoPilot 开发经验

### 7.1 开发流程

```
1. 编写详细需求文档
2. 将文档放入 .claw 文件夹
3. 执行 /init 命令初始化项目
4. CoPilot 自动生成 CLAW.md（最高开发准则）
5. 多轮对话迭代开发
6. 生成最终 Readme 文档
```

### 7.2 关键技巧

| 技巧 | 说明 |
|------|------|
| **需求文档要详细** | 技术选型、架构设计、功能细节都要明确 |
| **迭代调试** | 先跑通最小功能单元，再逐步丰富 |
| **错误经验沉淀** | 将调试经验写成 Skill，避免重复犯错 |
| **模块化维护** | 复杂功能拆分模块，便于维护 |

---

## 八、课程推荐（广告部分）

### 8.1 大模型 Agent 正经开发实战课

**五大模块**：
1. **Stage 1**：大模型零技术入门
2. **Stage 2**：Agent 开发框架（Coze、Dify、LangChain、LangGraph）
3. **Stage 3**：工业级 Agent 进阶（记忆管理、RAG、Skills、MCP）
4. **Stage 4**：部署上线（Docker、K8S、运维、评估）
5. **Stage 5**：14 项工业级项目实战

### 8.2 Web Coding AI 全栈开发课

**核心内容**：
- 200 亿 Token 实战经验分享
- 4 项工业级项目交付
- AI 编程工具深度使用

---

## 九、核心要点总结

| 要点 | 内容 |
|------|------|
| **Readme 作用** | 记录最终版本，便于协作和维护 |
| **技术选型** | LangChain 1.2+ + DeepSeek + FastAPI |
| **项目结构** | backend/frontend 分离，模块化设计 |
| **自我进化** | 自动更新 memory.md，从错误中学习 |
| **Skills 设计** | Markdown + 元数据 + 按需加载 |
| **记忆管理** | 压缩 + RAG 混合检索 |

---

*注：本集重点展示了 Mini-OpenClaw 项目的完整文档结构和实际运行效果，为后续深入学习奠定基础*
