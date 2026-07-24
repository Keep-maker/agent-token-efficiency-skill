# 网站部署指南

本项目静态站点位于 `docs/`，零构建、零依赖。

## 在线地址

| 平台 | 地址 | 适用场景 |
|------|------|----------|
| **GitHub Pages** | https://keep-maker.github.io/agent-token-efficiency-skill/ | 海外 / 全球 |
| **Gitee Pages** | https://keep-maker.gitee.io/agent-token-efficiency-skill/ | 国内（需手动开启，见下） |
| **Cloudflare Pages** | 配置 Token 后自动部署 | 全球 CDN 加速 |

## 速度优化（已内置）

- 移除 Google Fonts，改用系统字体栈（国内首屏更快）
- 单文件 HTML + 内联 CSS，无 JS 框架
- `docs/_headers` 配置 CDN 缓存（Cloudflare Pages 生效）

## 1. GitHub Pages（已启用）

仓库 Settings → Pages → Source: `main` / `docs`

推送 `main` 分支后自动更新，通常 1–2 分钟生效。

## 2. Gitee Pages（国内推荐）

1. 打开 https://gitee.com/keep-maker/agent-token-efficiency-skill
2. 点击 **服务** → **Gitee Pages**
3. 分支选 `main`，目录选 **docs**（或 `/docs`）
4. 点击 **启动** / **更新**

> Gitee Pages 免费版需手动点「更新」才会同步最新代码。

## 3. Cloudflare Pages（可选，全球 CDN）

### 方式 A：GitHub Actions（推荐）

1. 注册 [Cloudflare](https://dash.cloudflare.com/)（免费）
2. 创建 API Token：My Profile → API Tokens → Edit Cloudflare Workers → Pages:Edit
3. 在 GitHub 仓库 Settings → Secrets 添加：
   - `CLOUDFLARE_API_TOKEN`
   - `CLOUDFLARE_ACCOUNT_ID`（Dashboard 右侧可见）
4. 推送代码后 workflow 自动部署，或 Actions 里手动运行 **Deploy to Cloudflare Pages**

### 方式 B：本地一键

```bash
npx wrangler pages deploy docs --project-name=agent-token-efficiency
# 需先 wrangler login 或设置 CLOUDFLARE_API_TOKEN
```

部署后地址形如：`https://agent-token-efficiency.pages.dev`

## 本地预览

```bash
cd docs && python -m http.server 8765
# 打开 http://localhost:8765
```
