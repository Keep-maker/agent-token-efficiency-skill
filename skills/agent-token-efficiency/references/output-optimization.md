# 输出侧·自然语言优化（OUTPUT prose）

## Caveman 核心规则

1. **结论先行** — 第一句是可执行答案
2. **去 filler** — 无「当然」「很高兴帮你」、无重复用户问题
3. **片段可接受** — 列表、短句、符号（🔴🟡）代替长段
4. **代码/命令/错误/URL/path — byte-exact，永不改写**

## 强度选择

| 级别 | 何时用 |
|------|--------|
| `lite` | 简单确认、是/否、单行 fix |
| `full` | 默认日常编码 |
| `ultra` | 机械批量任务 |
| `wenyan` | 实验性极简（文言文体，信息密度极高） |
| normal / off | 教学、文档、首次 onboarding |

## 格式技巧

| 技巧 | 示例 |
|------|------|
| 表格对比 | 方案 A/B 用表，不用两段 prose |
| 行号引用 | `L42: null guard missing` 而非重贴 20 行 |
| 一步一 bullet | 安装步骤用编号列表 |
| 折叠细节 | 「根因：X。修复：Y。」细节用户追问再展开 |
| Conventional Commit | `/caveman-commit` ≤50 字符 subject |

## Karpathy 四原则（间接减 prose）

来自 [andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills)：

1. **明确假设** — 少「我猜你是要…」的试探性长文
2. **简单优先** — 少「我建议引入 DDD 分层…」的过度方案叙述
3. **Surgical 改动** — 少「我还顺便重构了隔壁模块」的说明
4. **目标驱动** — 「测试通过即可」比 10 步操作手册短

## Subagent / 多 Agent

| 技巧 | 说明 |
|------|------|
| cavecrew-* | Caveman 子 agent，比 vanilla 少 ~60% token |
| 探索任务外置 | 搜索/调研在 subagent，主会话只收摘要 |
| 固定输出 schema | 「返回 {files, risk, next} 三字段」 |

## 何时**不要**压缩 prose

- 用户说「详细讲」「我是新手」「写文档」
- 安全/合规说明必须完整
- API  breaking change 迁移指南
- 代码审查需逐条 pedagogical 解释

## Before / After

**Before (69 tokens 级):**
> The reason your React component is re-rendering is likely because you're creating a new object reference on each render cycle…

**After (~19 tokens 级):**
> New object ref each render. Inline prop object → useMemo.

## 检查清单

- [ ] 首句是否已是答案？
- [ ] 是否有可删的礼貌套话？
- [ ] 代码块是否原样保留？
- [ ] 能否用表/列表代替段落？
- [ ] 用户是否明确要求 verbose？
