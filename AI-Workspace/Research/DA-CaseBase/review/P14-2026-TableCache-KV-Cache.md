# P14-2026-TableCache - KV Cache 预计算优化

> **论文标题**：TableCache: Primary Foreign Key Guided KV Cache Precomputation for Low Latency Text-to-SQL
> **作者**：Jinbo Su, Yuxuan Hu, Cuiping Li, Hong Chen, Jia Li, Lintao Ma, Jing Zhang
> **年份**：2026
> **来源**：arXiv:2601.08743
> **相关 RQ**：RQ3 三层架构设计
> **相关度**：⭐ **（KV Cache 预计算需要特定推理引擎支持，与案例库方法差异大）**

---

## 一、研究背景

### 1.1 应用场景

**Text-to-SQL 的延迟问题**：

| 延迟来源 | 占比 | 说明 |
|---------|------|------|
| **Prefill 阶段** | 60-80% | 处理长 Schema 的 Prompt |
| **Decode 阶段** | 20-40% | 生成 SQL Token |

**核心问题**：

> 每次查询都需要处理完整的数据库 Schema，导致 Prefill 延迟高

### 1.2 现有方法的局限

| 方法 | 局限性 |
|------|--------|
| **SGLang / vLLM** | 无法复用 Schema 的 KV Cache |
| **传统缓存** | 缓存 SQL 结果，不缓存计算过程 |
| **Schema 裁剪** | 可能裁剪掉重要信息 |

---

## 二、问题定义

### 2.1 问题形式化

**输入**：
- 数据库 Schema S（数百张表）
- 用户问题 q

**输出**：
- SQL 查询
- 最小化 Prefill 延迟

**目标**：
- 预计算 Schema 的 KV Cache
- 查询时复用，降低延迟

### 2.2 核心挑战

| 挑战 | 说明 |
|------|------|
| **C1: Cache 组织** | 如何组织 Schema 的 KV Cache？|
| **C2: 复用策略** | 如何根据问题选择相关 Cache？|
| **C3: 主外键引导** | 如何利用 Schema 结构优化？|

---

## 三、核心方法

### 3.1 核心思想

**利用主外键关系引导 KV Cache 预计算**：

```
数据库 Schema
    ↓ 按表分组
表级 KV Cache
    ↓ 主外键关联
相关表 Cache 组合
    ↓ 查询时复用
快速 Prefill
```

### 3.2 整体架构

```
┌─────────────────────────────────────────────────────────────────────┐
│                    TableCache 架构                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │              离线阶段：KV Cache 预计算                        │   │
│  │  ─────────────────────────────────────────────────────────  │   │
│  │  1. 按表分割 Schema                                           │   │
│  │  2. 预计算每张表的 KV Cache                                    │   │
│  │  3. 基于主外键构建 Cache 索引                                  │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                              ↓                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │              在线阶段：查询处理                                │   │
│  │  ─────────────────────────────────────────────────────────  │   │
│  │  1. 识别相关表                                                │   │
│  │  2. 加载对应表的 KV Cache                                      │   │
│  │  3. 组装 Prompt + 预计算 Cache                                 │   │
│  │  4. 生成 SQL                                                   │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.3 主外键引导策略

**利用 Schema 结构**：

```python
def build_cache_index(schema):
    cache_index = {}
    
    for table in schema.tables:
        # 预计算表的 KV Cache
        cache_index[table.name] = precompute_kv_cache(table)
        
        # 记录主外键关系
        cache_index[table.name].primary_keys = table.primary_keys
        cache_index[table.name].foreign_keys = table.foreign_keys
        
    return cache_index

def load_relevant_caches(question, cache_index):
    # 1. 识别相关表
    relevant_tables = identify_relevant_tables(question)
    
    # 2. 加载对应 Cache
    caches = [cache_index[table] for table in relevant_tables]
    
    # 3. 按主外键关系组合
    combined_cache = combine_by_fk_relations(caches)
    
    return combined_cache
```

### 3.4 效果

| 指标 | 传统方法 | TableCache | 提升 |
|------|---------|-----------|------|
| **TTFT** | 基线 | **降低 3.62x** | 显著 |
| **准确率影响** | - | 忽略不计 | 可接受 |

---

## 四、对 DA-CaseBase 的启示

### 6.1 适用性分析

| 场景 | 适用性 | 原因 |
|------|--------|------|
| **降低延迟** | ⭐⭐⭐⭐ | Layer 3 生成时可复用 |
| **KV Cache 复用** | ⭐⭐⭐ | 需要特定推理引擎支持 |

### 6.2 可借鉴的技术

| 技术 | 应用方式 |
|------|---------|
| **表级 Cache** | 预计算 Schema 相关部分的 KV Cache |
| **主外键引导** | 利用 Schema 结构优化检索 |

### 6.3 不适用的部分

| 功能 | 原因 |
|------|------|
| **需要特定引擎** | 依赖 vLLM/SGLang 等支持 KV Cache 复用 |

---

## 五、研究价值总结

### 5.1 核心贡献

1. **KV Cache 预计算**：Schema 级别的 Cache 复用
2. **主外键引导**：利用 Schema 结构优化
3. **显著加速**：TTFT 降低 3.62x

### 5.2 与本研究的关系

**借鉴价值**：
- 预计算思想 → 案例库的预计算 Embedding
- 表级组织 → 案例按 Schema 组织

**局限性**：
- 需要特定推理引擎支持

---

## 六、论文链接

- arXiv: https://arxiv.org/abs/2601.08743

---

*深度解读日期：2026-03-17 18:43*