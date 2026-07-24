# agent-token-efficiency-skill

**Agent Token 效率综合 Cursor Skill** — 整合 Ponytail、Caveman、Headroom「三件套」及输入/输出/工作流全链路省 token 策略，在**不牺牲正确性、安全性与可验证性**的前提下降低 API 成本。

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![skills.sh](https://img.shields.io/badge/skills.sh-agent--token--efficiency-000000?style=flat&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIxNiIgaGVpZ2h0PSIxNiIgZmlsbD0iI2ZmZiI+PHBhdGggZD0iTTggMEw2IDEwaDR6Ii8+PC9zdmc+)](https://skills.sh/Keep-maker/agent-token-efficiency-skill/agent-token-efficiency)

## skills.sh 安装

本 Skill 已发布到 [skills.sh](https://skills.sh/) 生态，一键安装：

```bash
npx skills add Keep-maker/agent-token-efficiency-skill --agent cursor -y
```

- **Skill 页面**：https://skills.sh/Keep-maker/agent-token-efficiency-skill/agent-token-efficiency
- **搜索**：`npx skills find token-efficiency --owner Keep-maker`

## 为什么需要这个 Skill？

Agent 会话的 token 消耗通常来自三条管道：

```text
INPUT（读文件、日志、历史）  →  推理  →  OUTPUT（解释 + 代码）
     ↑ 常占 60–90%                    ↑ prose    ↑ code
     Headroom                         Caveman     Ponytail
```

市面工具多各自为战。本 Skill 把**原理、决策树、检查清单、安装命令**合成一份 Agent 可执行的 playbook，供 Cursor / Claude Code / Codex 等按需加载。

## 核心内容

| 模块 | 文件 | 说明 |
|------|------|------|
| 主 Skill | [SKILL.md](SKILL.md) | 诊断流程、决策树、红线、输出模板 |
| 三件套 | [references/three-pillars.md](references/three-pillars.md) | Ponytail / Caveman / Headroom 分工与叠加 |
| 输入优化 | [references/input-optimization.md](references/input-optimization.md) | Headroom、@引用、ignore、记忆、MCP、RAG |
| 输出· prose | [references/output-optimization.md](references/output-optimization.md) | Caveman、Karpathy 原则、格式技巧 |
| 代码最小化 | [references/code-minimization.md](references/code-minimization.md) | Ponytail 七级梯、安全红线 |
| 工作流 | [references/workflow-patterns.md](references/workflow-patterns.md) | Skill 架构、记忆、Subagent、TDD |
| 工具安装 | [references/tools-install.md](references/tools-install.md) | 一键命令与验证 |
| 示例 | [examples/before-after.md](examples/before-after.md) | 7 组前后对比 |

## 三件套速查

| 工具 | GitHub | 作用段 | 典型收益 |
|------|--------|--------|----------|
| **Headroom** | [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) | 进模型前的输入 | 上下文 **-60% ~ -95%** |
| **Caveman** | [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman) | 自然语言输出 | 输出 prose **~ -65%** |
| **Ponytail** | [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail) | 生成代码 | LOC **~ -54%**，tokens **~ -22%** |

三者**机制不重复**，推荐叠加。详见 [Headroom vs Caveman 对比文](https://pasqualepillitteri.it/en/news/5700/headroom-vs-caveman-compress-input-cut-output)。

## 其他省 Token 方法（不影响质量）

本 Skill 还覆盖以下**非三件套**策略：

- **渐进式披露**：claude-mem 三层检索（search → timeline → get_observations）
- **精准 @ 引用**：不粘贴整文件/整库
- **`.cursorignore`**：排除 node_modules、dist、大二进制
- **记忆文件 compress**：`/caveman-compress` 缩小 CLAUDE.md/AGENTS.md
- **MCP schema 压缩**：`caveman-shrink`
- **Karpathy 四原则**：少假设、要简单、surgical 改动、目标驱动
- **Subagent 隔离**：探索任务只回摘要
- **并行工具调用**：减少读-想-再读轮次
- **Skill 模块化**：避免巨型 always-on Rules
- **TDD / 明确 DoD**：减少写错重写循环
- **日志 tail / jq 过滤**：只把 ERROR 相关片段送入 context

完整列表见 [references/workflow-patterns.md](references/workflow-patterns.md)。

## 安装

### Cursor（推荐）

```bash
# 全局
npx skills add Keep-maker/agent-token-efficiency-skill --agent cursor -g -y

# 或项目级
git clone https://github.com/Keep-maker/agent-token-efficiency-skill.git .agents/skills/agent-token-efficiency
```

### 手动

复制本仓库到：

- 项目：`.agents/skills/agent-token-efficiency/`
- 全局：`~/.agents/skills/agent-token-efficiency/`（Windows：`%USERPROFILE%\.agents\skills\`）

### 三件套一键（可选，与本 Skill 配合）

```bash
npx skills add DietrichGebert/ponytail -g -a "*" -y
npx skills add JuliusBrussee/caveman -g -a "*" -y
pip install headroom-ai httpx[http2]
headroom wrap cursor
```

## 使用

在 Cursor 对话中：

```
@agent-token-efficiency 我的 Agent 账单很高，读日志特别多，怎么优化？
```

或自然语言：

- 「怎么省 token？」
- 「Headroom 和 Caveman 有什么区别？」
- 「帮我对照 checklist 审查当前工作流」

## 快速决策树

```text
账单高？
├─ 工具读回日志/大文件多 → Headroom + input-optimization
├─ 回复解释很长         → Caveman + output-optimization
├─ 生成代码臃肿         → Ponytail + code-minimization
├─ 多轮历史长           → 新会话 + claude-mem/MemPalace
└─ MCP 工具描述过长     → caveman-shrink
```

## 不可省的红线

以下**禁止**为省 token 而删除或弱化：

- 安全校验、鉴权、信任边界 validation
- 数据丢失防护
-  accessibility 必需项
- 测试断言与可复现步骤
- 代码、命令、路径、错误信息的**字节级准确性**

## 目录结构

```
agent-token-efficiency-skill/
├── SKILL.md
├── README.md
├── LICENSE
├── references/
│   ├── three-pillars.md
│   ├── input-optimization.md
│   ├── output-optimization.md
│   ├── code-minimization.md
│   ├── workflow-patterns.md
│   └── tools-install.md
└── examples/
    └── before-after.md
```

## 相关资源

- [2026 GitHub Agent Skill 趋势整理](https://github.com/Keep-maker/langgraph-learning-skill)（姊妹仓库作者同一账号）
- [skills.sh 生态](https://skills.sh/)
- [forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills)
- [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem)

## 贡献

Issue / PR 欢迎。请保持：可执行 checklist、可验证数据、与三件套原理一致。

## License

MIT — 见 [LICENSE](LICENSE)

## 作者

[Keep-maker](https://github.com/Keep-maker)
