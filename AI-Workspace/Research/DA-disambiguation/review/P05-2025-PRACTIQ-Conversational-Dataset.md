# P05-PRACTIQ 论文笔记

**论文标题**: PRACTIQ: A Practical Conversational Text-to-SQL dataset with Ambiguous and Unanswerable Queries

**作者**: Mingwen Dong et al. (Amazon Web Services, UMass Amherst)

**发表**: NAACL 2025 (arXiv:2410.11076)

**代码**: https://github.com/amazon-science/conversational-ambiguous-unanswerable-text2sql

---

## 一、歧义问题类型分类

### 1.1 四种歧义类型 (Ambiguous)

| 类型 | 定义 | 示例 |
|------|------|------|
| **Ambiguous SELECT Column** | 问题有多个有效的 SQL，区别在于 SELECT 子句中使用的列 | 数据库: Stadium(Stadium Name, Standing Capacity, Seating Capacity)<br>问题: "What is the maximum capacity of all stadiums?"<br>歧义: capacity 可以指 Standing Capacity 或 Seating Capacity |
| **Ambiguous Values Within Column** | 问题可以映射到表中对应多个不同值的行 | 数据库: Classroom(Subject, Teacher Name)<br>问题: "Who is the Chemistry teacher?"<br>歧义: Subject 列包含 Organic Chemistry 和 Physical Chemistry 两个值 |
| **Ambiguous WHERE Columns** | 问题可以映射到多个不同列中的相同值 | 数据库: Properties(property_type_code, property_type_version)<br>问题: "What are the names of properties whose property type is a multiple of 5?"<br>歧义: 两列都包含值 5 |
| **Ambiguous Filter Criteria** | 问题中包含的术语在数据库中的定义/映射不明确 | 数据库: Thrombosis_Prediction(patient age, ...)<br>问题: "How many underage patients were examined...?"<br>歧义: "underage" 的具体年龄定义不明确 |

### 1.2 四种无法回答类型 (Unanswerable)

| 类型 | 定义 | 示例 |
|------|------|------|
| **Nonexistent SELECT Column** | 问题要求的列在数据库中不存在 | 数据库: Olympics(Medal, Name, Sport, Event)<br>问题: "What was the nickname of the gold medal winner...?"<br>无法回答: 数据库没有 nickname 信息 |
| **Nonexistent WHERE Column** | 用于过滤信息的列在数据库中不存在 | 数据库: Teams(Team Name, Ground, Town Name)<br>问题: "Which team comes from a town known for tin mining?"<br>无法回答: 没有关于 tin mining 的列 |
| **Nonexistent Filter Value** | 问题要求的值在数据库中不存在 | 数据库: Teams(Team Name, Ground)<br>问题: "What is the ground name of New York Yankees?"<br>无法回答: 数据库中没有 New York Yankees |
| **Unsupported Join** | 问题涉及无法通过外键连接的表 | 数据库: Students/Teachers/Grades 与 Library/Books 无外键连接<br>问题: "Which student borrowed book ABC from library XYZ?"<br>无法回答: 表之间无法 JOIN |

---

## 二、数据集构建方法

### 2.1 三阶段构建流程

```
Stage 1: SQL 解析 & 数据库修改
    ↓
Stage 2: SQL 修改 & 澄清响应生成
    ↓
Stage 3: 对话优化 & 质量控制
```

#### Stage 1: SQL 解析 & 数据库修改

- **方法**: 使用 SQLGLOT 解析 SQL，提取列和单元格值
- **核心思想**: 修改数据库而非用户问题（更符合真实场景，用户不了解数据库细节）
- **示例**: 
  - 对于 Ambiguous SELECT Column: 用 LLM 生成两个替代列（如 Capacity → Standing Capacity / Seating Capacity）
  - 对于 Nonexistent Filter Value: 从数据库中删除提到的单元格值

#### Stage 2: SQL 修改 & 澄清响应生成

- **助手响应生成**: 使用模板或提示方法生成助手对歧义/无法回答问题的初始响应
  - 模板示例: "I find two conflicting information in the data. Which one would you like to know about? Column_1 or Column_2"
  
- **逆向生成 (Reverse Generation)**:
  1. 先生成助手的最终 SQL 响应（通过编程修改原始 SQL）
  2. 再让 LLM 根据对话上下文填充用户的澄清响应
  3. 确保 SQL 准确且可执行（不是让 LLM 生成 SQL）

- ** Helpful SQL 生成** (创新点):
  - 对于 Ambiguous SELECT Column 和 Ambiguous WHERE Column，直接返回所有有效解释
  - 示例: "What is the maximum capacity?" → 返回 SELECT Standing Capacity, Seating Capacity
  - 减少用户获取信息所需的对话轮数

#### Stage 3: 对话优化 & 质量控制

- **对话优化**: 使用 3-shot prompt 改进对话的自然性和连贯性
- **添加自然语言解释**: 对 SQL 执行结果生成 NL 解释
- **质量控制**:
  - LLM 评估：检查生成的数据是否符合类别定义
  - 执行检查：确保所有 SQL 可执行
  - 二元分类过滤：每个类别生成后，用 LLM 进行二元分类验证

### 2.2 数据集统计

| 类别 | 示例数 | 二元分类准确率 |
|------|--------|---------------|
| Ambiguous SELECT Column | 171 | 90% |
| Ambiguous WHERE Column | 105 | 90% |
| Ambiguous Filter Criteria | 303 | 100% |
| Ambiguous Values Within Column | 122 | 80% |
| Nonexistent SELECT Column | 482 | 95% |
| Nonexistent WHERE Column | 236 | 95% |
| Unsupported Join | 213 | 100% |
| Nonexistent Filter Value | 170 | 100% |
| **Answerable (Spider Dev)** | **1034** | **100%** |
| **总计** | **2812** | **平均 93.75%** |

### 2.3 对话格式

每个样本包含 4 轮对话：
1. 用户初始问题
2. 助手澄清请求
3. 用户澄清响应
4. 助手 SQL 响应 + 执行结果 + NL 解释

### 2.4 质量评估

人工标注结果 (Likert 1-5 分，1 为最好):

| 指标 | 均值 | 标准差 | Krippendorff's Alpha |
|------|------|--------|---------------------|
| Naturalness | 1.57 | 0.87 | 0.8207 |
| Factuality | 1.15 | 0.53 | 0.6829 |
| Helpfulness | 1.41 | 0.74 | 0.7602 |

---

## 三、与本研究的关系（三层框架对应）

### 3.1 核心贡献对比

| 维度 | PRACTIQ | 本研究 (DA-disambiguation) |
|------|---------|---------------------------|
| **歧义分类** | 4 种歧义 + 4 种无法回答 (基于 SQL 结构) | 用户层/业务层/数据层三层框架 |
| **消歧方法** | 澄清问题 + Helpful SQL 直接返回 | 三层协同消歧框架 |
| **数据集** | 2812 轮对话 (基于 Spider) | 待构建 |
| **交互策略** | 固定 4 轮对话 | 最小交互策略 (借鉴 EIG) |

### 3.2 歧义类型映射

```
PRACTIQ 分类                          本研究三层框架
─────────────────────────────────────────────────────
Ambiguous SELECT Column      →        数据层歧义 (Schema Linking)
Ambiguous WHERE Columns      →        数据层歧义 (Schema Linking)
Ambiguous Values Within      →        业务层歧义 (概念定义)
Ambiguous Filter Criteria    →        业务层歧义 (业务规则)
Nonexistent SELECT Column    →        数据层无法回答
Nonexistent WHERE Column     →        数据层无法回答
Nonexistent Filter Value     →        数据层无法回答
Unsupported Join             →        数据层无法回答 (Schema 限制)
```

### 3.3 可借鉴的技术

1. **逆向生成方法**: 先生成 SQL 再生成用户响应 → 确保 SQL 准确性
   - 应用：构建我们的消歧对话数据集

2. **Helpful SQL 策略**: 对某些歧义直接返回所有可能解释
   - 应用：数据层歧义可直接返回多列，减少交互

3. **质量控制流程**: LLM 评估 + 执行检查 + 分类验证
   - 应用：确保我们数据集的质量

4. **9 分类任务**: 同时识别歧义类型和无法回答类型
   - 应用：我们的分类器设计 (9-way: 8 种问题类型 + 可回答)

### 3.4 研究空白（我们的机会）

| PRACTIQ 局限 | 我们的方向 |
|-------------|-----------|
| 分类基于 SQL 结构，缺乏用户意图理解 | 用户层消歧：用户画像 + 偏好学习 |
| 固定 4 轮对话，无最优问题选择策略 | 最小交互策略：借鉴 EIG 选择最优澄清问题 |
| 无业务知识图谱支持 | 业务层消歧：概念定义库 + 规则库 |
| 基于 Spider，缺乏真实业务场景 | 京东营销中台真实案例构建 |

### 3.5 三层框架对应关系

```
┌─────────────────────────────────────────────────────────┐
│                    用户层 (User Layer)                   │
│  - 用户画像驱动的消歧                                      │
│  - 隐式偏好学习 (PRACTIQ 未涉及)                          │
│  - 角色差异处理 (如 P03 提及但无机制)                      │
└─────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────┐
│                   业务层 (Business Layer)                │
│  - Ambiguous Filter Criteria → 业务规则消歧              │
│  - Ambiguous Values Within → 概念定义消歧                │
│  - 业务知识图谱支持 (PRACTIQ 未涉及)                      │
└─────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────┐
│                    数据层 (Data Layer)                   │
│  - Ambiguous SELECT/WHERE Column → Schema Linking        │
│  - Nonexistent Column/Value → Schema 验证                │
│  - Unsupported Join → Schema 连接性检查                  │
│  - PRACTIQ 主要覆盖此层                                   │
└─────────────────────────────────────────────────────────┘
```

### 3.6 实验设计借鉴

| 任务 | PRACTIQ 方法 | 我们的适配 |
|------|-------------|-----------|
| 问题分类 | 9-way 分类 (8 种 + 可回答) | 相同，但增加三层标签 |
| SQL 生成 | DIN-SQL 框架 | 可扩展为三层消歧后生成 |
| 评估指标 | 分类准确率 + 执行准确率 | 增加消歧轮数、用户满意度 |
| Cell Value 检索 | Oracle vs Lexical 对比 | 可增加业务层知识检索 |

---

## 四、关键发现

1. **SOTA 模型表现不佳**: Claude 3.5 Sonnet 最佳分类准确率仅 77.4%（含 Oracle cell values）
2. **Cell Value 检索重要**: 对涉及值的 3 个子类别，Oracle cell values 提升准确率 1.5%
3. **Helpful SQL 有效**: 对 Ambiguous SELECT/WHERE Column 直接返回所有解释可减少交互轮数
4. **对话质量高**: 人工标注 Naturalness/Factuality/Helpfulness 均分<1.6（1 为最好）

---

## 五、对我们的启示

1. **数据集构建**: 采用三阶段方法 + 逆向生成确保质量
2. **分类体系**: 9-way 分类任务设计可直接借鉴
3. **消歧策略**: Helpful SQL 可作为我们"最小交互"策略的基线
4. **研究定位**: PRACTIQ 主要覆盖数据层，我们在用户层和业务层有创新空间

---

*整理时间: 2026-03-20*
*整理人: 科研小新*
