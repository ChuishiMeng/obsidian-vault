# Andrej Karpathy 信息源完整收集指南

> 最后更新：2026-03-23

---

## 📱 主要活跃信息源

### 1. Bear Blog（当前主要）
- **URL**: https://karpathy.bearblog.dev/blog/
- **RSS**: https://karpathy.bearblog.dev/blog/?format=rss
- **状态**: ✅ 活跃（2025年3月开始）
- **内容**: 深度思考、技术文章、年度回顾
- **更新频率**: 每月2-4篇

**最新文章**（截至2025年12月）：
- 2025 LLM Year in Review（2025-12-20）
- Chemical hygiene（2025-12-19）
- Auto-grading decade-old Hacker News discussions（2025-12-10）
- The space of minds（2025-11-30）
- Verifiability（2025-11-18）

### 2. YouTube 频道
- **URL**: https://www.youtube.com/@AndrejKarpathy
- **RSS**: 通过 RSSHub: https://rsshub.app/youtube/channel/UCr8R1hRq8oK0rKcG3l3Q6Xg
- **状态**: ✅ 活跃
- **内容**: 教育视频、技术教程

**主要系列**：
- **Zero to Hero**: 神经网络从零开始
- **Deep Dive into LLMs**: LLM 深度解析
- **How I use LLMs**: 实用指南

### 3. Twitter/X
- **URL**: https://x.com/karpathy
- **RSS**: 通过 Nitter: https://nitter.net/karpathy/rss
- **状态**: ✅ 非常活跃
- **内容**: 实时想法、行业洞察、技术分享

**近期热点**：
- 2025年12月：宣布不再亲自写代码，转向 AI Agent 指挥
- "9级地震"推文引发硅谷讨论

---

## 📚 历史信息源

### 4. GitHub Blog
- **URL**: https://karpathy.github.io/
- **RSS**: https://karpathy.github.io/feed.xml
- **状态**: ⏸️ 历史（2012-2022）
- **经典文章**:
  - The Unreasonable Effectiveness of RNNs (2015)
  - Software 2.0 (2017)
  - A Recipe for Training Neural Networks (2019)

### 5. Medium
- **URL**: https://karpathy.medium.com
- **RSS**: https://karpathy.medium.com/feed
- **状态**: ⏸️ 历史
- **内容**: Software 2.0 等早期文章

### 6. GitHub 仓库
- **URL**: https://github.com/karpathy
- **RSS**: https://github.com/karpathy.atom
- **状态**: ✅ 活跃
- **热门项目**:
  - nanoGPT
  - micrograd
  - arxiv-sanity
  - llm.c

---

## 🎙️ 访谈和播客

### 近期访谈（2024-2025）

| 日期 | 节目 | 主题 | 链接 |
|------|------|------|------|
| 2025 | Dwarkesh Podcast | Eureka Labs 愿景 | [YouTube](https://www.youtube.com/watch?v=lXUZvyajciY) |
| 2025 | YC AI Startup School | AI 创业建议 | [YouTube](https://www.youtube.com/watch?v=LCEmiRjPEtQ) |
| 2024 | GPU Mode | GPU 优化 | [YouTube](https://www.youtube.com/watch?v=FH5wiwOyPX4) |
| 2024 | No Priors Podcast | AI 研究趋势 | [YouTube](https://www.youtube.com/watch?v=hM_h0UA7upI) |
| 2022 | Lex Fridman Podcast | 深度访谈 | [YouTube](https://www.youtube.com/watch?v=cdiD-9MMpb0) |

---

## 🏢 Eureka Labs

### 公司信息
- **官网**: https://eurekalabs.ai/
- **GitHub**: https://github.com/EurekaLabsAI
- **状态**: 2024年7月创立

### 产品
- **LLM101n**: 从零构建 LLM 课程
- **nanochat**: 教学用全栈项目

---

## 🔧 信息源收集方案

### 推荐抓取策略

```yaml
优先级排序:
  1. Bear Blog RSS      # 深度文章
  2. YouTube RSS        # 视频更新
  3. Twitter (Tavily)   # 实时动态
  4. GitHub RSS         # 代码项目
  5. 播客监控           # 访谈更新
```

### 技术实现

**RSS 抓取**:
```bash
# Bear Blog（需要处理 403）
curl -H "User-Agent: Mozilla/5.0" https://karpathy.bearblog.dev/blog/?format=rss

# YouTube（通过 RSSHub）
curl https://rsshub.app/youtube/channel/UCr8R1hRq8oK0rKcG3l3Q6Xg

# GitHub
curl https://github.com/karpathy.atom
```

**Tavily 搜索备用**:
```bash
# 当 RSS 失败时搜索
TAVILY_API_KEY=xxx node search.mjs "Andrej Karpathy latest blog"
```

---

## 📊 更新频率统计

| 信息源 | 频率 | 可靠性 |
|--------|------|--------|
| Bear Blog | 每月2-4篇 | ⭐⭐⭐⭐⭐ |
| YouTube | 每月1-2个 | ⭐⭐⭐⭐ |
| Twitter | 每天多条 | ⭐⭐⭐ |
| GitHub | 不定期 | ⭐⭐⭐⭐ |
| 播客 | 每季度 | ⭐⭐ |

---

## 🎯 关键监控点

### 高优先级
1. **Bear Blog 新文章** - 深度技术洞察
2. **YouTube 新视频** - 教育内容
3. **Twitter 重大宣布** - 如 Eureka Labs 进展

### 中优先级
4. **GitHub 新项目** - 开源工具
5. **播客访谈** - 行业观点

### 低优先级
6. **Medium/GitHub Blog** - 历史内容，偶尔回顾

---

## 📝 备注

- Bear Blog RSS 有 403 限制，需要设置 User-Agent
- YouTube RSS 建议通过 RSSHub 或自建实例
- Twitter 建议直接使用 Tavily 搜索，Nitter 不稳定
- 播客更新需要手动监控或订阅频道 RSS

---

*最后更新：2026-03-23*
