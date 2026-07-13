# 黑金方正 · Hugo 个人博客设计

日期：2026-07-13
仓库：`HFLYHYZQ/web`（GitHub Pages）
访问地址：`https://HFLYHYZQ.github.io/web/`
Hugo 版本：v0.164.0 extended（本机已装，winget）

## 1. 目标

用 Hugo 搭建一个自定义风格（不套现成主题）的个人博客，部署到 GitHub Pages。视觉为"黑金方正"风格，首页带开屏动画。

## 2. 视觉设计

### 2.1 配色
- 背景纯黑：`#0a0a0a`
- 卡片/分层底：`#111111`、`#1a1a1a`
- 主色金属金：`#d4af37`，高光金 `#f0d97a`
- 正文：`#e8e8e8`（米白偏暖）
- 边框：`rgba(212,175,55,0.3)` 细金线

### 2.2 造型"方正"
- 全局 `border-radius: 0`（直角）
- 细金线边框、栅格化布局、硬朗几何

### 2.3 字体
- 标题：思源宋体（Source Han Serif），接近方正书宋气质
- 正文：思源黑体（Source Han Sans）
- 开源免授权可商用；后续可替换为授权的方正字库字体文件

## 3. 开屏动画

- **每次进入首页都展示**（不区分访客/首次，不用 localStorage 门控）。
- `璇女.png` 全屏 cover 背景 + 黑色渐变遮罩压暗。
- 正中一个金线方框"共赏"按钮：默认透明底金线金字，hover 时金色填充、黑字。
- 点击「共赏」→ 开屏淡出 → 露出首页内容。
- 实现为首页模板内的覆盖层（CSS + 少量 JS），非独立路由。

## 4. 首页（导航页）

- 璇女图作淡化背景纹理。
- 4 个方形导航卡，2×2 栅格（移动端单列），金线边框、hover 金光描边：
  - **关于** → `/about/`
  - **设置** → `/settings/`（先占位）
  - **友情链接** → `/links/`
  - **前情提要** → `/posts/`（文章列表）

## 5. 子页面

- **关于 / 友情链接 / 设置**：单栏长文页，黑底金字标题 + 正文。设置页先占位。
- **前情提要（`/posts/`）**：文章以方形卡片列表呈现（标题 + 日期 + 摘要 + 金线），按时间倒序。
- **文章页**：宋体大标题、发布日期、正文，底部金色分隔线。

## 6. 工程结构

```
web_project/
├── hugo.toml                 # baseURL + 站点参数
├── content/
│   ├── _index.md             # 首页
│   ├── about/index.md
│   ├── settings/index.md     # 占位
│   ├── links/index.md
│   └── posts/
│       ├── _index.md         # 列表页
│       └── welcome.md        # 示例文章
├── layouts/                  # 手写自定义模板
│   ├── _default/{baseof,single,list}.html
│   ├── index.html            # 首页（开屏 + 导航卡）
│   └── partials/{head,header,footer}.html
├── assets/scss/main.scss     # 黑金样式（Hugo extended 编译）
├── static/images/xuannv.png  # 璇女图（从 资料/ 复制）
├── archetypes/default.md
└── .github/workflows/hugo.yml
```

## 7. 部署

- Pages 来源：**GitHub Actions**。
- 工作流：拉代码 → 官方 Hugo action 构建 → `actions/deploy-pages` 发布。
- 推送 `main` 即自动上线。
- git remote：`https://github.com/HFLYHYZQ/web.git`
- `hugo.toml` 中 `baseURL = "https://HFLYHYZQ.github.io/web/"`

## 8. 技术取舍

- 开屏用模板内覆盖层 + JS：纯前端、无路由、符合"每次进入"语义。
- GitHub Actions 而非提交 docs/：仓库只存源码，不污染历史。
- 手写项目级 layouts 而非 themes/ 子目录：单站点最直接，便于改样式。
