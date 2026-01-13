---
title: 记录一次完整搭建博客的过程
pubDatetime: 2026-01-13
description: Create Blog Step
draft: false
featured: true
tags: []
---
1. Fork [Astro](https://github.com/t0saki/AstroPages-Bilingual) 仓库。

![](image.png)

2. 登录 Cloudflare Dashboard > Workers 和 Pages > 创建应用程序 > 下面小字Pages Get Started > Connect to Git。

![](image.png)

3. 选择你的仓库。

![](image.png)

4. **构建设置 (Build Settings)**：

![](image.png)

- **Framework Preset**: Astro

重要：构建框架一定要选择 **Astro** ，我一开始没选择 使用的默认的，部署出来没有前端页面

- **Build Command**: `npm run build` (默认)
- **Output Directory**: `dist` (默认)

5. 点击 **Save and Deploy**。

![](image.png)

6. 部署成功后打开生成的链接 **https://xxxx.pages.dev，出现前端页面表示正常**

![](image.png)

- 如果你想换成自己的域名，现在需要绑定自定义域名

  ![](image.png)

- 绑定成功后等待 DNS解析 状态变为活动即可，验证打开绑定的域名是否生效

  ![](image.png)

7. 创建 GitHub App

   - 前往 [GitHub Developer Settings](https://github.com/settings/apps) > New GitHub App。

     ![](image.png)

   - **GitHub App name:** `you github username`

   - **Homepage URL**: `https://your-site.pages.dev 或 在上一步已绑定的自己的域名 https://your-domain.xxx`

   - **Callback URL**: `https://your-site.pages.dev/api/keystatic/github/oauth/callback 或 在上一步已绑定的自己的域名 https://your-domain.xxx/api/keystatic/github/oauth/callback`

   - **Permissions**: 对 "Contents" 的读写权限 (Read & Write)，对 "Metadata" 的只读权限 (Read-only)。

     - 点击 **Repository permissions**

       ![](image.png)

     - 找到 Contents 并选择 Read & Write

       ![](image.png)

     - 找到 Metadata，默认 Read-only

   - 取消勾选 Webhook

     ![](image.png)

   - 点击创建

     ![](image.png)

   - 保存 `Client ID` 并生成一个 `Client Secret`。

     ![](image.png)

   - 左边菜单找到 **Install App**，安装到你的内容仓库

   ![](image.png)

   - 选择 Fork 的 [Astro](https://github.com/t0saki/AstroPages-Bilingual) 仓库

   ![](image.png)

1. **在 Cloudflare 设置环境变量**

   - 前往 Pages 项目 > Settings > Environment variables。
   - 添加以下变量：
   - `KEYSTATIC_GITHUB_CLIENT_ID`: (你的 Client ID)
   - `KEYSTATIC_GITHUB_CLIENT_SECRET`: (你的 Client Secret)
   - `KEYSTATIC_SECRET`: (用于会话加密的随机长字符串)
     - **重要：**`KEYSTATIC_SECRET`**推荐使用代码生成的**64 位随机字符串，如果位数少了的会有问题，可以去 [Tools](https://tools.nb.uy/token-generator) 在线生成

       ![](image.png)
   - 添加完以后重新部署以使变量生效。
   - 部署完成后，访问 `https://your-site.pages.dev/keystatic 或 自己的域名 https://your-domain.xxx/keystatic`，使用 GitHub 登录即可管理线上内容
