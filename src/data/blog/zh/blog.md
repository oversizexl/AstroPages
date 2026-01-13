---
title: 如何用 AstroPages-Bilingual 模板快速搭建双语博客（Cloudflare Pages + Keystatic CMS 完整教程）
pubDatetime: 2026-01-13
description: 从 Fork 仓库到上线后台编辑，一步步带你部署一个免费、高性能的双语 Astro 博客，还能在线用 Keystatic 管理内容。亲测超坑的点都标注了！
draft: false
featured: false
tags:
  - Astro
  - Keystatic
  - Cloudflare Pages
  - 双语博客
---
大家好，我是 Sherlock。

最近想自己搭一个支持中英双语的个人博客，选来选去最后落在了 **AstroPages-Bilingual** 这个模板上（原仓库：https://github.com/t0saki/AstroPages-Bilingual）。它用 Astro 5 + Keystatic CMS + Cloudflare Pages 部署，速度快、免费、内容直接存 GitHub，超级适合个人玩家。

整个过程我踩了不少坑（尤其是第一次没选 Astro 框架导致空白页），现在写下来分享给大家。

### 1. 先 Fork 模板仓库

直接点这个链接 Fork：\
https://github.com/t0saki/AstroPages-Bilingual

Fork 后建议改个名字，比如 `sherlock-blog` 什么的，方便记忆。

### 2. 上 Cloudflare Pages 部署前端（最关键的一步！）

1. 打开 Cloudflare Dashboard：https://dash.cloudflare.com\
   → 左侧菜单选 **Workers & Pages**\
   → 点击 **Create application** → **Pages** → **Get Started**
1. 选 **Connect to Git**，授权你的 GitHub，然后找到你 Fork 的仓库，点 **Begin setup**。
1. **构建设置（Build Settings）** ← 这里超级重要，我第一次没注意，直接空白页了！
   - **Framework Preset**：一定要选 **Astro**！（默认是 No framework，会导致没前端页面，404 或白屏）
   - **Build Command**：保持默认 `npm run build`
   - **Output Directory**：保持默认 `dist`
1. 点 **Save and Deploy**，等 1-3 分钟。\
   成功后会给你一个链接：https://xxxx.pages.dev\
   打开看一眼，如果首页和文章列表正常出现，就成功啦！

**小贴士**：

- 部署失败了？去 **Deployments** 里点最新那次，看 Build logs，常见错误就是没选 Astro。
- 想用自己的域名？可以现在跳到最后一步绑定，后面再回来。

### 3. 设置 GitHub App，让 Keystatic 能在线编辑

Keystatic 是这个模板的亮点，它让内容管理像 WordPress 一样简单，但数据直接存 GitHub。

1. 去 GitHub 创建 App：https://github.com/settings/apps/new
1. 填写信息（替换成你自己的）：
   - **GitHub App name**：随便填，比如 `sherlock-keystatic`（唯一就行）
   - **Homepage URL**：你的站点地址，比如 `https://your-site.pages.dev`（或自定义域名）
   - **Callback URL**：必须填 `https://your-site.pages.dev/api/keystatic/github/oauth/callback`（漏了会报 redirect 错误！）
   - **Webhook**：取消勾选，不需要
1. **Repository permissions**：
   - 点 **Repository permissions**
   - Contents → 选 **Read & Write**（必须！）
   - Metadata → 默认 **Read-only** 就好
1. 拉到底，点 **Create GitHub App**\
   → 复制 **Client ID**\
   → 生成并复制 **Client Secret**（只显示一次，保存好！）
1. 左侧菜单点 **Install App** → 选你的 Fork 仓库 → **Install**。

### 4. 在 Cloudflare 加环境变量（激活后台）

回到 Cloudflare Pages 项目：

1. → **Settings** → **Environment variables** → **Add variable**
1. 加这三个（选 **Production**）：
   - `KEYSTATIC_GITHUB_CLIENT_ID`：粘贴 Client ID
   - `KEYSTATIC_GITHUB_CLIENT_SECRET`：粘贴 Client Secret
   - `KEYSTATIC_SECRET`：**随机长字符串**！推荐 64 位以上\
     可以去这个工具在线生成：https://tools.nb.uy/token-generator\
     或者终端跑 `npx @keystatic/generate-secret`\
     **警告**：别用 `123456`、`sherlock` 这种弱密码，会认证失败或不安全！
1. 加完点 **Save** → 去 **Deployments** → 手动 **Redeploy**（或 push 个空 commit 触发）。

### 5. 测试后台登录

部署完后，打开：\
https://your-site.pages.dev/keystatic\
（或你的自定义域名 + /keystatic）

点 **Login with GitHub** → 授权 → 成功进入后台！\
现在你可以在线写文章、上传图片了，所有改动都会自动 commit 到 GitHub。

### 6. （可选）绑定自己的域名

1. Pages 项目 → **Custom domains** → **Set up a domain**
1. 输入你的域名（提前加到 Cloudflare DNS）
1. 等状态变成 **Active**
1. 浏览器打开域名测试一下，OK 就大功告成！

### 小结 & 我的心得

整个流程其实不难，关键坑就两个：

- 构建设置必须选 Astro（不然白屏）
- KEYSTATIC_SECRET 一定要够长随机（弱密码会报错）

上线后站点速度飞起（Astro SSG + Cloudflare 边缘缓存），内容管理也方便。推荐大家试试！

有问题欢迎评论区留言，或者去原仓库提 issue。祝你也快速搭好自己的双语小窝～

（完）
