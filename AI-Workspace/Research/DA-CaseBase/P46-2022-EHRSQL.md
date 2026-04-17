# P46 - EHRSQL 论文解读

> NeurIPS 2022 (Datasets and Benchmarks) | KAIST
> 关键词：EHR、医疗Text-to-SQL、不可回答检测、时间表达式

---

## 基本信息

- **论文标题**: EHRSQL: A Practical Text-to-SQL Benchmark for Electronic Health Records
- **发表会议**: NeurIPS 2022 (Datasets and Benchmarks)
- **作者**: Sungwon Han, Soyoung Jeon 等（KAIST）
- **GitHub**: glee4810/EHRSQL

## 核心问题

**现有 Text-to-SQL 基准（Spider等）过于干净，无法反映真实企业场景的复杂性。**

EHRSQL 提供医疗电子病历领域的真实挑战场景。

## 核心挑战

### 1. 广泛的查询需求
- 简单检索到复杂操作（生存率计算、时间序列分析）
- 反映真实医院场景

### 2. 时间表达式理解
- "过去3个月"、"入院后第7天" 等医疗时间敏感问题
- 需要精确的时间计算逻辑

### 3. 不可回答问题检测
- 基于预测置信度判断问题是否可回答
- 关键的**安全机制**（避免生成错误SQL）

## 实验结果

EHRSQL 建立了医疗 Text-to-SQL 的评估基准，后续方法（如 CBR-to-SQL）在此领域展开研究。

## 与 DA-CaseBase 的关系

### ⭐ 高度相关

EHRSQL 揭示的问题正是 DA-CaseBase 要解决的：

| EHRSQL 挑战 | DA-CaseBase 解决方案 |
|------------|-------------------|
| 领域术语不认识 | 案例库存储术语→列映射 |
| 时间表达式理解 | 案例库包含时间处理模板 |
| 不可回答问题 | 案例库置信度 + 拒绝机制 |
| Schema复杂度高 | 案例预存Schema子集 |

### 可借鉴的技术点

1. **不可回答问题检测**：
   - EHRSQL 的置信度拒绝机制 → DA-CaseBase 的案例匹配置信度
   - 低置信度时拒绝回答或降级到 LLM 生成

2. **领域术语映射是案例库的核心价值**：
   - EHR 场景：医学术语 → ICD编码 → 数据库列名
   - 企业场景：业务术语 → 字段名 → 数据库列名
   - 完全同构的问题！

3. **Schema 复杂度的相似性**：
   - 医院数据库：100+ 张表，复杂关联
   - 企业数据库：同样 100+ 张表
   - 证明案例库方案的通用性

### 本研究的切入点
- EHRSQL 证明**领域专用性**是 Text-to-SQL 的核心挑战
- DA-CaseBase 的案例库天然存储领域知识
- 可以用 EHRSQL 验证案例库在跨领域场景的效果

---

*解读时间: 2026-04-17 | 基于搜索摘要（PDF未下载）*
