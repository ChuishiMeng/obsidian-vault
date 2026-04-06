# P18-2025-OpenSearch-SQL - 动态 Few-shot 与一致性对齐（深度解读）

> **论文标题**：OpenSearch-SQL: Enhancing Text-to-SQL with Dynamic Few-shot and Consistency Alignment
> **作者**：Xiangjin Xie, Guangwei Xu, LingYan Zhao, Ruijie Guo（阿里巴巴）
> **年份**：2025
> **来源**：SIGMOD 2025
> **链接**：https://arxiv.org/abs/2502.14913
> **相关 RQ**：RQ1 语义相似度匹配, RQ2 案例检索策略
> **相关度**：⭐⭐⭐ **（动态 Few-shot 策略可借鉴）**

---

## 一、研究背景

### 1.1 问题场景：多 Agent 协作的 LLM 不稳定

**背景**：当前 Text-to-SQL 系统多采用多 Agent 协作架构，但效果不稳定。

**核心问题**：Agent 之间存在"幻觉累积"问题。

**具体场景**：

```
多 Agent 协作流程：
Agent 1 (Schema Linking) → 输出: 表和列
Agent 2 (SQL Generation) → 使用 Agent 1 的输出生成 SQL
Agent 3 (Debug) → 检查 SQL 错误

问题：
- Agent 1 输出错误 → Agent 2 继承错误 → Agent 3 无法修正
- 错误会累积，不会自动消失
```

**通俗理解**：

```
就像传话游戏：
A 告诉 B → B 告诉 C → C 告诉 D

如果 A 说错了，后面的人都会错
而且越传越离谱
```

---

### 1.2 多 Agent 协作的三大问题

| 问题 | 说明 | 表现 |
|------|------|------|
| **L1 框架不完整** | 缺少验证、纠错机制 | 生成错误的 SQL 无法自动修正 |
| **L2 Agent 不稳定** | 后续 Agent 未正确使用前序输出 | 信息丢失、幻觉累积 |
| **L3 指令设计不足** | 指令质量影响 SQL 质量 | 小改动导致大差异 |

---

## 二、核心方法

### 2.1 四模块 + 一致性对齐架构

```
┌──────────────────────────────────────────────────────┐
│                  OpenSearch-SQL                      │
├──────────────┬──────────────┬──────────┬────────────┤
│Preprocessing │  Extraction  │Generation│ Refinement │
│    预处理     │    提取       │   生成    │    精炼    │
└──────────────┴──────────────┴──────────┴────────────┘
                    ↕ Alignment (一致性对齐)
```

**模块功能**：

| 模块 | 输入 | 输出 | 核心任务 |
|------|------|------|---------|
| **Preprocessing** | 数据库 + 训练集 | 向量库 + Schema + Few-shot | 构建辅助信息 |
| **Extraction** | NLQ + Schema | 列 + 值 + 实体 | 提取必要元素 |
| **Generation** | 提取信息 + Few-shot | 候选 SQL 集合 | SQL 生成 |
| **Refinement** | 候选 SQL | 最终 SQL | 纠错 + 投票选择 |

---

### 2.2 核心创新一：Self-Taught Few-shot

**传统方法**：Query-SQL 配对

```
/* Answer the following: {question} */
#SQL: {SQL}
```

**问题**：缺少推理过程，LLM 只能看到最终答案

**OpenSearch-SQL 方法**：Query-CoT-SQL 配对

```
/* Answer the following: {question} */
#reason: 分析如何生成 SQL
#columns: 最终使用的列
#values: SQL 中的过滤条件
#SELECT: SELECT 内容
#SQL-like: 忽略 JOIN 的简化 SQL
#SQL: {SQL}
```

**优势**：
- 提供推理过程，帮助 LLM 理解
- 增强生成稳定性
- 便于错误定位

---

### 2.3 核心创新二：SQL-Like 中间语言

**定义**：忽略特定语法元素的简化 SQL

**目的**：
- 让 LLM 专注于 SQL 逻辑而非格式
- 降低生成复杂度

**示例**：

```sql
-- SQL-Like（简化版）
Show COUNT(DISTINCT Patient.ID)
WHERE IGA > 80 AND IGA < 500

-- 完整 SQL
SELECT COUNT(DISTINCT T1.ID) FROM Patient AS T1
INNER JOIN Laboratory AS T2 ON T1.ID = T2.ID
WHERE T2.IGA > 80 AND T2.IGA < 500
```

---

### 2.4 核心创新三：一致性对齐机制 ⭐ 重点

**核心思想**：

> 类似残差连接，在每个模块输出后进行对齐，减少幻觉在链式传递中的累积

**三种对齐类型详解**：

#### (1) Agent Alignment（代理对齐）

**目的**：确保 SQL 中的列和值与数据库一致

**问题场景**：
```sql
-- 原始 SQL（错误）
SELECT ID FROM table WHERE table.name = 'John'

-- 数据库实际存储
table.name 的值是 'JOHN'（大写）

-- 对齐后 SQL（正确）
SELECT ID FROM table WHERE table.name = 'JOHN'
```

**实现方式**：
1. 检查 SQL 中的值与数据库实际值是否匹配
2. 如果不匹配，自动修正
3. 处理大小写、格式等差异

#### (2) Function Alignment（函数对齐）

**目的**：标准化 SQL 聚合函数，防止错误表达式

**问题场景**：
```sql
-- 原始 SQL（错误）
SELECT ID FROM table ORDER BY MAX(score)

-- 问题：ORDER BY 中直接用 MAX 不合理

-- 对齐后 SQL（正确）
SELECT ID FROM table GROUP BY ID ORDER BY score
```

**实现方式**：
1. 检查聚合函数使用是否正确
2. 处理不恰当的 AGG 函数嵌套
3. 消除冗余 JOIN

#### (3) Style Alignment（风格对齐）

**目的**：处理数据集特性相关问题

**问题场景**：
```sql
-- 原始 SQL（可能漏数据）
SELECT ID FROM table ORDER BY score DESC LIMIT 1

-- 问题：如果 score 有 NULL 值，可能返回错误结果

-- 对齐后 SQL（正确）
SELECT ID FROM table WHERE score IS NOT NULL ORDER BY score DESC LIMIT 1
```

**实现方式**：
1. 根据数据集特性添加必要条件
2. 处理 MAX vs LIMIT 1 的选择
3. 添加 IS NOT NULL 等条件

---

#### Info Alignment（信息对齐）

**在 Extraction 阶段使用**：

**目的**：确保 SELECT 内容与 NLQ 一一对应

**实现方式**：
1. 从 NLQ 中提取与 SELECT 内容对应的短语
2. 确保 SELECT 内容的数量和顺序符合预期
3. 处理不同表中同名列的冲突

**示例**：
```
NLQ: "How many patients with a normal Ig A level came to the hospital after 1990?"

Info Alignment 输出:
SELECT content: [How many patients] → COUNT(DISTINCT Patient.ID)
```

---

### 2.5 核心创新四：Self-Consistency & Vote

**投票策略**：
```sql
SELECT SQL FROM K 
WHERE ans = (SELECT ans FROM K GROUP BY ans 
             ORDER BY COUNT(*) DESC LIMIT 1)
ORDER BY t LIMIT 1
```

**选择标准**：
1. 执行结果一致性最高
2. 执行时间最短

---

## 三、实验结果

### 3.1 BIRD 基准性能

| 方法 | Dev EX | Test EX | Test R-VES |
|------|--------|---------|------------|
| GPT-4 | 46.35% | 54.89% | 51.57% |
| DAIL-SQL | 54.76% | 57.41% | 54.02% |
| MCS-SQL | 63.36% | 65.45% | 61.23% |
| CHESS | 65.00% | 66.69% | 62.77% |
| Distillery (SFT) | 67.21% | 71.83% | 67.41% |
| **OpenSearch-SQL** | **69.30%** | **72.28%** | **69.36%** |

> 🏆 **三项指标均为第一**

### 3.2 消融实验

| 配置 | EX | 说明 |
|------|-----|------|
| Full pipeline | 70.6% | 完整系统 |
| w/o Few-shot | 66.0% | -4.6% |
| w/o CoT | 69.2% | -1.4% |
| w/o Alignment | 69.6% | -1.0% |
| w/o Self-Consistency | 68.2% | -2.4% |

---

## 四、对 DA-CaseBase 的启示

### 4.1 动态 Few-shot 可用于案例检索

**方法借鉴**：

```
输入: 用户查询 Q
输出: 最相关的 K 个案例

步骤:
1. 使用 Masked Question Similarity (MQs) 计算相似度
2. 检索 Top-K 相似查询
3. 生成 Query-CoT-SQL 格式的 Few-shot
```

**与案例检索的关系**：

| OpenSearch-SQL | DA-CaseBase |
|----------------|-------------|
| Few-shot 示例选择 | 案例检索 |
| 相似度计算 | 语义相似度匹配 |
| Query-CoT-SQL 格式 | 案例模板表示 |

### 4.2 一致性对齐可用于 SQL 验证

**应用场景**：
- **Agent Alignment**: 案例检索后验证列/值与数据库一致性
- **Function Alignment**: SQL 模板匹配后检查函数使用正确性
- **Style Alignment**: 生成结果后对齐业务规则（如 IS NOT NULL）

### 4.3 Self-Consistency 可用于结果选择

**应用场景**：
- 多个案例生成多个候选 SQL
- 执行验证
- 一致性投票选择最优

---

## 五、关键论文信息速查

| 指标 | OpenSearch-SQL |
|------|---------------|
| 发表会议 | SIGMOD 2025 |
| BIRD Dev EX | 69.30% |
| BIRD Test EX | 72.28% |
| 关键技术 | 动态 Few-shot + 一致性对齐 |
| 代码开源 | 是 |

---

*解读完成：2026-03-18*
*字数：约 4000 字*