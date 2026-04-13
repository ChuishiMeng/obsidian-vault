---
title: Karpathy LLM Wiki - 原始想法文件
date: 2026-04-05
source: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
author: Andrej Karpathy
tags: [知识管理, LLM-Wiki, Agent, Karpathy, Gist]
type: primary-source
---

# Karpathy LLM Wiki - 原始想法文件（Idea File）

> 📄 原始 Gist：[karpathy/LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
> 📄 原始推文：[1347万浏览](https://x.com/karpathy/status/2039805659525644595)（2026-04-02）
> 📄 后续推文：[分享 Gist](https://x.com/karpathy/status/2040470801506541998)（2026-04-04）

---

## 核心思想

**RAG 的问题**：每次查询都从零开始「重新发现」知识，没有积累。

**LLM Wiki 的方案**：让 LLM **增量构建和维护持久化的 Wiki**——一个结构化、互相链接的 Markdown 文件集合，位于你和原始素材之间。

> **Obsidian 是 IDE，大模型是程序员，Wiki 是代码库。**

---

## 三层架构

### Raw Sources（原始素材）
- 文章、论文、图片、数据文件
- **不可变**：LLM 只读不写
- 这是真相之源

### The Wiki（知识库）
- LLM 生成的 Markdown 文件目录
- 摘要、实体页、概念页、对比页、总览、综合分析
- **LLM 全权拥有**：创建、更新、交叉引用、保持一致性
- 人类只读，LLM 写

### The Schema（规则文件）
- CLAUDE.md（Claude Code）或 AGENTS.md（Codex）
- 告诉 LLM Wiki 如何组织、约定、工作流
- **人类和 LLM 共同进化**

---

## 四大操作

### Ingest（导入）
1. 新素材放入 raw/
2. LLM 读取、讨论要点
3. 写摘要页
4. 更新索引
5. 更新相关实体/概念页
6. 追加日志
7. **一篇素材可能联动 10-15 个 Wiki 页面**

### Query（查询）
- LLM 搜索相关页面 → 读取 → 综合回答（带引用）
- 输出格式：Markdown / Marp 幻灯片 / matplotlib 图表
- **关键洞察**：好的回答可以存回 Wiki 作为新页面
- 规模：~100 篇素材、~40 万字，无需复杂 RAG

### Lint（自检）
- 页面间矛盾检测
- 过时数据标注
- 孤立页面发现
- 缺失交叉引用
- 数据空缺补全（网络搜索）
- 建议新的研究方向

### File Back（回填）
- 查询结果存回 Wiki
- 你的探索让知识库更丰富
- **用的越多越聪明**

---

## 索引和日志

### index.md
- 内容导向的 Wiki 目录
- 每页有链接 + 一行摘要 + 元数据
- LLM 每次 Ingest 后更新
- 查询时先读 index 找相关页

### log.md
- 时间线记录（追加）
- 格式：`## [2026-04-02] ingest | Article Title`
- 可用 unix 工具解析：`grep "^## \[" log.md | tail -5`

---

## 应用场景

- **个人**：目标追踪、健康、心理学、自我提升
- **研究**：论文/文章/报告 → 持续构建研究 Wiki
- **读书**：章节 → 角色/主题/情节线页面
- **商业/团队**：Slack 线程、会议记录、项目文档
- **竞品分析、尽职调查、旅行规划、课程笔记、爱好深挖**

---

## 工具推荐

- **Obsidian Web Clipper**：网页 → Markdown
- **本地图片下载**：确保 LLM 可直接引用
- **Graph View**：查看知识网络
- **Marp**：Markdown → 幻灯片
- **Dataview**：查询页面元数据
- **Git**：版本控制
- **qmd**：本地搜索引擎（BM25 + 向量 + LLM 重排序）

---

## 为什么有效

维护知识库最繁琐的是**簿记**（bookkeeping）——更新交叉引用、保持摘要最新、标注矛盾、维护一致性。

人类放弃 Wiki 是因为维护成本增长比价值快。

**LLM 不觉得无聊、不会忘记更新交叉引用、一次能处理 15 个文件。维护成本接近零。**

> 人类负责策展、分析方向、提出好问题、思考意义。LLM 负责其他一切。

---

## 与 Vannevar Bush Memex 的联系

1945 年 Bush 提出个人化知识存储系统 Memex，文档间由「关联线索」连接。

Bush 没解决的问题是：**谁来做维护？**

现在 LLM 解决了。
