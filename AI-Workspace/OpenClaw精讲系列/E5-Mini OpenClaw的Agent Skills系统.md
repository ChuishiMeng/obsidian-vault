# E5 - Mini OpenClaw的Agent Skills系统

> 视频链接: https://www.bilibili.com/video/BV1hAcjzJEan/?p=6
> 时长: 09:00
> 提取时间: 2026-02-19

## 核心知识点

1. **Agent Skills系统本质**: 基于文件夹和Markdown文档的轻量级技能管理
2. **Skills接入无关框架**: 无论使用LangChain、ADK等何种框架都能简单接入
3. **Skills工作流程**: 元数据注册 → 按需加载 → Tool Response传递

## 详细内容总结

### 一、Agent Skills系统的定位

Agent Skills系统是现代Agent开发中**必须提前规划**的核心模块。与MCP等复杂协议不同，Anthropic提出的Skills范式相对简单，只需通过文本拼接即可实现。

**关键认知**：
- Skills接入与开发框架无关（LangChain、ADK均可）
- 只需设定简单工作流即可接入
- Skills本质上是操作手册的规范化管理

### 二、Skills文件结构

```
project/
└── skills/                    # Skills存放文件夹
    ├── weather-query/         # 单个技能文件夹
    │   └── SKILL.md          # 技能定义文档
    ├── pdf-reading/
    │   └── SKILL.md
    └── ...其他技能/
```

**结构说明**：
- 每个Skills有一个独立的文件夹
- 文件夹内至少包含一个 `SKILL.md` 文件
- 文件夹名称即为技能标识

### 三、SKILL.md 文档格式

遵循Anthropic提出的Skills基础范式，元数据包含：

```markdown
---
name: weather-query
description: 查询天气信息，支持国内外主要城市
---

# 天气查询技能

## 使用说明
当用户询问天气相关问题时，使用Fetch工具访问天气API...

## 示例
用户: 北京今天天气怎么样？
操作: 使用Fetch访问 https://wttr.in/Beijing
```

**必须字段**：
| 字段 | 说明 |
|------|------|
| `name` | 技能名称，用于标识和调用 |
| `description` | 功能描述，模型据此判断是否选用 |

### 四、Skills调用流程

```
1. 会话初始化
   └── 程序读取 skills/ 下所有 SKILL.md 的元数据
   └── 拼接生成 skills-snapshot.md
   └── 作为 System Message 注入到对话开始

2. 模型运行时
   └── 模型根据 description 判断是否需要某个 Skill
   └── 选定后，通过文件读取工具加载完整 SKILL.md
   └── SKILL.md 内容作为 Tool Response Message 返回

3. 后续处理
   └── 模型理解操作手册内容
   └── 根据指导调用其他工具（如命令行、Fetch等）
   └── 完成任务
```

### 五、Skills Snapshot 机制

**skills-snapshot.md** 是Skills系统的核心：

- **生成时机**: 每次会话开始时
- **内容来源**: 读取所有SKILL.md的元数据
- **保留信息**: name、description、SKILL.md路径
- **注入方式**: 作为System Message放在会话列表最前面

**示例格式**：
```markdown
# Available Skills

## weather-query
- 描述: 查询天气信息
- 路径: ./skills/weather-query/SKILL.md

## pdf-reading
- 描述: PDF文档阅读与解析
- 路径: ./skills/pdf-reading/SKILL.md
```

### 六、多步工具调用

当Skill内容包含操作指导而非直接答案时：

1. 模型读取Skill文档（Tool Response）
2. 根据文档指导，决定下一步操作
3. 调用其他内置工具（如命令行执行curl）
4. 获取最终结果返回用户

**这种设计实现了**：
- Skills作为"操作手册"而非"数据源"
- 灵活组合多种工具完成任务
- 支持复杂的链式调用

## 架构图示

```
┌─────────────────────────────────────────────────────┐
│                    System Message                    │
│  ┌─────────────────────────────────────────────┐   │
│  │           skills-snapshot.md                 │   │
│  │  • Skill A: description → path              │   │
│  │  • Skill B: description → path              │   │
│  │  • Skill C: description → path              │   │
│  └─────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────┐
│                  User Message                        │
│            "帮我查一下北京天气"                       │
└─────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────┐
│              Model Decision                          │
│     根据 description 选择 weather-query Skill        │
└─────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────┐
│              Tool Call: ReadFile                     │
│         读取 ./skills/weather-query/SKILL.md        │
└─────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────┐
│              Tool Response Message                   │
│        SKILL.md 完整内容（操作指导手册）               │
└─────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────┐
│              Model Action                            │
│    根据手册指导，调用 curl/fetch 获取天气数据          │
└─────────────────────────────────────────────────────┘
```

## 总结

Mini OpenClaw的Agent Skills系统设计精妙：

1. **轻量级**: 仅需文件夹+Markdown，无复杂依赖
2. **框架无关**: 任何Agent框架都能简单接入
3. **按需加载**: 通过Snapshot机制实现高效上下文管理
4. **灵活组合**: Skills可指导调用其他工具，实现复杂任务

这套设计是理解和实现Agent技能管理的基础范式。

---

> 本文档基于视频转录内容整理，可能存在部分识别误差
