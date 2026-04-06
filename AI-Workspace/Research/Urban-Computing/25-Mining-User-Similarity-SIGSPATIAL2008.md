# Mining User Similarity Based on Location History
**会议**: ACM SIGSPATIAL 2008  
**作者**: Yu Zheng, Xing Xie, Wei-Ying Ma  
**领域**: 用户建模 / 相似性计算 / 位置服务

---

## 核心贡献

### 问题定义
如何基于用户的**位置历史**来度量用户之间的相似性？

### 核心洞察
```
传统用户相似性: 基于人口统计、购买行为
位置相似性: 用户去过的地方反映兴趣和生活方式

两个经常去相同地方的人 → 更可能有相似兴趣
```

---

## 方法论

### 整体框架

```
┌────────────────────────────────────────────────────────┐
│            位置历史相似性计算框架                       │
├────────────────────────────────────────────────────────┤
│  用户位置历史                                          │
│       │                                               │
│       ▼                                               │
│  ┌─────────────┐                                      │
│  │ 停留点检测  │  提取有意义的停留位置                │
│  └──────┬──────┘                                      │
│         │                                             │
│         ▼                                             │
│  ┌─────────────┐                                      │
│  │ 位置聚类    │  形成语义位置                        │
│  └──────┬──────┘                                      │
│         │                                             │
│         ▼                                             │
│  ┌─────────────┐                                      │
│  │ 序列建模    │  构建位置序列                        │
│  └──────┬──────┘                                      │
│         │                                             │
│         ▼                                             │
│  ┌─────────────┐                                      │
│  │ 相似性计算  │  多维度度量用户相似                  │
│  └─────────────┘                                      │
└────────────────────────────────────────────────────────┘
```

### 1. 位置历史表示
```python
class LocationHistory:
    """
    用户位置历史
    """
    def __init__(self, user_id):
        self.user_id = user_id
        self.stay_points = []      # 停留点序列
        self.locations = []         # 聚类后的语义位置
        self.sequences = []         # 位置序列
    
    def add_stay_point(self, point):
        self.stay_points.append(point)
```

### 2. 多维度相似性度量

#### 2.1 位置重合度
```python
def location_similarity(user1, user2):
    """
    Jaccard相似度: 共同访问位置
    """
    locs1 = set(user1.locations)
    locs2 = set(user2.locations)
    
    intersection = locs1 & locs2
    union = locs1 | locs2
    
    return len(intersection) / len(union) if union else 0
```

#### 2.2 时间模式相似性
```python
def temporal_similarity(user1, user2):
    """
    访问时间模式相似性
    同一位置在不同时间的访问
    """
    common_locs = set(user1.locations) & set(user2.locations)
    
    similarities = []
    for loc in common_locs:
        # 提取访问时间分布
        times1 = extract_visit_times(user1, loc)
        times2 = extract_visit_times(user2, loc)
        
        # 比较时间分布
        sim = compare_distributions(times1, times2)
        similarities.append(sim)
    
    return mean(similarities) if similarities else 0
```

#### 2.3 序列模式相似性
```python
def sequence_similarity(user1, user2):
    """
    位置序列的相似性
    """
    # 最长公共子序列
    lcs = longest_common_subsequence(
        user1.sequences, 
        user2.sequences
    )
    
    # 归一化
    max_len = max(len(user1.sequences), len(user2.sequences))
    
    return len(lcs) / max_len if max_len > 0 else 0
```

#### 2.4 层次化相似性
```python
def hierarchical_similarity(user1, user2, location_hierarchy):
    """
    考虑位置的层次结构（区→城市→省）
    精确匹配权重更高
    """
    total_score = 0
    for loc1 in user1.locations:
        best_match = 0
        for loc2 in user2.locations:
            # 计算层次匹配程度
            match_level = find_common_ancestor(
                loc1, loc2, location_hierarchy
            )
            # 越底层匹配分数越高
            score = 1.0 / match_level.depth
            best_match = max(best_match, score)
        total_score += best_match
    
    return total_score / len(user1.locations)
```

### 3. 综合相似性
```python
def overall_similarity(user1, user2, weights):
    """
    加权综合多个维度的相似性
    """
    sim_loc = location_similarity(user1, user2)
    sim_time = temporal_similarity(user1, user2)
    sim_seq = sequence_similarity(user1, user2)
    sim_hier = hierarchical_similarity(user1, user2)
    
    overall = (weights['location'] * sim_loc +
               weights['temporal'] * sim_time +
               weights['sequence'] * sim_seq +
               weights['hierarchy'] * sim_hier)
    
    return overall
```

---

## 实验结果

### 数据集
- 微软GeoLife项目GPS轨迹
- 多个用户，长期位置历史

### 应用验证
- **朋友推荐**: 相似用户可能成为朋友
- **地点推荐**: 推荐相似用户去过的地方

### 发现
- 位置相似性比传统方法更准确
- 多维度融合优于单一维度
- 层次化匹配提升泛化能力

---

## 与其他论文的关系

### 技术关联
```
前置: GPS轨迹收集、停留点检测
      ↓
本篇: User Similarity (SIGSPATIAL 2008)
      ↓ 提供用户相似性度量
      
后续应用:
  - Travel Time Estimation: 相似用户路径
  - 推荐系统: 协同过滤基础
  - 社交网络: 朋友推荐
```

---

## 对虚拟用户研究的启发

### 1. 用户相似性建模
```
位置历史相似性        → 行为历史相似性

去过的地方            → 执行过的操作
访问时间模式          → 操作时间模式
位置序列              → 操作序列
```

**应用**:
```python
def behavior_similarity(virtual_user1, virtual_user2):
    """
    虚拟用户行为相似性
    """
    # 功能使用相似性
    features1 = set(virtual_user1.used_features)
    features2 = set(virtual_user2.used_features)
    sim_feature = jaccard(features1, features2)
    
    # 操作序列相似性
    seq1 = virtual_user1.action_sequence
    seq2 = virtual_user2.action_sequence
    sim_sequence = lcs_similarity(seq1, seq2)
    
    # 时间模式相似性
    time1 = extract_time_pattern(virtual_user1)
    time2 = extract_time_pattern(virtual_user2)
    sim_time = compare_time_patterns(time1, time2)
    
    return weighted_combine(sim_feature, sim_sequence, sim_time)
```

### 2. 多维度用户画像
```python
class VirtualUserProfile:
    """
    虚拟用户画像（类比位置历史）
    """
    def __init__(self, user_id):
        self.user_id = user_id
        
        # 类比: 去过的地方 → 用过的功能
        self.visited_features = []  # 访问的功能
        
        # 类比: 访问时间 → 使用时间
        self.usage_patterns = {}    # 时间模式
        
        # 类比: 位置序列 → 操作序列
        self.action_sequences = []  # 行为序列
        
        # 类比: 位置层次 → 功能层次
        self.feature_hierarchy = {} # 功能层次关系
```

### 3. 协同过滤推荐
```
位置推荐:
  用户A去过故宫、颐和园
  用户B去过故宫
  → 推荐颐和园给用户B

功能推荐:
  用户A用过功能X、Y
  用户B用过功能X
  → 推荐功能Y给用户B
```

### 4. 用户聚类
```python
def cluster_virtual_users(users, similarity_func):
    """
    基于行为相似性聚类虚拟用户
    """
    # 计算相似性矩阵
    n = len(users)
    similarity_matrix = [[0]*n for _ in range(n)]
    
    for i in range(n):
        for j in range(i+1, n):
            sim = similarity_func(users[i], users[j])
            similarity_matrix[i][j] = sim
            similarity_matrix[j][i] = sim
    
    # 层次聚类
    clusters = hierarchical_clustering(similarity_matrix, threshold=0.7)
    
    return clusters
```

---

## 关键技术点

### 停留点语义标注
```python
def annotate_location(stay_point, poi_database):
    """
    给位置赋予语义标签
    """
    nearby_pois = find_nearby_pois(stay_point, poi_database, radius=200)
    
    if nearby_pois:
        # 选择最近或最相关的POI
        return most_relevant_poi(nearby_pois)
    else:
        return "Unknown Location"
```

### 时间分布比较
```python
def compare_distributions(times1, times2):
    """
    比较两个时间分布
    使用KL散度或直方图交集
    """
    # 转换为小时直方图
    hist1 = compute_hour_histogram(times1)
    hist2 = compute_hour_histogram(times2)
    
    # 直方图交集
    intersection = sum(min(h1, h2) for h1, h2 in zip(hist1, hist2))
    
    return intersection / 24  # 归一化
```

---

## 总结

**核心思想**: 位置历史反映用户兴趣和生活方式，可据此度量用户相似性

**方法论贡献**:
1. 多维度相似性度量框架
2. 层次化位置匹配
3. 时间模式相似性

**对虚拟用户的启示**:
1. 行为历史 = 用户画像的丰富数据源
2. 多维度相似性更准确
3. 可用于用户聚类和个性化推荐
