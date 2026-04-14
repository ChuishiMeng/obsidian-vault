# KnowU-Bench

## 基本信息

- **机构**：浙江大学
- **发布时间**：2026年4月
- **类型**：LLM User Simulator 评估基准
- **任务数量**：192 个评估任务
- **相关论文**：待查（arXiv 搜索）

## 核心功能

### 1. LLM-driven User Simulator 评估

评估维度：
- Profile 准确性
- Response 一致性
- Memory 保持度
- 行为真实性

### 2. Structured Profiles

- 预定义用户画像
- 多维度人格建模
- 动态行为生成

### 3. 192 个评估任务

覆盖场景：
- 问答模拟
- 调查响应
- 对话交互
- 决策模拟

## 与 virtual-users 项目的关系

### 直接相关性 ⭐⭐⭐⭐⭐

| virtual-users 研究问题 | KnowU-Bench 提供的解决方案 |
|------------------------|---------------------------|
| 如何评估虚拟用户真实性？ | 192 个标准化评估任务 |
| Profile 如何影响响应？ | Structured Profiles 方法 |
| Memory 如何保持？ | Memory 保持度评估 |
| 如何验证 CQCB？ | 可能直接适用 |

### 对 P5-P7 的帮助

**P5 数据与指标**：
- KnowU-Bench 提供了评估虚拟用户的标准方法
- 可以直接借鉴其评估任务设计

**P6 实验设计**：
- 192 个任务可作为实验任务模板
- 减少自己设计评估任务的工作量

**P7 实验验证**：
- 可用 KnowU-Bench 的方法验证 ACS 指标
- 对比浙大的方法 vs 我们的 CQCB

## 需要进一步研究

1. **获取论文**：搜索 arXiv 浙大 KnowU-Bench
2. **分析评估方法**：理解 192 任务的具体设计
3. **对比 ACS 指标**：KnowU-Bench vs 我们提出的 ACS
4. **借鉴 Profile 方法**：Structured Profiles vs Persona 残差表示

## 行业意义

KnowU-Bench 是目前发现的**第一个标准化 LLM user simulator 评估基准**。

这意味着：
- 虚拟用户研究正在标准化
- 评估方法不再是各自发明
- virtual-users 项目有现成的参考框架

## 参考资料

- [浙大 KnowU-Bench 官网](https://knowu.zju.edu.cn)（待确认）
- [arXiv 搜索](https://arxiv.org/search/?query=KnowU-Bench&searchtype=all)

## 相关概念

- [[virtual-users]]
- [[llm-user-simulation]]
- [[cqcb]]
- [[acs-metric]]

---
*创建时间：2026-04-14*
*来源：深夜自主探索发现*