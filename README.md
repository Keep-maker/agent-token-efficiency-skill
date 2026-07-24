# agent-token-efficiency-skill

**Agent Token 效率综合 Cursor Skill** — 整合 Ponytail、Caveman、Headroom「三件套」及输入/输出/工作流全链路省 token 策略，在**不牺牲正确性、安全性与可验证性**的前提下降低 API 成本。

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![GitHub](https://img.shields.io/badge/GitHub-Keep--maker/agent--token--efficiency--skill-181717?style=flat&logo=github)](https://github.com/Keep-maker/agent-token-efficiency-skill)
[![Gitee](https://img.shields.io/badge/Gitee-keep--maker/agent--token--efficiency--skill-C71D23?style=flat&logo=gitee)](https://gitee.com/keep-maker/agent-token-efficiency-skill)
[![skills.sh](https://img.shields.io/badge/skills.sh-agent--token--efficiency-000000?style=flat&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIxNiIgaGVpZ2h0PSIxNiIgZmlsbD0iI2ZmZiI+PHBhdGggZD0iTTggMEw2IDEwaDR6Ii8+PC9zdmc+)](https://skills.sh/Keep-maker/agent-token-efficiency-skill/agent-token-efficiency)

## 镜像仓库

| 平台 | 地址 |
|------|------|
| GitHub | https://github.com/Keep-maker/agent-token-efficiency-skill |
| Gitee | https://gitee.com/keep-maker/agent-token-efficiency-skill |

```bash
# GitHub
git clone https://github.com/Keep-maker/agent-token-efficiency-skill.git

# Gitee（国内访问更快）
git clone https://gitee.com/keep-maker/agent-token-efficiency-skill.git
```

CC Switch 也可添加 **Gitee 自定义仓库**：Owner `keep-maker`，Name `agent-token-efficiency-skill`，Branch `main`，Subdirectory `skills`。

## 网站（在线演示）

| 平台 | 地址 | 说明 |
|------|------|------|
| **GitHub Pages** | https://keep-maker.github.io/agent-token-efficiency-skill/ | 已部署，全球可访问 |
| **Gitee Pages** | https://keep-maker.gitee.io/agent-token-efficiency-skill/ | 国内更快，需在仓库「服务 → Gitee Pages」启动 |
| **Cloudflare Pages** | 配置 Token 后自动部署 | 见 [docs/DEPLOY.md](docs/DEPLOY.md) |

站点为纯静态页（`docs/index.html`），已优化：无 Google Fonts 依赖、系统字体、CDN 缓存头。

## skills.sh 与 CC Switch 说明

> **重要**：CC Switch 的「skills.sh 搜索」走的是 skills.sh **在线索引 API**，不是 GitHub 实时搜索。新仓库需要等待索引同步（或安装量积累），**短期内搜不到是正常的**。

### 方式一：命令行安装（立即可用，推荐）

```bash
npx skills add Keep-maker/agent-token-efficiency-skill --agent cursor -y
```

### 方式二：CC Switch 添加 GitHub 仓库（立即可用）

skills.sh 搜不到时，用**自定义仓库**绕过索引：

1. 打开 CC Switch → **Skills 管理** → **仓库管理**
2. 点击 **添加仓库**，填写：

| 字段 | 值 |
|------|-----|
| Owner | `Keep-maker` |
| Name | `agent-token-efficiency-skill` |
| Branch | `main` |
| Subdirectory | `skills` |

3. 保存后回到 Skills 列表，从该仓库安装 `agent-token-efficiency`

### 方式三：skills.sh 搜索（需等待索引）

索引同步后可搜索关键词：

- `agent-token-efficiency`（Skill 名，不是仓库名）
- `token-efficiency`

- **Skill 页面**：https://skills.sh/Keep-maker/agent-token-efficiency-skill/agent-token-efficiency
- **索引请求**：https://github.com/vercel-labs/skills/issues/1768

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
| 三件套 | [skills/agent-token-efficiency/references/three-pillars.md](skills/agent-token-efficiency/references/three-pillars.md) | Ponytail / Caveman / Headroom 分工与叠加 |
| 输入优化 | [skills/agent-token-efficiency/references/input-optimization.md](skills/agent-token-efficiency/references/input-optimization.md) | Headroom、@引用、ignore、记忆、MCP、RAG |
| 输出· prose | [skills/agent-token-efficiency/references/output-optimization.md](skills/agent-token-efficiency/references/output-optimization.md) | Caveman、Karpathy 原则、格式技巧 |
| 代码最小化 | [skills/agent-token-efficiency/references/code-minimization.md](skills/agent-token-efficiency/references/code-minimization.md) | Ponytail 七级梯、安全红线 |
| 工作流 | [skills/agent-token-efficiency/references/workflow-patterns.md](skills/agent-token-efficiency/references/workflow-patterns.md) | Skill 架构、记忆、Subagent、TDD |
| 工具安装 | [skills/agent-token-efficiency/references/tools-install.md](skills/agent-token-efficiency/references/tools-install.md) | 一键命令与验证 |
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

完整列表见 [skills/agent-token-efficiency/references/workflow-patterns.md](skills/agent-token-efficiency/references/workflow-patterns.md)。

## 安装

### Cursor（推荐）

```bash
# 全局
npx skills add Keep-maker/agent-token-efficiency-skill --agent cursor -g -y

# 或项目级（克隆后复制 skills/agent-token-efficiency 子目录）
git clone https://github.com/Keep-maker/agent-token-efficiency-skill.git
# 复制 skills/agent-token-efficiency → 项目 .agents/skills/agent-token-efficiency/
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
├── skills/
│   └── agent-token-efficiency/
│       ├── SKILL.md
│       ├── references/
│       └── examples/
├── skills.sh.json
├── README.md
├── LICENSE
└── package.json
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
