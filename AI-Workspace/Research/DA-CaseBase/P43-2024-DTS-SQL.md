# P43 - DTS-SQL: Decomposed Text-to-SQL with Small Large Language Models

> 深度解读 | 更新时间：2026-04-17

---

## 基本信息

| 项目 | 内容 |
|------|------|
| **论文标题** | DTS-SQL: Decomposed Text-to-SQL with Small Large Language Models |
| **作者** | Mohammadreza Pourreza, Davood Rafiei |
| **机构** | University of Alberta |
| **发表时间** | 2024年2月（arXiv: 2402.01117v1） |
| **会议/期刊** | arXiv preprint（格式遵循 ACL 风格） |
| **关键词** | Text-to-SQL, 任务分解, 小模型微调, Schema Linking, 开源LLM |

---

## 核心问题

### 解决什么问题？

**开源小模型（7B参数）在 Text-to-SQL 任务上远落后于闭源大模型（GPT-4）。**

具体表现：
- Llama2-7B 微调后在 Spider dev 上仅 66.7% EX，而 DAIL-SQL+GPT4 达 84.4% EX
- 性能差距达 **~18个百分点**，直接微调无法弥合
- 小模型需要同时学会两件事：(1) 识别相关表/列（Schema Linking）；(2) 生成正确SQL——两个目标耦合导致学习困难

### 为什么重要？

1. **数据隐私**：企业无法将客户数据发送给 GPT-4 等闭源模型的API
2. **部署成本**：小企业难以承受持续调用大模型API的费用
3. **本地部署需求**：7B 模型可在单卡上运行，适合私有化部署
4. **方法论启示**：证明了"任务分解 → 专项微调"是缩小大小模型差距的有效范式

---

## 技术方法

### 整体架构：两阶段分解微调（Decomposed Supervised Fine-tuning）

```
┌─────────────────────────────────────────────────────────┐
│                    DTS-SQL Pipeline                       │
│                                                           │
│  输入: NL Question + Full Database Schema                 │
│           │                                               │
│           ▼                                               │
│  ┌─────────────────────┐                                 │
│  │ Stage 1: Schema     │  Model_1 (7B LLM)              │
│  │ Linking Fine-tuning │  输出: 相关 Tables + Columns     │
│  └────────┬────────────┘                                 │
│           │ 筛选后的 Schema                               │
│           ▼                                               │
│  ┌─────────────────────┐                                 │
│  │ Stage 2: SQL        │  Model_2 (7B LLM)              │
│  │ Generation FT       │  输出: SQL Query                │
│  └─────────────────────┘                                 │
│                                                           │
└─────────────────────────────────────────────────────────┘
```

**核心思想**：将一个复杂的双目标任务拆分为两个单目标子任务，每个子任务由一个专门微调的 7B 模型负责。

### Stage 1: Schema Linking 微调

**目标**：给定 NL 问题和完整数据库 Schema，识别生成 SQL 所需的相关表和列。

**训练数据构造**：
- 从原始训练集 T = {(qᵢ, sᵢ, Dᵢ)} 中提取 SQL 中实际使用的表和列
- 构造新数据集 T' = {(qᵢ, Tᵢ, Cᵢ, Dᵢ)}，其中 Tᵢ 和 Cᵢ 分别是表名列表和列名列表

**损失函数**：

```
min 1/|T| Σ L(M*(σs(qᵢ, Tᵢ, Cᵢ, Dᵢ)))
```

L 为 next-token prediction loss，模型学习预测正确的表名和列名序列。

**Schema Linking 性能**（Table 5）：

| 模型 | 数据集 | Exact Match | Precision | Recall |
|------|--------|-------------|-----------|--------|
| DeepSeek 7B | Spider | 93.1% | 98.4% | 97.7% |
| Mistral 7B | Spider | 91.1% | 97.5% | 97.8% |
| DeepSeek 7B | Spider-SYN | 87.6% | 94.6% | 94.7% |
| Mistral 7B | Spider-SYN | 85.3% | 91.2% | 90.5% |

### Stage 2: SQL Generation 微调

**目标**：给定 NL 问题和 **筛选后的相关表 Schema**（而非全部表），生成 SQL。

**关键差异**：
- 传统方法：输入 = 问题 + **所有表** → 模型需同时做 schema linking + SQL生成
- DTS-SQL：输入 = 问题 + **相关表** → 模型只需专注 SQL 生成

**损失函数**：

```
min 1/|T| Σ L(M*(σg(qᵢ, Tᵢ, sᵢ)))
```

训练时使用 ground truth 的相关表；推理时使用 Stage 1 模型预测的相关表。

### Prompt 设计

论文使用标准化的 prompt 格式（遵循 Gao et al., 2023），包含：
- 外键约束信息
- 主键信息
- 列类型信息
- **3行示例数据**（帮助模型理解数据存储方式）

### 训练细节

| 配置 | 值 |
|------|-----|
| GPU | Nvidia Tesla A100 |
| Batch Size | 64 / 32 |
| Learning Rate | 1e-5 / 5e-5 |
| 加速技术 | Flash Attention 1 & 2 |
| 模型 | DeepSeek 7B, Mistral 7B |

---

## 实验结果

### 数据集

| 数据集 | 特点 |
|--------|------|
| **Spider** | 200个数据库Schema，160训练+40测试，跨域 |
| **Spider-SYN** | Spider的同义词替换版本，去除了NLQ与Schema的显式对应词 |

### 评估指标

- **EX (Execution Accuracy)**：生成SQL与参考SQL在多个数据库实例上执行结果一致
- **EM (Exact Set Match)**：比较SQL各子句（SELECT/WHERE/HAVING/GROUP BY/ORDER BY）的列和谓词匹配

### Spider Test Set 结果（Table 2）

| 方法 | EX | EM |
|------|-----|-----|
| DAIL-SQL + GPT-4 | 86.6 | - |
| DIN-SQL + GPT-4 | 85.3 | 60 |
| **DTS-SQL + DeepSeek 7B** | **84.4** | **73.7** |
| C3 + ChatGPT | 82.3 | - |
| RESDSQL-3B + NatSQL | 79.9 | 72 |
| DIN-SQL + CodeX | 78.2 | 57 |
| DTS-SQL + Mistral 7B | 77.1 | 69.3 |
| Graphix-3B + PICARD | - | 74 |

**亮点**：DTS-SQL + DeepSeek 7B（84.4% EX）几乎追平 DAIL-SQL + GPT-4（86.6% EX），差距仅 2.2%。

### Spider Dev Set 结果（Table 3 & 4）

| 方法 | EX | EM |
|------|-----|-----|
| **DTS-SQL + DeepSeek 7B** | **85.5** | **79.1** |
| DAIL-SQL + GPT-4 | 84.4 | 74.4 |
| C3 + GPT-3.5 | 81.8 | - |
| DTS-SQL + Mistral 7B | 78.6 | 73.3 |
| DIN-SQL + GPT-4 | 74.2 | 60.1 |

**亮点**：在 Spider dev set 上，DTS-SQL + DeepSeek 7B **超过了 DAIL-SQL + GPT-4**，达到 SOTA。

### 分解效果验证（消融实验，Table 3）

| 模型 | 方式 | EX | EM |
|------|------|-----|-----|
| DeepSeek 7B | 全表微调（基线） | 82.1 | 69.0 |
| DeepSeek 7B | **DTS-SQL** | **85.5** | **79.1** |
| DeepSeek 7B | 上界（完美Schema Linking） | 90.3 | 84.2 |
| Mistral 7B | 全表微调（基线） | 71.9 | 70.9 |
| Mistral 7B | **DTS-SQL** | **78.6** | **73.3** |
| Mistral 7B | 上界（完美Schema Linking） | 86.6 | 80.7 |

**关键发现**：
- 分解带来 **+3.4%（DeepSeek）到 +6.7%（Mistral）** 的 EX 提升
- 上界与实际性能差距 4.8-8.0%，说明 Schema Linking 仍有很大改进空间
- Mistral 从分解中获益更大（+6.7% vs +3.4%），说明越弱的模型越受益于任务分解

### Spider-SYN 结果（Table 6）

| 模型 | 方式 | EX | EM |
|------|------|-----|-----|
| DeepSeek 7B | 全表微调 | 70.4 | 56.6 |
| DeepSeek 7B | **DTS-SQL** | **76.2** | **68.9** |
| Mistral 7B | 全表微调 | 67.0 | 63.9 |
| Mistral 7B | **DTS-SQL** | **71.1** | **64.6** |

同义词替换场景下提升更大（+4.1~5.8%），验证了方法的跨场景泛化能力。

---

## 与 DA-CaseBase 的关系

> ⚠️ DA-CaseBase 研究的是案例库理论（Case-Base Theory），而非 NL2SQL 方法本身。以下从案例库角度分析本论文的价值。

### 1. 可借鉴点

#### (a) 任务分解思想 → 案例库的分层匹配架构

DTS-SQL 的核心贡献是将 NL2SQL 分解为 Schema Linking + SQL Generation 两阶段。这对案例库设计有直接启发：

- **案例检索也可以分解**：先做"结构匹配"（Schema/表结构层面找相似案例），再做"语义匹配"（在候选集内做精细SQL语义对比）
- **分层案例库**：第一层索引案例的 Schema 特征（涉及哪些表、列、关系），第二层索引 SQL 结构特征（JOIN 模式、聚合类型等）
- DTS-SQL 证明了分解能让小模型获得大幅提升——案例库的分层匹配同样可能让检索更精准

#### (b) Schema Linking 作为案例表示的关键维度

论文详细分析了 Schema Linking 的精度（93.1% EM, 98.4% Precision, 97.7% Recall），这揭示了一个重要信息：

- **Schema 特征是案例的高质量索引**：一个 NL2SQL 案例可以用"涉及的表+列"作为结构化索引
- 高 Precision/Recall 说明 Schema 特征有很强的区分性，适合作为案例库的一级检索键
- **案例表示公式**：Case = (NL_Query, Schema_Signature, SQL_Template, Execution_Context)

#### (c) 上界分析 → 案例库质量的量化框架

Table 3 的上界分析方法非常值得借鉴：
- 完美 Schema Linking → 90.3% EX vs 实际 85.5% EX，差距 4.8%
- **类比到案例库**：可以用"完美案例检索"（Oracle）作为上界，量化案例库质量对最终性能的贡献
- 这提供了一种评估案例库效果的实验范式

#### (d) Prompt 中的示例行 → 案例库中的数据快照

DTS-SQL 在 prompt 中包含 3 行示例数据来帮助模型理解数据存储方式。这启示：
- 案例不仅包含 (Question, SQL) 对，还应包含**数据快照**（sample rows）
- 数据快照能帮助 LLM 理解列的语义、数据格式、值域范围
- **丰富案例表示**：Case = (NL, SQL, Schema, Sample_Rows, Metadata)

### 2. 本研究的切入点

#### (a) DTS-SQL 缺少案例复用机制 → DA-CaseBase 的价值所在

DTS-SQL 的局限性恰好是案例库的机会：
- DTS-SQL 每次推理都从零开始做 Schema Linking + SQL Generation
- **没有利用历史成功案例**：如果用户曾问过类似问题，之前的 Schema Linking 结果和 SQL 模板可以直接复用
- DA-CaseBase 可以为 DTS-SQL 的每个阶段提供案例支持：
  - Stage 1: 用历史案例指导 Schema Linking（"类似问题通常涉及哪些表"）
  - Stage 2: 用历史案例提供 SQL 模板（"类似问题的 SQL 结构是什么"）

#### (b) Schema Linking 的 ~7% 误差 → 案例库纠错的空间

论文承认 Schema Linking 只有约 90% 的准确率，是性能瓶颈。案例库可以：
- 提供**校验机制**：检索相似案例的 Schema 特征，与模型预测做交叉验证
- 提供**补全机制**：如果模型遗漏了某个表，但历史案例显示类似问题需要该表，则补充

#### (c) 跨域泛化的挑战 → 案例库的迁移学习价值

Spider-SYN 的结果下降（85.5% → 76.2%）说明同义词替换就能显著影响性能。案例库可以：
- 存储**同义词映射关系**作为案例的一部分
- 在新领域中，通过少量案例快速适配（few-shot case adaptation）
- 案例库的跨域迁移比模型重新微调更轻量

### 3. 案例库理论视角的总结

| 案例库维度 | DTS-SQL 提供的启示 |
|-----------|-------------------|
| **案例定义** | Case = (NL, Schema_Signature, SQL, Sample_Rows) |
| **案例表示** | 分层表示：Schema 特征层 + SQL 结构层 |
| **案例匹配** | 分解匹配：先 Schema 匹配，再 SQL 语义匹配 |
| **案例应用** | 两阶段应用：案例辅助 Schema Linking → 案例辅助 SQL Generation |
| **案例库评估** | 上界分析法：完美检索 vs 实际检索的性能差距 |

### 4. DA-CaseBase 可进一步研究的问题

1. **案例索引设计**：如何用 Schema 特征构建高效的案例索引？
2. **分层案例匹配**：DTS-SQL 的两阶段分解能否映射为案例库的两级匹配？
3. **案例库对 Schema Linking 的增强**：案例库能否将 90% → 95%+ 的 Schema Linking 准确率？
4. **案例库规模与性能的关系**：多少案例足以覆盖一个领域的 NL2SQL 需求？

---

## 论文优缺点总结

### 优点
- ✅ 方法简洁有效：仅靠任务分解就获得 3-7% 的稳定提升
- ✅ 实验充分：两个数据集 × 两个模型 × 消融实验 + 上界分析
- ✅ 实际意义明确：解决了隐私和成本问题
- ✅ 可复现：代码和预测结果已开源

### 缺点
- ❌ 仅在 Spider 系列数据集评测，缺少 BIRD 等更难的 benchmark
- ❌ 两个 7B 模型的总参数量为 14B，推理需两次前向传播，效率待讨论
- ❌ Schema Linking 模型的错误会级联传播到 SQL Generation，缺少纠错机制
- ❌ 没有探索 few-shot / 案例检索等增强 Schema Linking 的方法
- ❌ 未讨论对 complex SQL（嵌套、多表 JOIN）的具体表现

---

*解读完成于 2026-04-17 | 科研小新*
