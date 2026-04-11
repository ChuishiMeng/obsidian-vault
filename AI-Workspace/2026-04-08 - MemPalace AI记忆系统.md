# MemPalace：生化危机女主用 Claude 手搓满分 AI 记忆系统

> 来源：新智元
> 日期：2026-04-08
> GitHub：https://github.com/milla-jovovich/mempalace

---

## 故事背景

《生化危机》《第五元素》女主 **Milla Jovovich**，白天拍戏走秀带娃，深夜用 Claude Code 写代码，跟工程师好友 Ben Sigman 一起做出了 **MemPalace** — 一个 AI 长期记忆系统。

在公认最严苛的长期记忆基准 **LongMemEval** 上，**500 题全对，全球首个满分**。

GitHub 已获 17.9K stars。

---

## MemPalace 架构

借鉴古希腊「记忆宫殿」技巧，把 AI 的记忆结构化为虚拟空间：

| 层级 | 概念 | 作用 |
|------|------|------|
| **翼楼 (Wing)** | 项目/人 | 顶层分类 |
| **房间 (Room)** | 主题 | 按主题归类 |
| **走廊 (Hall)** | 记忆类型 | 决策/里程碑/偏好/建议/发现 |
| **隧道 (Tunnel)** | 跨翼楼关联 | 同名房间自动打通 |
| **衣柜/抽屉** | 摘要/原文 | 分层存储，按需检索 |

---

## 核心数据

- 全库搜索召回率 60.9% → 加结构过滤后 **94.8%**
- 本地 ChromaDB 存储，不调 API，**一年 0.7 美元**
- 支持 MCP，一行命令接入 Claude/ChatGPT/Cursor

---

## AAAK：AI 专用压缩方言

把 1000 token 的信息压缩到 ~120 token，信息基本无损，任何大模型都能直接读。

示例：
```
TEAM: PRI(lead) | KAI(backend,3yr) SOR(frontend) MAY(infra) LEO(junior,new)
PROJ: DRIFTWOOD(saas.analytics) | SPRINT: auth.migration→clerk
DECISION: KAI.rec:clerk>auth0(pricing+dx) | ★★★★
```

---

## ⚠️ 社区 48 小时扒皮

开源社区发现夸大宣传：

| 宣传 | 实际 |
|------|------|
| 「30 倍无损压缩」 | 示例用假 tokenizer，实际反而多了 token |
| AAAK 无损 | 有损，AAAK 模式 84.2% vs raw 模式 96.6% |
| 「+34% 宫殿增益」 | 标准 ChromaDB 元数据过滤，非独创 |
| 矛盾检测自动运行 | fact_checker.py 根本没接入流程 |

**但作者做了一件体面的事**：没删评论没辩解，直接在项目顶部贴公开信逐条认错。社区反而因此更认可这个项目。

---

## 三步上手

```bash
pip install mempalace
mempalace init ~/projects/myapp
mempalace mine ~/chats/ --mode convos

# 接 Claude（MCP）
claude mcp add mempalace -- python -m mempalace.mcp_server
```

---

## 意义

不只是技术本身，而是「开发者」的边界真的在消失。MemPalace 的结构化记忆思路（翼楼→房间→走廊→隧道）值得借鉴，跟 OpenClaw 的 MEMORY.md 方案是不同路线的解法。