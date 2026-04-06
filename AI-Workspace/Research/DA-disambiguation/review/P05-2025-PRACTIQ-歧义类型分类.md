# PRACTIQ 论文笔记

**标题**: PRACTIQ: A Practical Conversational Text-to-SQL dataset with Ambiguous and Unanswerable Queries  
**发表**: NAACL 2025

---

## 1. 歧义类型分类

### 4 种歧义类型

| 类型 | 说明 | 示例 |
|------|------|------|
| **Ambiguous SELECT Column** | 选择列歧义 | "显示用户信息" → ID/姓名/全部？ |
| **Ambiguous Values Within Column** | 列内值歧义 | "状态=1" → 成功/待处理？ |
| **Ambiguous WHERE Columns** | WHERE 列歧义 | "按日期" → 创建日期/更新日期？ |
| **Ambiguous Filter Criteria** | 过滤条件歧义 | "优质用户" → 存款达标/理财达标？ |

### 4 种无法回答类型

| 类型 | 说明 |
|------|------|
| **Nonexistent SELECT Column** | 列不存在 |
| **Nonexistent WHERE Column** | WHERE 列不存在 |
| **Nonexistent Filter Value** | 过滤值不存在 |
| **Unsupported Join** | 不支持的连接 |

---

## 2. 数据集构建方法

### 三阶段流程

```
Stage 1: SQL 解析 → 修改数据库 Schema
Stage 2: 逆向生成 → 先 SQL 后用户响应 + Helpful SQL 策略
Stage 3: 对话优化 → 3-shot 改进 + 质量控制
```

### 数据集规模

- **总对话**: 2812 轮
- **歧义/无法回答**: 1802 轮
- **可回答**: 1034 轮

---

## 3. 与本研究的关系

### 三层框架对应

| 层面 | PRACTIQ 覆盖 | 我们的创新 |
|------|-------------|-----------|
| **数据层** | ✅ 主要覆盖 | Schema 歧义处理 |
| **业务层** | ⚠️ 部分涉及 | 概念定义 + 业务规则消歧 |
| **用户层** | ❌ 未涉及 | 用户画像驱动消歧 |

### 可借鉴技术

- 逆向生成确保 SQL 准确性
- Helpful SQL 减少交互轮数
- 9-way 分类任务设计
- 质量控制流程