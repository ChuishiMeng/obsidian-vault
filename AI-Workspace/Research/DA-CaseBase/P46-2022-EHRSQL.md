# P46 - EHRSQL 2024 Shared Task 深度解读

> ⚠️ 注意：文件编号为 P46，实际 PDF 为 EHRSQL 2024 共享任务概述论文（arXiv:2405.06673v2），而非 NeurIPS 2022 原始数据集论文。原始 EHRSQL 数据集论文（Lee et al., NeurIPS 2022）在本文中被大量引用作为基础。

---

## 基本信息

| 字段 | 内容 |
|------|------|
| **论文标题** | Overview of the EHRSQL 2024 Shared Task on Reliable Text-to-SQL Modeling on Electronic Health Records |
| **作者** | Gyubok Lee, Sunjun Kweon, Seongsu Bae, Edward Choi |
| **机构** | KAIST AI（韩国科学技术院） |
| **发表会议** | 6th Clinical Natural Language Processing Workshop (ClinicalNLP 2024) @ NAACL 2024, Mexico City |
| **时间** | 2024年5月（arXiv v2），会议2024年6月 |
| **ArXiv** | 2405.06673v2 |
| **GitHub** | [glee4810/ehrsql-2024](https://github.com/glee4810/ehrsql-2024) |
| **竞赛平台** | [Codabench](https://www.codabench.org/competitions/1889/) |
| **关键词** | EHR, Text-to-SQL, 可靠性, 拒答机制, MIMIC-IV, 医疗NLP |

### 与原始 EHRSQL 的关系
- **EHRSQL (NeurIPS 2022)**: 原始数据集论文，首次提出医疗 Text-to-SQL 基准，基于 MIMIC-III 和 eICU
- **EHRSQL 2024 Shared Task (本文)**: 基于原始数据集的升级版共享任务，使用 MIMIC-IV Demo，新增更具挑战性的不可回答问题和数据划分策略

---

## 核心问题

### 解决什么问题？

**核心目标**：构建面向电子健康记录（EHR）的**可靠**问答系统，通过 Text-to-SQL 技术让医疗专业人员无需 SQL 技能即可查询患者数据。

**"可靠性"的定义**（本文核心贡献）：
- 不仅要**正确生成 SQL**（提供效用 utility）
- 还要能**拒绝回答**不可回答的问题或可能出错的问题（减少伤害 harm）
- 采用 TrustSQL (Lee et al., 2024) 的可靠性定义

### 为什么重要？

1. **安全关键领域**：医疗场景中错误预测后果严重，一条错误 SQL 可能导致临床决策失误
2. **EHR 利用率低**：医护人员缺乏 SQL 技能，EHR 中的数据价值被严重低估
3. **现有 Text-to-SQL 局限**：大多数基准只关注最大化 SQL 生成准确率，忽略了"知道自己不知道"的能力
4. **真实需求驱动**：数据来源于对 200+ 韩国大学医院医护人员的调研，反映真实临床需求

### 与传统 Text-to-SQL 的关键区别

| 维度 | 传统 Text-to-SQL (Spider等) | EHRSQL 2024 |
|------|---------------------------|-------------|
| 评估目标 | 最大化 SQL 准确率 | 准确率 + 可靠拒答 |
| 不可回答问题 | 不考虑 | 20% 比例，含对抗性问题 |
| 领域特殊性 | 通用 | 医疗领域（时间表达式、专业术语） |
| 数据来源 | 研究人员编写 | 真实医护人员需求调研 |
| SQL 复杂度 | 中等 | 高（涉及时间计算、多表连接） |

---

## 技术方法

### 1. 数据集构建流程

```
200+ 医护人员调研问卷
      ↓
  问题模板提取（去重、翻译）
      ↓
  SQL 标注（链接到 MIMIC-IV Demo Schema）
      ↓
  ChatGPT 生成自然语言改写（人工审核）
      ↓
  值采样（从 MIMIC-IV Demo 数据库采样具体值）
      ↓
  添加不可回答问题（原始 + TrustSQL 对抗性问题）
      ↓
  新数据划分（含 seen/unseen 模板）
```

### 2. 数据集特点

**数据库**: MIMIC-IV Demo（100 名患者，与完整 MIMIC-IV 相同 Schema）
- 优势：无需 CITI 培训即可使用，允许商业 API 调用
- Schema 与完整版相同，SQL 可直接迁移

**数据划分** (关键创新):

| 划分 | 可回答模板 | 可回答样本 | 不可回答样本 | 总计 |
|------|-----------|-----------|-------------|------|
| Train | 100 (seen) | 4,674 | 450 | 5,124 |
| Valid | 134 (100 seen + 34 unseen) | 931 | 232 | 1,163 |
| Test | 134 (100 seen + 34 unseen) | 934 | 233 | 1,167 |

关键设计：验证集和测试集包含**34 个训练集中未出现的问题模板**（unseen SQL structures），模拟真实部署中遇到新类型问题的场景。

**不可回答问题的类型**:
- 原始 EHRSQL 的误收集问题（多样性有限）
- TrustSQL 的对抗性问题：引用不存在的列、超出 SQL 功能的请求
- 新增问题仅出现在验证集和测试集，增加难度

### 3. 评估指标：Reliability Score (RS)

**核心公式** — 五种评分情况：

| 情况 | 条件 | 得分 |
|------|------|------|
| ✅ 正确回答 | 可回答 + 生成SQL + SQL正确 | **+1** |
| ⏸️ 放弃可回答 | 可回答 + 拒答 | **0** |
| ❌ 错误回答 | 可回答 + 生成SQL + SQL错误 | **-c** |
| ❌ 误答不可回答 | 不可回答 + 生成SQL | **-c** |
| ✅ 正确拒答 | 不可回答 + 拒答 | **+1** |

**惩罚参数 c 的含义**:
- `RS(0)`: 无惩罚，等价于传统准确率
- `RS(10)`: **主评估指标**，1 次错误 = 10 次正确的负抵消
- `RS(N)`: 最严格，N = 数据集大小，1 次错误抵消所有正确

**Baseline**: ABSTAIN-ALL（全部拒答）= 20%（因不可回答问题占 20%，全部正确拒答得分）

### 4. 参赛方法分类

#### 统一方法 (Unified Approach) — 5 支队伍

用单一 LLM 同时处理 SQL 生成和拒答判断：

| 队伍 | RS(10) | 核心方法 | 模型 |
|------|--------|---------|------|
| **LG AI Research & KAIST** | **81.32** | 自训练 + 伪标签不可回答问题 | ChatGPT (fine-tuned) |
| PromptMind | 74.89 | 三模型集成 + 一致性投票拒答 | GPT-4 + ChatGPT + Claude Opus |
| ProbGate | 74.21 | log-probability 阈值 + 错误处理 | ChatGPT (fine-tuned) |
| KU-DMIS | 59.21 | 伪问题-SQL对生成 + 多采样一致性拒答 | ChatGPT (fine-tuned) |
| Project PRIMUS | -713.37 | 直接 ICL 生成 SQL 和 null | SQLCoder-7b-2 |

#### 管道方法 (Pipeline Approach) — 3 支队伍

多个专门模型串联：

| 队伍 | RS(10) | 管道结构 | 模型 |
|------|--------|---------|------|
| AIRI NLP | 44.04 | 逻辑回归检测 → T5-3B 生成 → 可执行性检查 | T5-3B + LR |
| LTRC-IIITH | 43.70 | SQLCoder检测 → SQLCoder生成 → log-prob阈值 + 可执行性检查 | SQLCoder-7b-2 |
| Saama Technologies | 36.08 | 集成分类器检测 → CodeLlama生成 → ChatGPT 答案选择 | 多模型集成 |

---

## 实验结果

### 主要结果对比

| 排名 | 队伍 | RS(0) | RS(10) ⭐ | RS(N) | 方法类型 | 集成 | 微调 |
|------|------|-------|---------|-------|---------|------|------|
| 1 | LG AI Research & KAIST | 88.17 | **81.32** | -711.83 | Unified | ❌ | ✅ |
| 2 | PromptMind | 82.60 | 74.89 | -817.40 | Unified | ✅ | ✅ |
| 3 | ProbGate | 81.92 | 74.21 | -818.08 | Unified | ❌ | ✅ |
| 4 | KU-DMIS | 72.07 | 59.21 | -1427.93 | Unified | ❌ | ✅ |
| 5 | AIRI NLP | 68.89 | 44.04 | -2831.11 | Pipeline | ❌ | ✅ |
| 6 | LTRC-IIITH | 66.84 | 43.70 | -2633.16 | Pipeline | ❌ | ✅ |
| 7 | Saama Technologies | 53.21 | 36.08 | -1946.79 | Pipeline | ✅ | ✅ |
| 8 | Project PRIMUS | 14.14 | -713.37 | -84.9K | Unified | ❌ | ❌ |
| - | ABSTAIN-ALL (Baseline) | 20.00 | 20.00 | 20.00 | - | - | - |

### 五大核心发现

1. **同一 LLM，用法不同结果差异巨大**：同样使用 ChatGPT 微调，第1名 (81.32) 和第4名 (59.21) 差距 22 分，关键在于自训练策略
2. **统一方法 > 管道方法**：前4名全是统一方法，但管道方法中使用 LLM 的潜力尚未充分探索
3. **RS 各惩罚级别差距小 = 排名高**：RS(0) 和 RS(10) 差距越小，说明错误预测越少，拒答机制越有效
4. **领域微调至关重要**：无论通用模型还是代码模型，微调后性能显著提升；唯一未微调的队伍 (Project PRIMUS) 排名最低
5. **测试时伪标签微调有效但有局限**：在固定基准上有效，但不适用于实时部署

### 关键失败模式

- **所有队伍 RS(N) 均为负值**：即使最好的方法，在最严格评估下仍然不可靠
- **1 次错误的代价巨大**：RS(N) 中一次错误抵消所有正确预测
- 这意味着**完全可靠的医疗 Text-to-SQL 仍是未解决问题**

---

## 与 DA-CaseBase 的关系

### ⭐ 核心关联：领域术语映射 = 案例定义的核心

EHRSQL 最深刻的启示是：**领域术语到数据库 Schema 的映射是 Text-to-SQL 成败的关键**。这与案例库理论中"案例的定义和表示"高度同构。

#### 1. 术语映射 ↔ 案例索引设计

| EHRSQL 挑战 | 案例库理论对应 | DA-CaseBase 切入点 |
|------------|-------------|-------------------|
| "最近一次处方" → `MAX(starttime) FROM prescriptions` | 案例索引：自然语言意图 → 结构化查询模式 | 案例索引应包含领域术语到 Schema 元素的映射表 |
| "住院时长" → `strftime('%J', dischtime) - strftime('%J', admittime)` | 案例解决方案：复杂计算模板 | 案例解决方案应编码领域特定的计算逻辑 |
| 同一问题在 MIMIC-III 和 eICU 中 SQL 不同 | 案例适配：跨数据库迁移 | 案例表示需要抽象层，支持跨 Schema 复用 |

#### 2. 不可回答检测 ↔ 案例覆盖度分析

EHRSQL 的可靠性要求（"宁可拒答不可答错"）直接映射到案例库的**案例覆盖度**问题：

- **案例库中有匹配案例** → 高置信度回答
- **案例库中无匹配但 Schema 支持** → 尝试推理但需验证
- **Schema 本身不支持** → 确定性拒答
- **问题超出 SQL 能力**（如"长期使用胰岛素的副作用"）→ 确定性拒答

案例库可以显式编码**拒答规则**作为特殊案例类型。

#### 3. 问题模板 ↔ 案例模板

EHRSQL 的数据构建方式为案例库设计提供了直接启示：

```
EHRSQL:  问题模板 + 值采样 → 具体问题-SQL对
                    ↕ 同构
DA-CaseBase: 案例模板 + 参数绑定 → 具体案例实例
```

- **100 个问题模板** 生成 4,674 个训练样本 → 高效的案例复用
- **Seen vs Unseen 模板划分** → 案例库需要处理"案例外推"问题
- 34 个 unseen 模板是性能下降的主要原因 → **案例库的泛化能力是核心挑战**

#### 4. 可借鉴的具体技术

| 技术 | 来源 | 可借鉴点 |
|------|------|---------|
| **自训练 + 伪标签** | 第1名 LG AI Research | 案例库可通过自训练自动扩展"拒答案例" |
| **多模型一致性投票** | 第2名 PromptMind | 案例检索时使用多个相似度度量，一致才采纳 |
| **Log-probability 阈值** | 第3名 ProbGate | 案例匹配置信度阈值设计 |
| **双检索器（通用 + 领域）** | 第2名 PromptMind | 案例索引可设计通用语义索引 + 领域术语索引 |
| **RS 评估指标** | 任务设计 | 案例库系统评估可采用类似的"惩罚错误"指标 |

#### 5. 对案例定义和表示的启示

基于 EHRSQL 的经验，DA-CaseBase 中的"案例"应包含：

```yaml
Case:
  # 案例索引层
  intent: "查询患者最近一次处方"
  domain_terms: ["处方", "最近", "药物"]
  schema_mapping:
    处方: prescriptions
    药物: drug
    最近: MAX(starttime)
  
  # 案例解决方案层
  sql_template: |
    SELECT {target_column} FROM {table}
    WHERE subject_id = {patient_id}
    AND {time_column} = (SELECT MAX({time_column}) FROM {table} WHERE subject_id = {patient_id})
  
  # 案例元信息
  answerability: true
  confidence_threshold: 0.85
  time_expression: true
  complexity: "nested_subquery"
  
  # 适配规则
  adaptation_rules:
    - if_schema_missing: "abstain"
    - if_value_type_mismatch: "cast_or_abstain"
```

#### 6. 关键洞察总结

> **EHRSQL 证明了一个核心论点：在领域 Text-to-SQL 中，"知道什么能回答、什么不能回答"比"生成正确 SQL"更难、也更重要。**
>
> 这恰恰是案例库理论的核心优势所在——案例库天然具有**有限性和边界性**，能够显式表达"我知道的"和"我不知道的"。相比之下，LLM 缺乏这种显式边界，只能通过概率阈值、一致性投票等间接手段实现拒答。
>
> DA-CaseBase 的研究切入点：**将案例库的显式边界性（explicit boundary）作为实现可靠 Text-to-SQL 的核心机制**，而非仅依赖 LLM 的隐式不确定性估计。

---

## 局限性（论文自述）

1. 不能代表医院场景中所有类型的可回答和不可回答问题
2. 仅使用 MIMIC-IV，非通用 EHR Schema
3. 数据库经过预处理（去除跨表重复值以减少歧义），与真实 EHR 有差距
4. 大多数方法高度依赖底层 LLM，新 LLM 的实验验证不足

---

## 参考文献

- Lee, G., et al. (2022). EHRSQL: A Practical Text-to-SQL Benchmark for Electronic Health Records. *NeurIPS 2022 (Datasets and Benchmarks)*
- Lee, G., et al. (2024). TrustSQL: A Reliability Benchmark for Text-to-SQL Models with Diverse Unanswerable Questions. *arXiv:2403.15879*
- Johnson, A., et al. (2020). MIMIC-IV. *PhysioNet*
- Jo, Y., et al. (2024). Self-training LLMs with Pseudo-labeled Unanswerable Questions. *ClinicalNLP 2024*

---

*解读时间: 2026-04-17 | 解读者: 科研小新*
