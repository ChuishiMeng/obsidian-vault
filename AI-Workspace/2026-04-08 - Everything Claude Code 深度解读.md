# Everything Claude Code 深度解读

> 来源：AI编程实验室 · 鲁工（九年AI算法老兵）
> 日期：2026-04-08
> GitHub：https://github.com/affaan-m/everything-claude-code

---

## 规模

- **36 个专用 subagent**，150+ skills，68 个命令，覆盖 10 种编程语言 rules
- 作者 Affaan Mushtaq，Anthropic 黑客松获奖者，10 个月实战打磨
- 跨平台：Claude Code、Codex、Cursor、OpenCode、Gemini 都能用
- 官网：https://ecc.tools/

---

## 三个亮点

### 1. pass@k / pass^k 验证指标

- pass@k：跑 k 次至少一次通过的概率
- pass^k：k 次全部通过的概率
- 数据：k=3 时 pass@k = 91%，但 pass^k 只有 34%
- 意义：判断 agent 输出是稳定可靠还是碰运气

### 2. AgentShield 安全集成

- 内置 102 条安全规则 + 1282 个安全测试
- `/security-scan` 命令，覆盖 OWASP 常见漏洞

### 3. 沙盒化 subagent

- 每个 subagent 有独立的工具权限限制
- 比如代码审查 agent 只能读不能写，防止多 agent 并行时误操作

---

## vs Superpowers 对比

| 维度 | Everything Claude Code | Superpowers |
|------|----------------------|-------------|
| **定位** | 装备齐全的工具箱 | 资深架构师 |
| **核心** | 场景化 agent + skills | 开发方法论 + 流程约束 |
| **流程** | 不管流程，给你工具 | brainstorm → plan → TDD → review |
| **优势** | 深度工程化、安全扫描 | 代码质量、流程成熟 |

**建议：两个都装，Superpowers 管流程，ECC 管工具。**

---

## 扣分项

- 学习成本高：36 agent / 150 skills / 68 命令，光搞清楚用途就要花时间
- 配置偏 Go 语言，其他语言深度有差距
- v1.9.0 起支持按需安装（只装 TypeScript + Python 的规则）

---

## 安装方式

```bash
# 简单安装（不装 rules）
/plugin marketplace add affaan-m/everything-claude-code
/plugin install everything-claude-code@everything-claude-code

# 全量安装（含 rules）
git clone https://github.com/affaan-m/everything-claude-code.git
cd everything-claude-code
npm install
./install.sh --profile full
```

---

## 评分

鲁工给了 **9/10 分**。