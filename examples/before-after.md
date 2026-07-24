# Before / After 示例

## 1. 输入：构建失败日志

**Before（灌入 8000 行 log）：**
```
Agent Read: npm run build
→ 全文 8000 行 stdout 进入 context
```

**After（Headroom + tail）：**
```
Agent Read: npm run build 2>&1 | tail -n 80
→ Headroom 再压缩 → ~200 token 等价信息
→ 模型仍可通过 retrieve 要完整 trace
```

---

## 2. 输出 prose：React 重渲染

**Before (~1180 tokens 级解释）：**
> The reason your React component is re-rendering is likely because you're creating a new object reference on each render cycle. When you pass an inline object as a prop…

**After（Caveman ~159 tokens 级）：**
> New ref each render. Inline object prop = new ref = re-render. Wrap in `useMemo`.

**代码块：** 两处 `useMemo` 示例 **完全相同**。

---

## 3. 输出 code：Date picker

**Before（ponytail 基准 ~404 LOC）：**
- 安装 flatpickr
- Wrapper 组件 + CSS + timezone 讨论

**After（~23 LOC）：**
```html
<input type="date" />
```

---

## 4. 用户 prompt

**Before：**
> 帮我看看项目，我想优化性能，你可以随便看，给我全面分析…（+ 粘贴 200 行无关配置）

**After：**
> @src/App.tsx @vite.config.ts 首屏 LCP 慢，只查 bundle 与 lazy load，给 top 3 fix。

---

## 5. 记忆文件

**Before：** `AGENTS.md` 706 tokens 散文规范

**After：** `/caveman-compress AGENTS.md` → ~285 tokens，代码块/路径不变

---

## 6. MCP 工具描述

**Before：** 每个 tool description 200 字教程 × 30 tools = 6000 token 级 schema

**After：** `caveman-shrink` 包装 MCP server，description 保留「何时用」一句

---

## 7. 多轮历史

**Before：** 同一 chat 30 轮，每轮重复贴相同错误

**After：** claude-mem search → 只注入 3 条相关 observation IDs 全文
