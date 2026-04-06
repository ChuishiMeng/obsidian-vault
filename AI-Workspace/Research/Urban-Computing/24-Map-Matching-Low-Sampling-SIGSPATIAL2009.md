# Map-Matching for Low-Sampling-Rate GPS Trajectories
**会议**: ACM SIGSPATIAL 2009  
**作者**: Yin Lou, Chengyang Zhang, Yu Zheng, Xing Xie, Wei Wang, Yan Huang  
**领域**: 轨迹处理 / 地图匹配 / 路网映射

---

## 核心贡献

### 问题定义
**地图匹配**: 将GPS点序列映射到道路网络上的过程

**核心挑战**:
- **低采样率**: GPS点间隔大（2-5分钟），中间路径不确定
- **GPS误差**: 10-20米定位误差
- **道路复杂**: 平行路、高架桥、隧道等

### 提出方法: ST-Matching
**Spatio-Temporal Matching**: 结合空间和时序信息的地图匹配算法

---

## 核心挑战分析

### 低采样率问题
```
高采样率(10秒):
  A──→──→──→──B  路径清晰

低采样率(2分钟):
  A──────────→B  中间路径不确定
  可能在主路，可能在辅路
```

### 三种错误场景
```
1. 平行道路混淆
   主路 ══════
   辅路 ──────  GPS点漂移

2. 路口选择错误
        ↗
   ──→ ● ──→
        ↘

3. 高架桥 vs 地面
   高架 ═══
   地面 ───  GPS高度不准确
```

---

## ST-Matching 方法论

### 整体框架

```
┌─────────────────────────────────────────────────────┐
│                   ST-Matching 流程                  │
├─────────────────────────────────────────────────────┤
│  GPS轨迹 P = {p₁, p₂, ..., pₙ}                      │
│          │                                          │
│          ▼                                          │
│  ┌──────────────┐                                   │
│  │ 候选路段选择 │  为每个点找k个候选路段            │
│  └──────┬───────┘                                   │
│         │                                           │
│         ▼                                           │
│  ┌──────────────┐                                   │
│  │ 空间分析     │  计算观测概率 P(pᵢ|cᵢⱼ)          │
│  └──────┬───────┘                                   │
│         │                                           │
│         ▼                                           │
│  ┌──────────────┐                                   │
│  │ 时间分析     │  计算转移概率 P(cᵢⱼ|cᵢ₋₁ₖ)       │
│  └──────┬───────┘                                   │
│         │                                           │
│         ▼                                           │
│  ┌──────────────┐                                   │
│  │ 路径推理     │  找最优匹配路径                   │
│  └──────────────┘                                   │
└─────────────────────────────────────────────────────┘
```

### 1. 候选路段选择
```python
def find_candidate_segments(gps_point, road_network, r=50):
    """
    找GPS点周围r米内的所有路段
    """
    candidates = []
    for segment in road_network:
        dist = point_to_segment_distance(gps_point, segment)
        if dist <= r:
            candidates.append({
                'segment': segment,
                'projection': project_point(gps_point, segment),
                'distance': dist
            })
    return candidates
```

### 2. 空间分析 - 观测概率
```python
def observation_probability(gps_point, candidate):
    """
    P(pᵢ|cᵢⱼ): GPS点在候选路段的概率
    假设GPS误差服从高斯分布
    """
    d = candidate['distance']  # GPS点到路段距离
    
    # 高斯分布
    σ = 20  # GPS误差标准差
    P_obs = (1/(√(2π)σ)) * exp(-d²/(2σ²))
    
    return P_obs
```

### 3. 时间分析 - 转移概率
```python
def transition_probability(c_prev, c_curr, v_gps):
    """
    P(cᵢⱼ|cᵢ₋₁ₖ): 从上一候选路段转移到当前的概率
    考虑行驶距离与时间的一致性
    """
    # 路网上最短距离
    d_route = shortest_path_distance(c_prev, c_curr)
    
    # GPS计算的距离（基于速度和时间）
    d_gps = v_gps * time_diff(c_prev, c_curr)
    
    # 距离差
    Δd = abs(d_route - d_gps)
    
    # 指数分布
    β = 0.5  # 参数
    P_trans = exp(-Δd / β)
    
    return P_trans
```

### 4. 路径推理
```python
def find_optimal_path(candidates_all, N):
    """
    动态规划找最优匹配路径
    """
    # V[i][j]: 到达第i个点的第j个候选的最大概率
    # prev[i][j]: 记录路径
    
    V = [[0]*k for _ in range(N)]
    prev = [[None]*k for _ in range(N)]
    
    # 初始化
    for j in range(k):
        V[0][j] = candidates_all[0][j]['P_obs']
    
    # 递推
    for i in range(1, N):
        for j in range(len(candidates_all[i])):
            c_curr = candidates_all[i][j]
            best_prob = 0
            best_prev = None
            
            for l in range(len(candidates_all[i-1])):
                c_prev = candidates_all[i-1][l]
                prob = V[i-1][l] * \
                       transition_probability(c_prev, c_curr) * \
                       c_curr['P_obs']
                
                if prob > best_prob:
                    best_prob = prob
                    best_prev = l
            
            V[i][j] = best_prob
            prev[i][j] = best_prev
    
    # 回溯
    return backtrace(V, prev)
```

---

## 实验结果

### 数据集
- 西雅图真实GPS轨迹
- 采样率: 30秒 - 5分钟

### 性能
| 方法 | 准确率 | 
|------|--------|
| 几何方法(最近邻) | ~60% |
| 几何+拓扑 | ~70% |
| **ST-Matching** | **~85%** |

### 优势场景
- 低采样率轨迹
- 复杂道路网络
- GPS误差较大

---

## 与其他论文的关系

### 技术关联
```
前置: GPS轨迹收集
      ↓
本篇: Map-Matching (SIGSPATIAL 2009)
      ↓ 提供准确的轨迹数据
      
后续: 
  - T-Drive (SIGSPATIAL 2010): 基于匹配轨迹的导航
  - Travel Time Estimation (KDD 2014): 更准确的时间估计
  - Trajectory Mining: 更可靠的挖掘结果
```

---

## 对虚拟用户研究的启发

### 1. 离散数据到连续路径
```
低采样GPS点          → 稀疏用户行为记录
地图匹配             → 行为路径重构

GPS点: 14:00→14:05→14:10
行为: 登录→???→下单

需要推断中间步骤！
```

**应用**:
```python
def reconstruct_user_path(sparsely_observed_actions, all_possible_paths):
    """
    重构用户行为路径（类似地图匹配）
    """
    # 观测概率: 用户在状态s执行动作a的概率
    P_obs = compute_observation_prob()
    
    # 转移概率: 从状态s1到s2的概率
    P_trans = compute_transition_prob()
    
    # 动态规划找最可能路径
    optimal_path = viterbi(sparsely_observed_actions, P_obs, P_trans)
    
    return optimal_path
```

### 2. 概率推理框架
```
ST-Matching三要素:
1. 候选生成（空间范围）
2. 观测概率（局部合理性）
3. 转移概率（全局一致性）

虚拟用户行为建模:
1. 可能行为生成（界面可达）
2. 单步合理性（操作意图）
3. 序列一致性（任务逻辑）
```

### 3. 不确定性处理
```python
def handle_uncertainty(observed_actions):
    """
    处理行为数据的不确定性
    """
    # 不返回单一结果，而是概率分布
    paths_with_probs = []
    for path in candidate_paths(observed_actions):
        prob = compute_path_probability(path, observed_actions)
        paths_with_probs.append((path, prob))
    
    return sorted(paths_with_probs, key=lambda x: -x[1])
```

### 4. 数据预处理重要性
```
GPS轨迹 → 地图匹配 → 后续分析
用户日志 → 行为对齐 → 后续分析

高质量预处理 = 高质量结果
```

---

## 关键技术点

### 候选点过滤
```python
def filter_candidates(gps_point, candidates, heading):
    """
    根据行驶方向过滤不合理的候选
    """
    filtered = []
    for c in candidates:
        # 候选路段方向应与行驶方向一致
        if angle_diff(c.segment.heading, heading) < 90:
            filtered.append(c)
    return filtered
```

### 路网距离计算
```python
def shortest_path_distance(start_seg, end_seg, road_network):
    """
    Dijkstra或A*计算路网最短距离
    """
    # 构建图
    G = build_graph(road_network)
    
    # A*搜索
    dist = a_star(G, start_seg.end_node, end_seg.start_node)
    
    return dist + start_seg.remaining + end_seg.remaining
```

---

## 总结

**核心思想**: 利用时空约束的概率模型处理低采样率轨迹匹配

**方法论贡献**:
1. 候选-观测-转移三阶段框架
2. 高斯观测概率 + 指数转移概率
3. 动态规划最优路径搜索

**对虚拟用户的启示**:
1. 稀疏行为数据可重构完整路径
2. 概率推理框架处理不确定性
3. 数据预处理是分析的基础
