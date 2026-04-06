# ST-MVL: Filling Missing Values in Geo-Sensory Time Series Data
**会议**: IJCAI 2016  
**作者**: Xiuwen Yi, Yu Zheng, Junbo Zhang, Tianrui Li  
**领域**: 数据质量 / 时空数据修复 / 传感器网络

---

## 核心贡献

### 问题定义
**Geo-Sensory时间序列数据**（如空气质量监测站数据）经常存在缺失值问题：
- 影响实时监控
- 降低后续数据分析性能
- 传统方法无法处理**块状缺失**(block missing)

### ST-MVL方法
**Spatio-Temporal Multi-View Learning**：时空多视图学习方法，联合填充多个传感器的缺失值

---

## 核心挑战

### 1. 块状缺失问题
```
传统缺失: 随机散点缺失
    X O X O X  ← NMF等方法可处理

块状缺失: 连续时间/空间缺失
    X X X O X  ← NMF等无法处理
    （某传感器持续故障，某区域无数据）
```

### 2. 空间依赖性
- 附近传感器数据相关性高
- 需要利用空间邻居信息

### 3. 时间依赖性
- 历史数据有周期性模式
- 需要利用时间序列特性

---

## 方法论

### 多视图框架

```
┌─────────────────────────────────────────┐
│              ST-MVL 框架                 │
├─────────────────────────────────────────┤
│  ┌──────────┐   ┌──────────┐            │
│  │空间视图  │   │时间视图  │            │
│  │(S-View)  │   │(T-View)  │            │
│  └────┬─────┘   └────┬─────┘            │
│       │              │                  │
│       └──────┬───────┘                  │
│              ▼                          │
│       ┌──────────┐                      │
│       │ 联合优化 │                      │
│       └────┬─────┘                      │
│            ▼                            │
│       填充结果                          │
└─────────────────────────────────────────┘
```

### 1. 空间视图 (S-View)
```python
def spatial_view(sensor_network, location):
    """
    利用空间邻居预测缺失值
    """
    neighbors = find_neighbors(location, k=5)
    
    # 空间权重（距离越近权重越大）
    weights = [1/distance(n, location) for n in neighbors]
    weights = normalize(weights)
    
    # 加权平均
    prediction = sum(w * n.value for w, n in 
                    zip(weights, neighbors))
    return prediction
```

### 2. 时间视图 (T-View)
```python
def temporal_view(time_series, timestamp):
    """
    利用历史数据预测缺失值
    """
    # 相同时间点历史值
    same_time_values = time_series[
        time_series.time % T == timestamp % T
    ]
    
    # 时间衰减权重（越近权重越大）
    weights = [exp(-λ * Δt) for Δt in time_deltas]
    
    prediction = weighted_average(same_time_values, weights)
    return prediction
```

### 3. 联合优化
```python
def joint_optimization(X_missing, S_view, T_view):
    """
    最小化目标函数:
    min ||X - X_S||² + ||X - X_T||² + λ||X||²
    subject to: X[observed] = X_missing[observed]
    """
    # 迭代优化
    X = initialize(X_missing)
    while not converged:
        # 空间视图更新
        X_S = update_from_spatial(X)
        # 时间视图更新
        X_T = update_from_temporal(X)
        # 融合
        X = α * X_S + (1-α) * X_T
        # 保持观测值不变
        X[observed] = X_missing[observed]
    return X
```

---

## 实验结果

### 数据集
- 北京空气质量监测站数据
- 多个传感器，长时间序列
- 真实缺失模式

### 性能对比
| 方法 | MAE | RMSE |
|------|-----|------|
| Mean填充 | 较高 | 较高 |
| KNN | 中等 | 中等 |
| Matrix Factorization | 中等 | 中等 |
| **ST-MVL** | **最低** | **最低** |

### 处理块状缺失
- 传统方法在块状缺失时性能急剧下降
- ST-MVL通过空间视图有效缓解该问题

---

## 与其他论文的关系

### 相关工作
- **U-Air (KDD 2013)**: 空气质量推断，需要完整数据
- **Forecasting Air Quality (KDD 2015)**: 预测前需要数据修复

### 技术关联
```
数据准备链:
ST-MVL(修复) → U-Air(推断) → Forecast(预测)
```

---

## 对虚拟用户研究的启发

### 1. 多视图数据融合
```
Geo-Sensory数据缺失 → 虚拟用户数据不完整

空间视图              → 群体视图
(邻居传感器)          (相似用户行为)

时间视图              → 历史视图
(历史模式)            (用户历史行为)
```

### 2. 数据增强策略
```python
def virtual_user_data_completion(incomplete_data):
    """
    虚拟用户数据补全
    """
    # 群体视图: 相似用户如何操作
    group_view = aggregate_similar_users(incomplete_data)
    
    # 历史视图: 该用户历史如何操作
    history_view = retrieve_history(incomplete_data.user_id)
    
    # 联合优化
    completed = joint_optimize(
        incomplete_data, 
        group_view, 
        history_view
    )
    return completed
```

### 3. 块状缺失处理
**场景**: 虚拟用户某些功能完全未测试
```
问题: 功能A从未被测试
传统: 无法推断
ST-MVL思路: 
  - 空间: 测试过功能A的相似用户如何操作
  - 时间: 用户历史操作模式迁移
```

### 4. 时空数据质量
```
城市传感器数据         → 虚拟用户测试数据
- 传感器故障           → 测试用例遗漏
- 通信中断             → 日志丢失
- 维护停机             → 维护窗口
- 数据异常             → 异常行为
```

---

## 关键技术点

### 空间邻居选择
```python
def select_spatial_neighbors(target, candidates, k=5):
    """
    不仅考虑地理距离，还考虑数据相关性
    """
    # 地理距离
    geo_dist = [distance(target.loc, c.loc) for c in candidates]
    # 数据相关
    correlation = [pearson(target.series, c.series) for c in candidates]
    
    # 综合评分
    scores = [α/geo_d + β*corr for geo_d, corr in 
              zip(geo_dist, correlation)]
    
    return top_k(candidates, scores, k)
```

### 迭代优化策略
- EM风格迭代
- 每次迭代更新一个视图
- 观测值保持不变
- 收敛判据：变化小于阈值

---

## 总结

**核心思想**: 利用时空数据的互补性，多视图联合优化填充缺失值

**方法论贡献**:
1. 空间-时间双视图框架
2. 联合优化目标函数
3. 块状缺失处理方案

**对虚拟用户的启示**:
1. 数据不完整是常态，需要补全策略
2. 多视图融合提升鲁棒性
3. 群体智慧+个体历史是有效的补全信息源
