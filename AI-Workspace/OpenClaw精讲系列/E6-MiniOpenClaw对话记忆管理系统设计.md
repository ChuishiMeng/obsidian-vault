# E6 - MiniOpenClaw对话记忆管理系统设计

> 视频链接: https://www.bilibili.com/video/BV1hAcjzJEan/?p=7
> 时长: 23:03
> 提取时间: 2026-02-19

## 核心知识点

1. **消息组成三部分**: 历史对话、System Prompt、当前用户消息
2. **System Prompt六部分**: Skills Snapshot、Soul.md、Identity.md、User.md、AGENTS.md、Memory.md
3. **记忆压缩与检索**: 历史对话压缩 + Memory.md混合检索

## 详细内容总结

### 一、Agent消息的构成

一次完整的Agent消息由三部分组成：

```
┌─────────────────────────────────────┐
│           消息列表结构               │
├─────────────────────────────────────┤
│  1. System Message (系统消息)       │
│     - Skills Snapshot               │
│     - Soul.md                       │
│     - Identity.md                   │
│     - User.md                       │
│     - AGENTS.md                     │
│     - Memory.md                     │
├─────────────────────────────────────┤
│  2. 历史对话消息 (Chat History)      │
│     - User Messages                 │
│     - Assistant Messages            │
├─────────────────────────────────────┤
│  3. 当前用户消息 (Current Message)   │
└─────────────────────────────────────┘
```

### 二、System Prompt的六大部分

| 文件 | 功能 | 说明 |
|------|------|------|
| **Skills Snapshot** | 技能列表 | 所有Skills的名称和描述 |
| **Soul.md** | 人格与语气 | Agent的性格特点 |
| **Identity.md** | 身份信息 | Agent的名字、版本 |
| **User.md** | 用户画像 | 服务对象信息、偏好语言 |
| **AGENTS.md** | 操作指南 | 能做什么、如何做、工具使用规范 |
| **Memory.md** | 长期记忆 | 跨会话的重要信息 |

### 三、自我修改机制

在AGENTS.md中设定规则，让Agent能够自我修改：

```markdown
## 记忆协议

当用户提到重要信息时：
- 将其写入 Memory.md
- 永久记住该信息

当用户说自己的名字时：
- 更新 User.md 中的用户名称

当用户要求扮演某个角色时：
- 修改 Soul.md 和 Identity.md
```

### 四、记忆存储结构

```
workspace/
├── skills/                    # Skills目录
│   └── skill-name/
│       └── SKILL.md
├── Soul.md                    # 人格定义
├── Identity.md                # 身份信息
├── User.md                    # 用户画像
├── AGENTS.md                  # 操作指南
├── Memory.md                  # 长期记忆
└── sessions/                  # 历史对话
    └── session_xxx.json
```

### 五、记忆压缩策略

#### 5.1 历史对话压缩

当历史对话过长时：
- 对最古老的50%-70%的消息进行Summary总结
- 用总结替换原始消息
- 可重复进行多次压缩

#### 5.2 长期记忆检索

当Memory.md超过阈值（如50k tokens）时：
- 不进行压缩（都是重要信息）
- 采用**混合检索**方式：
  - BM25关键词匹配（权重30%）
  - RAG向量相似度（权重70%）
- 根据用户问题检索相关内容

### 六、Mini OpenClaw演示

系统展示了：
1. 左下方实时显示消息列表
2. 灰色部分为System Message
3. 包含所有六部分的拼接内容
4. 支持实时修改Memory.md并生效

### 七、开发流程

1. **拼接消息**: 将六个MD文档 + 历史对话 + 当前消息拼接
2. **发送请求**: 发送给大模型API
3. **处理响应**: 解析工具调用或返回文本
4. **更新记忆**: 根据需要更新Memory.md等文件

## 总结

OpenClaw的记忆管理系统核心设计：
- **六文档结构**: 清晰分离不同类型的记忆
- **本地存储**: 完全透明，便于调试
- **自我修改**: 通过AGENTS.md中的规则实现
- **智能压缩**: 历史对话压缩 + 长期记忆检索

这套设计让Agent具备真正的"记忆"能力，实现越用越聪明的效果。

---

> 本文档基于视频转录内容整理
