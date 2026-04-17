# P40: MAC-SQL — Multi-Agent Collaborative Framework for Text-to-SQL

> 解读时间：2026-04-17
> 解读人：科研小新

---

## 基本信息

| 项目 | 内容 |
|------|------|
| **论文标题** | MAC-SQL: A Multi-Agent Collaborative Framework for Text-to-SQL |
| **arXiv** | 2312.11242v6 |
| **发表会议** | ACL 2025 |
| **提交时间** | 2023-12（初版），2025-03-18（v6最终版） |
| **作者团队** | Bing Wang, Changyu Ren, Jian Yang, Xinnian Liang, Jiaqi Bai, Linzheng Chai (北京航空航天大学); Zhao Yan, Qian-Wen Zhang, Di Yin, Xing Sun (腾讯优图实验室) |
| **通讯作者** | Zhoujun Li†（北航） |
| **代码** | https://github.com/wbbeyourself/MAC-SQL |
| **基金** | 国家自然科学基金 (62276017, 62406033 等) |

---

## 核心问题

### 解决什么问题

1. **大型数据库性能退化**：现有 LLM-based Text-to-SQL 方法在面对包含大量表和列的"huge"数据库时，性能显著下降。过多的 schema 信息引入噪声，导致 LLM 生成的 SQL 包含不相关的表/列。
2. **复杂多步推理不足**：需要多步推理的复杂用户问题（如涉及子查询、聚合嵌套等），单次生成难以正确处理。
3. **缺乏工具使用和模型协作**：现有方法大多忽略了 LLM 使用外部工具（如 SQL 执行器）进行自我纠错的能力，也缺少多模型/多角色的协作机制。

### 为什么重要

- Text-to-SQL 是让非技术用户通过自然语言访问数据库的关键技术，实际应用中数据库往往规模庞大（BIRD 数据集存储量达 33.4GB，95 个大规模数据库）
- 复杂查询占实际场景的显著比例，单 Agent 方法难以稳健处理
- 多 Agent 协作范式在软件开发（ChatDev）等领域已证明有效，但在 Text-to-SQL 中尚属空白

---

## 技术方法

### 核心架构：三 Agent 协作框架

MAC-SQL 由三个功能互补的 Agent 组成，按需协作：

```
用户问题 + 数据库 Schema + 外部知识
        │
        ▼
   ┌─────────────┐
   │  Selector    │  ← 大数据库时激活，精简 Schema
   │  (辅助Agent) │
   └──────┬──────┘
          │ 精简后的 Sub-Database
          ▼
   ┌─────────────┐
   │ Decomposer  │  ← 核心Agent，问题分解 + SQL生成
   │  (核心Agent) │     Few-shot CoT 推理
   └──────┬──────┘
          │ 生成的 SQL
          ▼
   ┌─────────────┐
   │  Refiner     │  ← SQL 执行反馈 + 自动纠错
   │  (辅助Agent) │     最多 3 轮修正
   └──────┬──────┘
          │
          ▼
      最终 SQL
```

### 三个 Agent 的详细设计

#### 1. Selector Agent（数据库精简器）

**触发条件**：仅在数据库 schema 超过 token 阈值时激活（如 GPT-4-32k 时 len(tokens) > 25k）

**功能**：
- 输入：完整数据库 Schema S = {T, C}、用户问题 Q、外部知识 K
- 输出：最小子集 Schema S' = {T', C'}，其中 T' ⊆ T, C' ⊆ C
- 形式化：S' = f_selector(Q, S, K | M)

**实现细节**：
- 角色设定为"经验丰富的数据库管理员"
- 对每个表判断：≤10列的表标记 `keep_all`；完全不相关的表标记 `drop_all`
- 相关表保留 Top 6 最相关列（按相关性排序）
- 确保至少保留 3 个表
- 每个表至少保留 6 列名，防止遗漏
- 提供 1-shot 示例引导 JSON 格式输出

#### 2. Decomposer Agent（问题分解器）— 核心 Agent

**功能**：将复杂问题分解为渐进式子问题，通过 Chain-of-Thought (CoT) 推理逐步生成 SQL

**推理方式选择**：
- ❌ Least-to-Most：迭代生成每个子SQL，计算成本高，需确定停止条件
- ✅ Chain-of-Thought：单次生成所有子问题和对应 SQL，效率更高

**关键设计**：
- **动态难度判断**：简单问题直接生成 SQL，复杂问题分解为 1-5 个子问题
- **渐进式推理**：从最简子问题开始，逐步构建，最后一个子SQL即为最终答案
- **Few-shot 学习**：使用最多 2 个示例（2-shot 效果最佳）
- 形式化：P_M(Y|Q, S', K) = ∏_{j=1}^{L} P_M(Y_j | Y_{<j}, Q_j, S', K)

**Prompt 设计要点**：
- 包含约束条件（SELECT 只选必要列、避免不必要 JOIN、处理 NULL 值等）
- Schema 包含：数据库 ID、表名、列名、列描述、高频 cell 值
- 高频 cell 值过滤：忽略纯数值列、URL/邮件等非常规值

#### 3. Refiner Agent（SQL 修正器）

**功能**：执行生成的 SQL，检测并自动修正错误

**诊断维度**：
- 语法正确性（syntax errors）
- 执行可行性（能否成功执行）
- 结果非空性（是否返回非空结果）

**修正流程**：
- 最多 3 轮修正（实际效率考量）
- 输入：错误 SQL Y'、错误信息 E、原始问题 Q、Schema S'、知识 K
- 输出：修正后的 SQL Y
- 形式化：Y = f_refiner(E, Y', Q, S', K | M)

**当前限制**：
- 如果 SQL 执行无报错且结果非空，即使语义不正确，Refiner 也不会进一步修正
- 无法处理"语义正确但逻辑错误"的 ambiguous 情况

### 整体算法流程（Algorithm 1）

```python
def MAC_SQL(question, database, knowledge):
    # Step 1: 条件激活 Selector
    if need_simplify(database):
        database = LLM_Selector(question, database, knowledge)
    
    # Step 2: Decomposer 生成 SQL
    db_desc = get_db_representation(database, knowledge)
    sub_questions, sub_sqls = LLM_Decomposer(question, db_desc)
    sql = sub_sqls[-1]  # 最后一个子SQL即最终SQL
    
    # Step 3: Refiner 迭代修正（最多3次）
    for _ in range(max_try_times):
        ok, error = execute_and_analyze(sql, database)
        if ok:
            return sql
        sql = LLM_Refiner(question, db_desc, sql, error)
    
    return sql
```

### SQL-Llama：开源替代模型

**目标**：用开源模型替代 GPT-4 完成所有 Agent 任务

**基础模型**：Code Llama 7B

**训练数据**：Agent-Instruct 数据集
- 使用 GPT-4 在 BIRD + Spider 训练集上通过 MAC-SQL 框架生成指令数据
- 按难度级别收集，过滤掉 SQL 输出错误的样本
- 最终包含 10,000 条高质量指令数据
- 覆盖 3 个 Agent 任务：数据库精简、问题分解+SQL生成、SQL纠错

**训练方式**：Multi-task Supervised Fine-tuning
- 损失函数：L = -∑_{i=1}^{N} E[log P(Y_i | Q, S_i, K; M)]
- 同时学习 3 个 Agent 任务

---

## 实验结果

### 数据集

| 数据集 | 特点 | 规模 |
|--------|------|------|
| **BIRD** | 真实大规模数据库，95 个数据库，33.4GB 存储，37 个专业领域 | 训练集 + 1,534 dev + holdout test |
| **Spider** | 跨域多数据库基准，200 个数据库，138 个领域 | 7,000 训练 + 1,034 dev + test |

### 评估指标

- **EX (Execution Accuracy)**：预测SQL执行结果与Gold SQL执行结果一致的比例
- **EM (Exact Match)**：预测SQL各子句与Gold SQL完全匹配
- **VES (Valid Efficiency Score)**：衡量有效SQL的执行效率

### BIRD 数据集结果

| 方法 | Dev EX | Dev VES | Test EX | Test VES |
|------|--------|---------|---------|----------|
| Palm-2 | 27.38 | — | 33.04 | — |
| ChatGPT + CoT | 36.64 | 42.30 | 40.08 | 56.56 |
| Claude-2 | 42.70 | 49.77 | 49.02 | 60.77 |
| GPT-4 (vanilla) | 46.35 | 58.79 | 54.89 | 59.44 |
| DIN-SQL + GPT-4 | 50.72 | 56.08 | 55.90 | 61.95 |
| DAIL-SQL + GPT-4 | 54.76 | — | 57.41 | — |
| **MAC-SQL + GPT-4** | **59.39** | **66.39** | **59.59** | **67.68** |
| SQL-Llama (7B) | 32.87 | — | — | — |
| MAC-SQL + SQL-Llama | 43.94 | 55.67 | — | — |
| MAC-SQL + GPT-3.5 | 50.56 | 57.36 | — | — |

**关键发现**：
- MAC-SQL+GPT-4 在 BIRD test set 达到 **59.59% EX**，SOTA
- 比第二名 DAIL-SQL+GPT-4 高 2.18%（test）和 4.63%（dev）
- SQL-Llama (7B) 达到 43.94%，接近 vanilla GPT-4 的 46.35%（参数量差距巨大）

### Spider 数据集结果

| 方法 | Dev EX | Test EX |
|------|--------|---------|
| C3 + ChatGPT | 81.80 | 82.30 |
| DIN-SQL + GPT-4 | 82.80 | 85.30 |
| DAIL-SQL + GPT-4 | 84.40 | 86.60 |
| **MAC-SQL + GPT-4** | **86.75** | 82.80 |
| MAC-SQL + SQL-Llama | 76.25 | 70.58 |

**注意**：Spider dev set 上 MAC-SQL+GPT-4 最高（86.75%），但 test set 表现有差距（82.80% vs DAIL-SQL 的 86.60%），可能存在泛化问题。

### 消融实验（BIRD Dev Set）

| 配置 | Simple | Moderate | Challenging | All |
|------|--------|----------|-------------|-----|
| **MAC-SQL+GPT-4（完整）** | **65.73** | **52.69** | **40.28** | **59.39** |
| w/o Selector | 65.73 | 52.04 | 35.14 | 57.28 (-2.11) |
| w/o Decomposer | 61.51 | 48.82 | 38.89 | 55.54 (-3.85) |
| w/o Refiner | 63.24 | 44.52 | 33.33 | 54.76 (-4.63) |

**各组件贡献排序**：Refiner (+4.63) > Decomposer (+3.85) > Selector (+2.11)

**关键洞察**：
- **Refiner 贡献最大**（+4.63%），说明执行反馈纠错机制非常有效
- **Selector 主要影响 Challenging 题**（+5.14%），对 Simple 题无影响——符合预期，大schema时精简更关键
- **Decomposer 全面提升**，各难度级别均有贡献

### Few-shot 影响

| Few-shot | BIRD EX | BIRD VES | Spider EM | Spider EX |
|----------|---------|----------|-----------|-----------|
| 0-shot | 55.54 | 63.31 | 58.42 | 74.22 |
| 1-shot | 57.26 | 64.32 | 59.68 | 78.35 |
| 2-shot | 59.39 | 66.24 | 63.20 | 86.75 |

2-shot 效果最佳，但受限于 GPT-4 API 成本（BIRD dev 完整测试约 1000 万 tokens）未测试更多 shots。

### 错误分析（BIRD）

| 错误类型 | BIRD 占比 | Spider 占比 | 说明 |
|----------|-----------|-------------|------|
| Gold Error | 30% | 22% | 标注错误（最大来源！） |
| Semantic Correct | 14% | 22% | 语义正确但形式不匹配 |
| Schema Linking Error | 2% | 8% | Schema 链接错误 |
| Database Misunderstand | — | — | 数据库结构理解错误 |
| Question Misunderstand | — | — | 问题理解错误 |
| Evidence Misunderstand | — | — | 证据/知识理解错误 |
| Wrong Schema Linking | — | — | 表连接顺序错误 |
| Dirty Database Values | — | — | 数据库值不一致 |

**重要发现**：Gold Error（标注错误）是最大的错误来源，占 BIRD 30%、Spider 22%。

---

## 技术亮点与创新点

1. **按需激活的模块化设计**：Selector 仅在大数据库时激活，避免不必要的处理开销
2. **CoT vs Least-to-Most 的工程权衡**：选择 CoT 单次生成所有子问题，兼顾效率和效果
3. **执行反馈闭环**：Refiner 利用实际 SQL 执行结果（而非仅静态分析）进行纠错
4. **开源蒸馏**：通过 Agent-Instruct 数据集将 GPT-4 能力蒸馏到 7B 模型，实现可部署的轻量方案
5. **可扩展框架**：Agent 可按需扩展新功能/工具

---

## 局限性

1. **Refiner 的语义盲区**：SQL 执行无报错且结果非空时，即使语义错误也不修正
2. **Prompt 未充分优化**：作者承认 Agent Prompt 可能不是最优
3. **Spider test set 表现一般**：dev 上最优但 test 上低于 DAIL-SQL，泛化性存疑
4. **仅测试到 2-shot**：受限于 API 成本，未探索更多 demonstrations 的上限
5. **SQL-Llama 规模受限**：仅基于 7B 模型，更大模型可能效果更好

---

## 与本研究（DA-CaseBase）的关系

### 可借鉴的技术点

1. **多 Agent 协作架构**：MAC-SQL 的 Selector-Decomposer-Refiner 三 Agent 模式可直接迁移到 DA-CaseBase。案例库系统同样需要：
   - **检索 Agent**（类 Selector）：从案例库中检索相关案例
   - **生成 Agent**（类 Decomposer）：基于检索到的案例生成/改写 SQL
   - **验证 Agent**（类 Refiner）：执行验证 + 质量评估

2. **执行反馈纠错机制**：Refiner 的设计（执行SQL → 获取错误反馈 → 修正）可用于 DA-CaseBase 的**自动质量验证**。新沉淀的案例可通过执行验证确保语法和执行正确性。

3. **Schema 精简策略**：Selector 的数据库精简方法可用于案例检索时的 Schema 匹配——根据用户问题精简 Schema 后再进行案例匹配，提高检索精度。

4. **Agent-Instruct 数据构造方法**：通过强模型（GPT-4）生成指令数据、过滤错误样本、按难度采样的方法，可用于构造案例库的初始高质量案例集。

5. **消融实验设计**：三组件逐一移除的消融方法，可参考设计 DA-CaseBase 各模块（案例检索、质量过滤、自维护）的消融实验。

### 本研究的切入点

1. **MAC-SQL 缺乏案例积累机制**：MAC-SQL 的 Few-shot 示例是静态的（手工编写的 2 个 demonstrations），不会从历史交互中学习和积累。DA-CaseBase 的核心价值正是**自动将成功交互沉淀为案例**，使系统随使用持续进化。

2. **Refiner 的局限是 DA-CaseBase 的机会**：MAC-SQL 的 Refiner 只做执行级别的纠错，无法检测"执行成功但语义错误"的情况。DA-CaseBase 可通过**案例比对**（与相似历史案例的 SQL 模式对比）检测语义异常，填补这一空白。

3. **静态 vs 动态示例选择**：MAC-SQL 使用固定的 2-shot 示例。DA-CaseBase 可实现**动态案例检索**——根据当前查询的语义相似度从案例库中选择最相关的示例，预期效果优于静态 few-shot。

4. **质量保证的自动化**：MAC-SQL 的 Agent-Instruct 数据需要"过滤掉 SQL 输出错误的样本"（人工/半自动），而 DA-CaseBase 的核心创新正是**无人工审核的自动质量保证机制**。

5. **可与 MAC-SQL 框架集成**：DA-CaseBase 可作为 MAC-SQL 的第四个 Agent（Case Agent），在 Decomposer 生成 SQL 前提供历史案例参考，形成 Selector → Case Agent → Decomposer → Refiner 的增强流水线。

---

## 关键引用

- Wang et al. (2024). MAC-SQL: A Multi-Agent Collaborative Framework for Text-to-SQL. *ACL 2025*. arXiv:2312.11242.
- 相关工作：DIN-SQL (Pourreza & Rafiei, 2023), DAIL-SQL (Gao et al., 2023), C3-SQL (Dong et al., 2023)
- 数据集：BIRD (Li et al., 2023), Spider (Yu et al., 2018)
