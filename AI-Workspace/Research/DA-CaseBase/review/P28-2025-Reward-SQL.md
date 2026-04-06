# P28-2025-Reward-SQL - 过程奖励增强 Text-to-SQL

> **论文标题**：Reward-SQL: Boosting Text-to-SQL via Stepwise Reasoning and Process-Supervised Rewards
> **作者**：待补充
> **年份**：2025
> **来源**：arXiv:2505.04671
> **来源链接**：https://arxiv.org/abs/2505.04671
> **相关 RQ**：RQ2 案例模板表示与泛化
> **相关度**：⭐⭐ 中度相关
> **解读时间**：2026-03-20 14:50

---

## 一、研究背景

### 1.1 问题场景

**场景描述**：用户问"找出2022年评分高于平均值的球队名称"，Text-to-SQL 系统需要生成复杂 SQL。但当 SQL 涉及子查询、多表连接时，单次生成容易出错。

**具体示例**：
```
用户问题："找出buildupplaypass高于2012年平均值的球队"

错误 SQL（直接生成）：
SELECT team_long_name FROM Team WHERE buildupplaypass > AVG(buildupplaypass)

问题：AVG 不能直接用在 WHERE 子句中
```

**现有方案的问题**：
- 直接生成完整 SQL，错误难定位
- 即使有推理链，也只看最终结果奖励（ORM）
- 中间步骤出错无法被发现和纠正

### 1.2 学术场景 vs 企业场景对比

| 维度 | 学术基准 | 企业实际 |
|------|---------|---------|
| 数据规模 | BIRD/Spider 标准集 | 企业级复杂 Schema |
| 评估指标 | Execution Accuracy | 准确性 + 效率 |
| 可用资源 | 标准训练集 | 领域标注数据 |

---

## 二、核心贡献

### 2.1 主要贡献

1. **Chain-of-CTEs**：将复杂 SQL 分解为可解释的 CTE 链，每步独立验证
2. **PRM 集成策略**：四种集成方式（Training Signal、Inference Guide、Verifier、Reward Shaper）
3. **两阶段范式**：冷启动 + PRM 监督，有效提升推理质量

### 2.2 创新点

- **方法创新**：过程奖励模型（PRM）替代结果奖励模型（ORM）
- **技术创新**：Chain-of-CTEs 分解策略，实现细粒度监督
- **应用创新**：GRPO + Best-of-N 组合策略

---

## 三、方法论

### 3.1 整体框架

```
┌─────────────────────────────────────────────┐
│           Reward-SQL Framework              │
├─────────────────────────────────────────────┤
│  Stage 1: SFT 冷启动                        │
│    - 建立稳定推理基础                        │
│    - 学习基本 SQL 生成能力                   │
├─────────────────────────────────────────────┤
│  Stage 2: PRM 监督                          │
│    - Chain-of-CTEs 分解                     │
│    - 每步过程奖励                           │
│    - GRPO 在线学习                          │
├─────────────────────────────────────────────┤
│  Inference: Best-of-N                       │
│    - 多路径探索                             │
│    - 选择最优 SQL                           │
└─────────────────────────────────────────────┘
```

### 3.2 核心方法

#### 方法1：Chain-of-CTEs 分解

**输入**：复杂 SQL 查询
**输出**：CTE 链（每步可独立验证）
**核心步骤**：
1. 识别 SQL 中的子查询和复杂逻辑
2. 转换为 WITH 子句（CTE）
3. 每步添加验证点

**关键示例**：
```sql
-- 原始 SQL
SELECT t.team_long_name 
FROM Team t JOIN TeamAttributes ta ON t.team_api_id = ta.team_api_id
WHERE buildupplaypass > (SELECT AVG(buildupplaypass) FROM TeamAttributes)

-- Chain-of-CTEs 分解
WITH 
Avg_Buildup AS (
  SELECT AVG(buildupplaypassing) AS avg_build_up 
  FROM TeamAttributes
),
Above_Avg_Teams AS (
  SELECT team_api_id FROM TeamAttributes 
  WHERE buildupplaypass > (SELECT avg_build_up FROM Avg_Buildup)
)
SELECT t.team_long_name FROM Team t 
INNER JOIN Above_Avg_Teams aat ON t.team_api_id = aat.team_api_id;
```

#### 方法2：PRM 集成策略

| 集成方式 | 描述 | 效果 |
|---------|------|------|
| Training Signal | GRPO 在线学习调整策略 | +7% |
| Inference Guide | Best-of-N 选择最优路径 | +1.3% |
| Verifier | 执行前验证拒绝低质量 | 提升稳定性 |
| Reward Shaper | 平衡准确性与效率 | 降低延迟 |

### 3.3 技术细节

| 组件 | 实现方式 | 备注 |
|------|---------|------|
| 基础模型 | 7B 参数 LLM | 开源模型 |
| PRM 训练 | 过程标注数据 | 需要额外标注 |
| GRPO | Group Relative Policy Optimization | 强化学习 |

---

## 四、实验结果

### 4.1 实验设置

**数据集**：

| 数据集 | 规模 | 用途 |
|--------|------|------|
| BIRD | 12,750 问题 | 主实验 |
| Spider | 7,000 问题 | 验证 |

**Baseline**：

| Baseline | 描述 |
|----------|------|
| Baseline (7B) | 无 SFT 的原始模型 |
| SFT Only | 仅监督微调 |

**评估指标**：Execution Accuracy

### 4.2 主实验结果

| 方法 | 模型规模 | BIRD Accuracy | 提升 |
|------|---------|---------------|------|
| Baseline | 7B | 55.8% | - |
| SFT Only | 7B | 62.1% | +6.3% |
| Reward-SQL (GRPO) | 7B | **68.9%** | +13.1% |
| + Best-of-N | 7B | 70.2% | +14.4% |

### 4.3 消融实验

| 变体 | Accuracy | 变化 |
|------|----------|------|
| 完整方法 | 70.2% | - |
| w/o PRM | 62.1% | ↓8.1% |
| w/o Chain-of-CTEs | 65.3% | ↓4.9% |
| w/o Best-of-N | 68.9% | ↓1.3% |

### 4.4 关键发现

1. **冷启动重要**：先建立稳定推理基础，PRM 才能有效
2. **组合策略最优**：训练阶段 GRPO + 推理阶段 Best-of-N
3. **CTE 分解有效**：可解释性和错误定位显著提升

---

## 五、与本研究的关系

### 5.1 相关性分析

| 维度 | 关联程度 | 具体关联 |
|------|---------|---------|
| 问题定义 | 中 | SQL 质量评估 |
| 方法论 | 高 | CTE 分解可借鉴 |
| 实验设计 | 低 | 不同评估场景 |

### 5.2 可借鉴点

| 借鉴点 | 如何借鉴 |
|--------|---------|
| CTE 分解 | 将复杂 SQL 模板分解为可复用单元 |
| 过程奖励 | 评估 SQL 模板的每个步骤质量 |
| 验证机制 | 案例匹配时的多步验证 |

### 5.3 局限性/问题

| 局限 | 影响 |
|------|------|
| PRM 训练成本高 | 需探索轻量级方案 |
| 需要中间步骤标注 | 案例库可自建标注 |
| 跨领域迁移困难 | 领域适配需要额外工作 |

---

## 六、关键引用

### 6.1 核心引用（Top 5）

1. **PRM 论文** - 过程奖励模型的理论基础
2. **GRPO 论文** - 强化学习优化方法
3. **BIRD 基准** - 实验评估数据集
4. **Spider 基准** - 标准评估基准
5. **Chain-of-Thought** - 推理链分解思想

### 6.2 可扩展引用

- [ ] PRM 训练数据构建方法
- [ ] 轻量级过程奖励方案

---

## 七、总结

### 一句话总结

> Reward-SQL 通过 Chain-of-CTEs 分解和 PRM 过程奖励，将 BIRD 准确率从 55.8% 提升到 70.2%。

### 证据分级

| 结论 | 证据等级 | 备注 |
|------|---------|------|
| CTE 分解有效 | Level A | 主实验 + 消融实验验证 |
| PRM 提升准确率 | Level A | 多组对比实验 |
| 冷启动必要 | Level B | 消融实验间接验证 |

---

**解读完成时间**：2026-03-20 14:50
**文件保存路径**：`~/agentKB/Obsidian/AI-Workspace/Research/DA-CaseBase/review/P28-2025-Reward-SQL.md`