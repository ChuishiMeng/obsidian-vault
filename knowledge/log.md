# 知识库操作日志 - Knowledge Log

> 追加记录，不可修改历史条目

---

## [2026-04-05] ingest-batch | Obsidian 历史内容编译
- 从 Obsidian AI-Workspace 编译 59 个 Wiki 页面
- Sources: 32 篇（文章/访谈/技术/视频）
- Concepts: 10 个（第一性原理、AI Agent、Vibe Coding 等）
- Entities: 9 个（尹烨、辉哥、哈萨比斯、OpenClaw 等）
- Topics: 8 个（AI Agent 趋势、健康养生、职业发展 等）
- 所有页面带 YAML frontmatter 和 [[双链]] 交叉引用
- 子 Agent 超时但已完成大部分工作
- 创建 knowledge/ 目录结构
- 初始化 index.md
- 初始化 log.md
- 来源：Karpathy LLM Wiki 实践方案

## [2026-04-05] ingest-batch | Products 产品文档编译（16篇）
- 编译来源：~/agentKB/Obsidian/AI-Workspace/Products/（4 个子目录，16 篇文档）
- DataAgent（3 篇）：智能问数 PRD v1.0/v1.1 + 产品手册
- 乐高智能化（6 篇）：升级调研报告、技术方案、Figma AI 拆解、功能对标、技术分析、用户故事
- 曦和天工（5 篇）：平台整体架构 + 4 大模块设计文档
- 营销智能化（2 篇）：交互流程 + 技术实现设计
- 创建 Topics: 4 个（智能问数、乐高智能化、曦和天工、营销智能化）
- 创建 Entities: 1 个（曦和天工产品实体）
- 更新 index.md：Topics +4 行，Entities +1 行
- 所有文件带 YAML frontmatter 和 [[双链]] 交叉引用

## [2026-04-05] ingest | Karpathy LLM Wiki
- 保存到 knowledge/sources/karpathy-llm-wiki.md
- 创建概念/实体/主题页面（待补充）
- 原文：https://mp.weixin.qq.com/s/zOAsp5uZh_JTUb4VDliC0A
- 原始 Gist：https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f

## [2026-04-05] ingest-batch | Obsidian 历史内容编译
- 编译来源：~/agentKB/Obsidian/AI-Workspace/ 及子目录
- 跳过：每日信息源汇报（13个时效性文件）
- 创建文件统计：
  - Sources: 32 个（文章/访谈 21 个 + 技术 4 个 + 视频总结 7 个）
  - Concepts: 10 个（第一性原理、AI Agent、Agent 记忆、Data Agent、长期主义、正念、肠脑轴、AI 编程、MCP、Vibe Coding）
  - Entities: 9 个（尹烨、辉哥、哈萨比斯、张雪、Peter Steinberger、OpenClaw、Claude Code、熊辉、冯唐）
  - Topics: 8 个（AI Agent 趋势、健康养生、心理健康、职业发展、OpenClaw 实战、Data Agent 研究、科技行业观察、八段锦太极课程）
- 总 Wiki 页面：59 个
- 所有文件使用 YAML frontmatter（title, date, tags, source）
- 文件之间用 [[双链]] 互相引用
- 每个文件控制在 300-500 字以内（精简摘要）
- 更新 knowledge/index.md（所有页面的索引表）

## [2026-04-05] ingest-batch | Research 论文编译（101 篇）
- 编译来源：~/agentKB/Obsidian/AI-Workspace/Research/
- 5 个研究方向：
  - DA-CaseBase：16 篇论文 → topics/da-casebase.md + entities/da-casebase.md
  - DA-Disambiguation：4 篇核心论文 → topics/da-disambiguation.md + entities/da-disambiguation.md
  - Urban-Computing：29 篇论文 → topics/urban-computing.md + entities/urban-computing.md
  - VirtualUser：15 篇核心论文 → topics/virtual-user-research.md + entities/virtual-user.md
  - Ontology-Schema：13 篇论文 → topics/ontology-schema.md + entities/ontology-schema.md
- 新增概念页：8 个（语义缓存、案例推理、SQL 等价性判断、Schema Linking、虚拟用户 Persona、城市计算、跨域数据融合、意图消歧）
- 更新索引：knowledge/index.md（Sources 增加 Research 类，Concepts/Entities/Topics 增加新条目）
- 总 Wiki 页面：64 → 82 个
- 所有页面带 YAML frontmatter 和 [[双链]] 交叉引用
- 主题页控制在 500-800 字，概念页控制在 200-400 字
