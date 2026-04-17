# P43 - DTS-SQL 论文解读

> arXiv: 2402.01117 | 2024年2月 | University of Alberta
> 关键词：小模型分解、数据隐私、Text-to-SQL

---

## 基本信息

- **论文标题**: DTS-SQL: Decomposed Text-to-SQL with Small Large Language Models
- **发表时间**: 2024年2月
- **核心方向**: 用小模型（7B）替代大模型（GPT-4）做Text-to-SQL，通过分解降低任务复杂度

## 核心问题

**现有SOTA方法严重依赖闭源大模型（GPT-4, ChatGPT），存在数据隐私和部署成本问题。**

DTS-SQL 提出通过任务分解，使小模型也能达到接近大模型的效果。

## 技术方法

### 核心思路：分解训练

将 Text-to-SQL 分解为子任务，每个子任务用单一目标训练小模型：
1. **Schema Linking**：识别问题中涉及的表和列
2. **Query Classification**：Easy / Non-Nested / Nested 三级分类
3. **SQL Generation**：基于 NatSQL 中间表示生成 SQL
4. **Self-Correction**：执行反馈纠错

### 关键创新
- 每个 sub-task 单独训练，降低小模型学习难度
- 可用更小的模型（7B级）达到与 GPT-4 接近的效果
- **数据隐私友好**：可在私有环境部署

## 实验结果

| 数据集 | 模型 | EX |
|--------|------|-----|
| Spider Dev | GPT-4 | ~85.3% |
| Spider Dev | DTS-SQL (小模型) | 接近 GPT-4 水平 |
| BIRD | GPT-4 | ~55-60% |

## 与 DA-CaseBase 的关系

### 可借鉴的技术点
1. **分解思路对案例模板抽象的启示**：DTS-SQL 的 Schema Linking + SQL Generation 分离 → DA-CaseBase 的模板匹配（Schema级）+ 实体落地（实例级）分离
2. **小模型 + 案例库 = 大模型效果**：如果案例库提供高质量 few-shot 示例，小模型可能不需要分解也能达到好效果
3. **数据隐私契合度**：案例库方案同样支持私有部署，且案例库可以预加载不需要推理

### 本研究的切入点
- DTS-SQL 证明**分解降低难度**，但增加了系统复杂度（4个模块）
- DA-CaseBase 提供另一种降维方式：**用案例检索替代分解**，系统更简单
- 两种方法可以互补：分解 + 案例增强

---

*解读时间: 2026-04-17 | 基于搜索摘要（PDF未下载）*
