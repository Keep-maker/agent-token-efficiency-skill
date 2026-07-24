# 工具安装与配置

## 本 Skill（agent-token-efficiency）

```bash
# Cursor — 项目级
git clone https://github.com/Keep-maker/agent-token-efficiency-skill.git .agents/skills/agent-token-efficiency

# Cursor — 全局
npx skills add Keep-maker/agent-token-efficiency-skill --agent cursor -g -y
```

## Ponytail

```bash
# 全局 · 所有 Agent（推荐）
npx skills add DietrichGebert/ponytail -g -a "*" -y

# Cursor 始终生效规则（可选，与 Skill 叠加）
# 复制仓库 .cursor/rules/ponytail.mdc → ~/.cursor/rules/ponytail.mdc
```

**Claude Code 插件：**

```
/plugin marketplace add DietrichGebert/ponytail
/plugin install ponytail@ponytail
```

## Caveman

```powershell
# Windows — 自动检测本机所有 Agent
irm https://raw.githubusercontent.com/JuliusBrussee/caveman/main/install.ps1 | iex
```

```bash
# 或 skills CLI 全局
npx skills add JuliusBrussee/caveman -g -a "*" -y
```

**启用：** `/caveman` 或说「talk like caveman」

## Headroom

```bash
# 若 litellm 编译卡住，先装 wheel：
pip install "litellm>=1.86.2,<2.0" --prefer-binary --index-url https://pypi.org/simple
pip install headroom-ai httpx[http2] --index-url https://pypi.org/simple

# 完整 extras（可选，体积大）
pip install "headroom-ai[all]"
```

```bash
# Cursor 集成
headroom wrap cursor          # 注册 rtk hook + 启动 proxy
headroom proxy --port 8787    # 仅代理
headroom mcp install          # MCP compress/retrieve/stats
```

**Cursor 自定义 API Base URL（走 proxy 压缩）：**

- OpenAI 兼容：`http://127.0.0.1:8787/v1`
- Anthropic：`http://127.0.0.1:8787`

## 互补工具（可选）

| 工具 | 安装 | 作用 |
|------|------|------|
| karpathy-skills | `npx skills add forrestchang/andrej-karpathy-skills -g -y` | 减 over-engineering |
| claude-mem | `npx claude-mem install` | 跨会话记忆 + 三层检索 |
| MemPalace | `npx skills add milla-jovovich/mempalace --skill mempalace -g -y` | 结构化长期记忆 |
| caveman-shrink | `npm i -g caveman-shrink` | MCP description 压缩 |

## 验证安装

```bash
npx skills ls -g | findstr /i "ponytail caveman agent-token"
headroom --version
Test-Path "$env:USERPROFILE\.agents\skills\ponytail\SKILL.md"   # PowerShell
```

## 卸载

```bash
npx skills remove ponytail caveman agent-token-efficiency -g -y
headroom unwrap cursor
pip uninstall headroom-ai -y
```
