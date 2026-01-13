---
title: AstroPages 项目完整部署指南
pubDatetime: 2026-01-13
description: 创建博客步骤
draft: true
featured: true
tags: []
---
目标：把 Fork 的 Astro 双语博客部署到 Cloudflare Pages，并启用 Keystatic CMS 在线编辑内容（/keystatic 后台）。

#### 第一步：Fork 并准备仓库

1. 打开原仓库 → **Fork** 到你自己的 GitHub 账号下 （推荐：改仓库名为 your-username-blog 之类，便于管理）

#### 第二步：Cloudflare Pages 部署前端

1. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com/?referrer=grok.com) → **Workers & Pages** → **创建应用程序** → **Pages** → **Get Started** → **Connect to Git**

1. 选择你刚刚 Fork 的仓库 → **Begin setup**

1. **构建设置（Build Settings）** —— **这一步最关键！**

   {% table %}
   - 设置项
   - 推荐值
   - 说明 / 注意事项
   ---
   - Framework Preset
   - Astro
   - **必须选这个！** 没选会直接 404 / No content found（之前踩过的坑）
   ---
   - Build command
   - npm run build
   - 默认即可（项目 package.json 已定义）
   ---
   - Build output directory
   - dist
   - Astro 默认输出目录
   ---
   - Environment variables
   - （暂不填，后面加）
   - —
   {% /table %}

1. 点击 **Save and Deploy** → 等 1–3 分钟，部署成功后得到链接：https://your-project-name.pages.dev → 打开链接看到前端页面（首页/文章列表）即成功！

**小提示**：

- 第一次部署失败？查看 **Deployments** → 点最新一次 → Build logs，看报错（常见：没选 Astro、Node 版本不对）。
- 想用自己的域名？后面再绑（第 6 步）。

#### 第三步：创建 GitHub App（用于 Keystatic 授权）

1. 去 [GitHub Settings → Developer settings → GitHub Apps → New GitHub App](https://github.com/settings/apps/new?referrer=grok.com)
1. 填写信息（用你自己的站点替换）：
   - **GitHub App name**：建议 your-username-keystatic（唯一即可）
   - **Homepage URL**：https://your-project.pages.dev（或已绑定的自定义域名）
   - **Callback URL**：https://your-project.pages.dev/api/keystatic/github/oauth/callback （**必须加这个**，否则登录报 redirect_uri 错误）
   - **Webhook**：**取消勾选**（不需要）
1. **Repository permissions**：
   - Contents → **Read & Write**（必须！）
   - Metadata → **Read-only**（默认）
1. 点击 **Create GitHub App** → 立即复制：
   - **Client ID**
   - 生成并复制 **Client Secret**（只显示一次，记好！）
1. **安装 App**：
   - 页面左边 → **Install App** → 选择你 Fork 的仓库 → **Install**

#### 第四步：Cloudflare 添加环境变量（激活 Keystatic）

1. Cloudflare Dashboard → 你的 Pages 项目 → **Settings** → **Environment variables** → **Add variable**

1. 添加以下三个（**全部 Production**）：

   {% table %}
   - 变量名
   - 值示例 / 说明
   ---
   - KEYSTATIC_GITHUB_CLIENT_ID
   - 从上步复制的 Client ID
   ---
   - KEYSTATIC_GITHUB_CLIENT_SECRET
   - 从上步复制的 Client Secret
   ---
   - KEYSTATIC_SECRET
   - **必须是 64+ 位随机字符串**！推荐用工具生成： • [https://keystatic-deploy-test.netlify.app/generate-keystatic-secret/](https://keystatic-deploy-test.netlify.app/generate-keystatic-secret/?referrer=grok.com) • 或命令 npx @keystatic/generate-secret
   {% /table %}

   **KEYSTATIC\_SECRET 警告**： 如果只填 sherlock、123456 等短/弱密码 → 会报认证错误或安全隐患！必须随机长串。

1. 保存后 → **立即重新部署**（点 Deployments → Redeploy 或 push 一个空 commit）

#### 第五步：测试 Keystatic 后台

1. 打开 https://your-project.pages.dev/keystatic （或自定义域名 /keystatic）
1. 点击 **Login with GitHub** → 授权 → 成功进入后台 → 现在你可以在线创建/编辑文章、上传图片了（图片会 commit 到仓库）。

#### 第六步：（可选）绑定自定义域名

1. Pages 项目 → **Custom domains** → **Set up a domain**
1. 输入你的域名（需已加到 Cloudflare DNS）
1. 等 DNS 状态变为 **Active**
1. 验证：浏览器打开你的域名 → 看到站点即成功
