# P02-2026-CBR-to-SQL - 案例驱动的 Text-to-SQL（深度解读）

> **论文标题**：CBR-to-SQL: Rethinking Retrieval-based Text-to-SQL using Case-based Reasoning in the Healthcare Domain
> **作者**：Aalto University 团队
> **年份**：2026
> **来源**：arXiv:2603.05569
> **相关 RQ**：RQ2 案例模板表示与泛化
> **相关度**：⭐⭐⭐⭐⭐ **（本研究最直接的对比方法）**

---

## 一、研究背景与问题定义

### 1.1 问题场景：医护人员想查数据，但不会写 SQL

**背景**：医院存储了大量电子病历数据，医护人员经常需要查询数据来支持临床决策。

**核心问题**：医护人员不会写 SQL，但需要从数据库获取信息。

**具体场景**：

```
场景：医生想查"有多少糖尿病患者做过MRI检查"

数据库实际情况：
- 数据库有几十张表
- 表名、列名是缩写（如 icd9_code, proc_name）
- 需要关联多张表才能得到答案

医生期望：直接问"有多少糖尿病患者做过MRI？"
现实困难：必须写复杂的 SQL，涉及 JOIN、WHERE 等语法
```

**NL2SQL 系统的作用**：让医护人员用自然语言提问，系统自动生成 SQL

---

### 1.2 NL2SQL 系统的困境

**现状**：NL2SQL 系统（如 RAG-to-SQL）已经有了一定效果，但问题不少。

**核心困境**：如何让系统"举一反三"？

**通俗理解**：

```
场景1：系统学过"糖尿病患者有多少？"
场景2：用户问"哮喘患者有多少？"

期望：系统能把"糖尿病"替换成"哮喘"，复用之前的 SQL 结构
现实：系统检索"哮喘患者"的例子，找不到，只能从头生成
```

**问题分析**：

| 问题 | 说明 | 示例 |
|------|------|------|
| **直接匹配困难** | 用户问题与历史问题文字不同，难以匹配 | "糖尿病" vs "哮喘" |
| **实体值不匹配** | SQL 中的具体值不同，无法直接复用 | `WHERE disease='diabetes'` vs `WHERE disease='asthma'` |
| **数据稀缺** | 新医院没有太多历史案例 | 刚上线时系统效果差 |

---

### 1.3 核心洞察：结构相同，只是值不同

**关键发现**：

> 很多医疗查询，SQL 结构是相同的，只是"疾病名"、"药物名"等具体值不同

**示例**：

```sql
-- 问题1："糖尿病患者有多少？"
SELECT COUNT(*) FROM patients WHERE disease = 'diabetes'

-- 问题2："哮喘患者有多少？"
SELECT COUNT(*) FROM patients WHERE disease = 'asthma'

-- 观察：SQL 结构完全相同，只是 disease 的值不同！
```

**核心问题**：如何让系统识别"结构相同，只是值不同"？

---

### 1.4 现有方法的局限

**RAG-to-SQL 的问题**：

| 问题 | 说明 | 后果 |
|------|------|------|
| **检索不精准** | 用问题文本检索相似案例 | "糖尿病"和"哮喘"语义不相似，检索失败 |
| **噪音爆炸** | 为了覆盖更多情况，加了很多案例 | 检索结果混乱，反而降低准确率 |
| **数据稀缺** | 新医院缺乏历史案例 | 冷启动困难 |

**核心矛盾**：

> 普通 RAG 检索的是"问题文本相似"，而非"SQL 结构相似"
> 
> 但我们真正需要的是：找到结构相似的 SQL 模板，然后替换实体值

---

### 1.5 研究问题

**本文要解决的问题**：

1. 如何让系统识别"SQL 结构相似但实体值不同"的查询？
2. 如何在数据稀缺时也能工作？
3. 如何构建可复用的 SQL 模板库？

**核心贡献**：CBR-to-SQL —— 案例推理 + 两阶段分离（结构匹配 + 实体填充）

---

## 二、核心创新：CBR-to-SQL 框架

### 2.1 核心思想

**案例推理（Case-Based Reasoning）+ 两阶段分离**：

```
传统 RAG:  问题 → 检索相似例子 → 直接生成 SQL
                    ↓
               问题和例子都包含具体实体值
                    ↓
               实体不匹配 → SQL 错误

CBR-to-SQL: 问题 → 【抽象模板匹配】→ 【实体落地】→ SQL
                    ↓                    ↓
              只匹配逻辑结构        单独处理实体
```

### 2.2 两阶段架构详解

```
┌─────────────────────────────────────────────────────────────────┐
│                    CBR-to-SQL 完整架构                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ══════════════════ 离线阶段：Case Retain ══════════════════   │
│                                                                 │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐        │
│  │ 问题-SQL 对 │ → │ 实体掩码    │ → │ 模板入库    │        │
│  │ (原始数据)  │    │ (抽象化)    │    │ (向量库)    │        │
│  └─────────────┘    └─────────────┘    └─────────────┘        │
│                                                                 │
│  示例:                                                         │
│  问题: "糖尿病患者平均年龄"                                     │
│  SQL: SELECT AVG(age) FROM patients WHERE disease='diabetes'   │
│                          ↓ 掩码                                │
│  模板: SELECT AVG({col}) FROM {tbl} WHERE {col}={val}          │
│                                                                 │
│  ══════════════════ 在线阶段：Query Phase ══════════════════   │
│                                                                 │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐        │
│  │ 用户问题    │ → │ Step 1:     │ → │ Step 2:     │ → SQL  │
│  │             │    │ 模板构建    │    │ 实体落地    │        │
│  └─────────────┘    └─────────────┘    └─────────────┘        │
│                                                                 │
│  Step 1 - 模板构建:                                            │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │ 1. 问题掩码: "哮喘患者用过布地奈德"                       │   │
│  │            → "{疾病}患者用过{药物}"                      │   │
│  │                                                         │   │
│  │ 2. 检索相似模板: 向量相似度匹配                          │   │
│  │                                                         │   │
│  │ 3. 生成 SQL 草稿:                                        │   │
│  │    SELECT COUNT(patient_id)                              │   │
│  │    FROM {table}                                          │   │
│  │    WHERE {disease_col} = {disease_val}                   │   │
│  │      AND {drug_col} = {drug_val}                         │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  Step 2 - 实体落地:                                            │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │ 1. 实体识别: 哮喘 → disease, 布地奈德 → drug             │   │
│  │                                                         │   │
│  │ 2. 查询实体映射表:                                       │   │
│  │    哮喘 → diagnoses.icd9_code = '493'                   │   │
│  │    布地奈德 → medications.drug_name = 'Budesonide'      │   │
│  │                                                         │   │
│  │ 3. 修正处理: 错别字纠正、缩写展开                        │   │
│  │    "布地" → "布地奈德" → "Budesonide"                   │   │
│  │                                                         │   │
│  │ 4. 填充 SQL: 最终可执行 SQL                              │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 三、核心技术细节

### 3.1 离线阶段：实体掩码

**实体类型定义**：

| 实体类型 | 示例 | 掩码标签 |
|---------|------|---------|
| 疾病 | 糖尿病、哮喘、高血压 | {disease} |
| 药物 | 布地奈德、阿司匹林 | {drug} |
| 检查项目 | MRI、CT、血常规 | {procedure} |
| 表名 | patients, diagnoses | {table} |
| 列名 | age, icd9_code | {column} |

**掩码处理流程**：

```python
def mask_sql(question, sql, schema):
    # 1. NER 识别实体
    entities = extract_entities(question)
    
    # 2. Schema Linking 匹配表名、列名
    schema_entities = match_schema(entities, schema)
    
    # 3. 替换为占位符
    masked_sql = sql
    for entity in entities:
        masked_sql = masked_sql.replace(entity.value, entity.placeholder)
    
    return masked_sql, entities
```

**示例**：

```
原始:
  问题: "糖尿病患者的平均年龄"
  SQL: SELECT AVG(age) FROM patients WHERE disease='diabetes'

掩码后:
  问题模板: "{疾病}患者的平均年龄"
  SQL模板: SELECT AVG({column}) FROM {table} WHERE {column}={value}"
  实体记录: [
    {原值: '糖尿病', 类型: disease, 标签: {disease}},
    {原值: 'age', 类型: column, 标签: {column}},
    {原值: 'patients', 类型: table, 标签: {table}}
  ]
```

### 3.2 在线阶段 Step 1：模板构建

**输入**：用户自然语言问题

**处理流程**：

```python
def template_construction(question, template_db, llm):
    # 1. 问题掩码
    masked_question, entities = mask_question(question)
    
    # 2. 检索相似模板
    similar_templates = vector_search(masked_question, template_db, top_k=3)
    
    # 3. LLM 生成 SQL 草稿
    prompt = f"""
    相似模板: {similar_templates}
    用户问题: {masked_question}
    请生成 SQL 模板（用占位符表示实体）
    """
    sql_template = llm.generate(prompt)
    
    return sql_template, entities
```

**输出**：带占位符的 SQL 草稿

### 3.3 在线阶段 Step 2：实体落地

**实体映射表结构**：

```python
entity_lookup_table = {
    'diseases': {
        '糖尿病': {'table': 'diagnoses', 'column': 'icd9_code', 'value': '250'},
        '哮喘': {'table': 'diagnoses', 'column': 'icd9_code', 'value': '493'},
        '高血压': {'table': 'diagnoses', 'column': 'icd9_code', 'value': '401'},
        # ...
    },
    'drugs': {
        '布地奈德': {'table': 'medications', 'column': 'drug_name', 'value': 'Budesonide'},
        '阿司匹林': {'table': 'medications', 'column': 'drug_name', 'value': 'Aspirin'},
        # ...
    },
    'procedures': {
        'MRI': {'table': 'procedures', 'column': 'procedure_name', 'value': 'MRI'},
        # ...
    }
}
```

**实体查询流程**：

```python
def entity_retrieval(entity_name, entity_type, lookup_table, llm):
    # 1. 精确匹配
    if entity_name in lookup_table[entity_type]:
        return lookup_table[entity_type][entity_name]
    
    # 2. 语义搜索（处理缩写、错别字）
    candidates = semantic_search(entity_name, lookup_table[entity_type])
    
    # 3. LLM 消歧（多候选时）
    if len(candidates) > 1:
        return llm.disambiguate(entity_name, candidates)
    
    return candidates[0]
```

**错别字修正示例**：

```
用户输入: "布地"（不完整）
    ↓ 语义搜索
候选: ["布地奈德", "布洛芬"]
    ↓ LLM 消歧（结合上下文"哮喘患者"）
结果: 布地奈德 → Budesonide
```

---

## 四、实验设计

### 4.1 数据集选择

**MIMICSQL**（医疗 Text-to-SQL 标准基准）

**选择理由**：

| 考量 | MIMICSQL 的优势 |
|------|----------------|
| **真实性** | 基于 MIMIC-III 真实电子病历库 |
| **术语复杂度** | 医疗术语杂乱，测试检索能力 |
| **标准基准** | 已有文献支持，可对比 |

**数据集特征**：

| 属性 | 值 | 说明 |
|------|-----|------|
| 数据来源 | MIMIC-III 电子病历库 | 真实医疗场景 |
| 问题-SQL 对 | 10,000+ | 规模足够 |
| 表数量 | 5 | 多表 JOIN 场景 |
| 特点 | 医疗术语复杂 | 测试实体处理能力 |

**实验场景设计**：

| 场景 | 训练集 | 测试集 | 模拟场景 | 设计目的 |
|------|--------|--------|---------|---------|
| **CDB (Complete DB)** | 随机 100% | 随机 Split | 成熟医院 | 验证充分数据下的效果 |
| **IDB (Incomplete DB)** | 随机 10% | 随机 Split | 新医院 | 验证数据稀缺下的鲁棒性 |

**为什么设计两种场景？**

- **CDB**：验证「理想条件」下的上限
- **IDB**：验证「冷启动」场景的鲁棒性（与本研究高度相关！）

### 4.2 Baseline 选择

| Baseline | 选择理由 | 代表方法类型 |
|----------|---------|-------------|
| **RAG-to-SQL** | 最接近的竞品，直接检索问题-SQL 对 | 检索式方法 |
| **DIN-SQL** | SOTA 任务分解方法 | 生成式方法 |
| **DAIL-SQL** | In-context Learning | Few-shot 方法 |

**为什么选 RAG-to-SQL 作为主要对比对象？**

> 因为 RAG-to-SQL 是 TailorSQL 之前最接近的方法，都是"检索历史例子辅助生成"

### 4.3 评估指标

| 指标 | 定义 | 计算方式 | 重要性 |
|------|------|---------|--------|
| **Acc_LF** | 逻辑形式正确率 | SQL 结构正确（忽略具体值） | 高 |
| **Acc_EX** | 执行正确率 | SQL 执行结果与标准答案一致 | **最高** |
| **脆弱性指标** | 删除最优例子后准确率下降幅度 | 下降越小越鲁棒 | 中 |

**为什么定义两个准确率指标？**

- **Acc_LF**：排除值错误的影响，专注于结构正确性
- **Acc_EX**：最终用户关心的"结果对不对"

---

## 五、实验结果与分析

### 5.1 主实验结果

**准确率对比**：

| 方法 | Acc_LF (逻辑正确) | Acc_EX (执行正确) |
|------|------------------|-------------------|
| **CBR-to-SQL (CDB)** | **82.8%** | **88.2%** |
| RAG-to-SQL (CDB) | 75.6% | 81.4% |
| DIN-SQL (CDB) | 78.2% | 83.5% |
| **CBR-to-SQL (IDB)** | **71.3%** | **76.8%** |
| RAG-to-SQL (IDB) | 64.2% | 69.5% |

**关键发现**：

| 发现 | 说明 |
|------|------|
| 全量数据优势 | CBR-to-SQL 准确率比 RAG 高 7+ 个点 |
| 稀缺数据优势 | 只有 10% 数据时，优势更明显 |
| SOTA | 医疗领域 Text-to-SQL 新标杆 |

### 5.2 脆弱性测试

**测试方法**：删除检索到的最优例子，观察准确率下降幅度

| 方法 | 删除最优例子后准确率下降 |
|------|------------------------|
| **CBR-to-SQL** | **-3.2%** |
| RAG-to-SQL | -8.7% |

**结论**：CBR-to-SQL 不是靠死记个别例子，而是学会了 SQL 的逻辑结构

### 5.3 消融实验

| 配置 | Acc_LF | 说明 |
|------|--------|------|
| **完整 CBR-to-SQL** | **82.8%** | 两阶段完整 |
| 去掉实体落地 | 71.5% | 准确率暴跌 11+ 个点 |
| 模板构建用单步RAG | 79.3% | 小幅下降 |
| 用 GPT-4.1-mini | 78.6% | 弱模型也能保持优势 |

**结论**：两阶段设计是核心，实体落地步骤不可或缺

---

## 六、对 DA-CaseBase 的深度启示

### 5.1 与本研究的高度相似性

| 维度 | CBR-to-SQL | DA-CaseBase（本研究） |
|------|-----------|---------------------|
| **核心思想** | 模板匹配 + 实体分离 | 案例匹配 + SQL 复用 |
| **数据结构** | 抽象模板库 | 案例库 |
| **两阶段** | 模板构建 + 实体落地 | 案例匹配 + 参数填充 |
| **应用场景** | 医疗 | 企业数据分析 |

### 5.2 可直接借鉴的技术

| 技术 | 借鉴方式 |
|------|---------|
| **实体掩码** | 将企业 SQL 中的业务实体（地区、产品、时间）抽象为占位符 |
| **两阶段分离** | 模板匹配（逻辑结构）+ 实体落地（参数填充）|
| **实体映射表** | 构建企业业务术语 → 数据库字段映射 |
| **脆弱性指标** | 评估案例库的鲁棒性 |

### 5.3 具体实现建议

**Step 1：构建企业实体映射表**

```python
# 企业数据分析场景的实体类型
entity_types = {
    'region': ['华东', '华南', '华北', ...],      # 地区
    'product': ['产品A', '产品B', ...],           # 产品
    'time_period': ['本月', '上季度', ...],       # 时间周期
    'metric': ['销售额', '订单量', ...],          # 指标
}

# 实体到 Schema 的映射
entity_mapping = {
    '华东': {'table': 'orders', 'column': 'region', 'value': 'east_china'},
    '销售额': {'column': 'sales_amount'},
    '上季度': {'condition': "date >= DATE_SUB(NOW(), INTERVAL 1 QUARTER)"},
}
```

**Step 2：模板抽象**

```python
def abstract_template(sql, entity_mapping):
    # 1. 解析 SQL 结构
    parsed = sqlparse.parse(sql)[0]
    
    # 2. 识别实体
    for entity_type, entities in entity_mapping.items():
        for entity in entities:
            if entity in sql:
                sql = sql.replace(entity, f'{{{entity_type}}}')
    
    return sql

# 示例
sql = "SELECT SUM(sales) FROM orders WHERE region='华东' AND date >= '2025-01-01'"
template = abstract_template(sql, entity_mapping)
# 结果: "SELECT SUM(sales) FROM orders WHERE region={region} AND date >= {date}"
```

**Step 3：查询流程**

```python
def generate_sql(question, template_db, entity_mapping):
    # 1. 模板构建
    template = retrieve_template(question, template_db)
    
    # 2. 实体识别
    entities = extract_entities(question)
    
    # 3. 实体落地
    filled_sql = fill_template(template, entities, entity_mapping)
    
    return filled_sql
```

### 5.4 与 CBR-to-SQL 的差异点

| 维度 | CBR-to-SQL | DA-CaseBase（需补充） |
|------|-----------|---------------------|
| **领域** | 医疗 | 企业数据分析 |
| **实体类型** | 疾病、药物、检查 | 地区、产品、时间、指标 |
| **数据规模** | 10K 问题-SQL 对 | 待定（企业历史查询量） |
| **实时性要求** | 低（病历查询） | 高（业务分析） |
| **冷启动** | 医院初期数据少 | 新系统无历史数据 |

### 5.5 需要解决的关键问题

| 问题 | CBR-to-SQL 方案 | DA-CaseBase 需要思考 |
|------|----------------|---------------------|
| **冷启动** | 论文未深入讨论 | 如何在无历史数据时启动？ |
| **实体歧义** | LLM 消歧 | "销售额"是 revenue 还是 sales_amount？ |
| **复杂查询** | 单表为主 | 多表 JOIN、子查询如何模板化？ |
| **实时更新** | 静态映射表 | Schema 变更后如何更新模板？ |

---

## 七、论文局限性

### 6.1 论文承认的局限

| 局限 | 说明 |
|------|------|
| **实体标注错误** | 偶尔标错医疗实体 |
| **实体拆分** | 把一个实体拆成两个来标 |
| **缩写识别** | 医疗缩写识别不准 |

### 6.2 更深层局限

| 局限 | 对 DA-CaseBase 的启示 |
|------|---------------------|
| **单一领域** | 仅医疗，跨领域泛化能力未知 |
| **模板覆盖** | 复杂查询（多表 JOIN）模板化困难 |
| **维护成本** | 实体映射表需要持续维护 |

---

## 八、研究价值总结

### 7.1 方法论价值

1. **两阶段分离设计**：逻辑结构与实体分离，是案例库设计的核心思想
2. **脆弱性指标**：评估案例库鲁棒性的新视角
3. **稀缺数据优化**：解决冷启动问题的思路

### 7.2 工程价值

1. **不需要重新训练模型**：基于现有 LLM 的框架改造
2. **易调试**：两阶段结构，问题定位清晰
3. **可扩展**：新领域只需更新实体映射表

### 7.3 与本研究的关系

```
CBR-to-SQL 是 DA-CaseBase 最直接的技术对照

DA-CaseBase = CBR-to-SQL 核心思想
            + 企业数据分析场景适配
            + 三层架构（缓存 + 模板 + 生成）
            + 自动沉淀机制
```

---

## 九、论文链接

- arXiv: https://arxiv.org/abs/2603.05569
- Aalto University: https://aaltodoc.aalto.fi/items/b41458e4-cf38-452b-ad44-437e9976f56c

---

*深度解读日期：2026-03-17 16:32*
*参考：豆包解读 + 多源文献分析*