---
title: Proxy 使用（含 VSCode & PicList 迁移）
date: 2025-02-23 21:09:11
tags: ["Hexo", "博客"]
categories: ["技术"]
top_img: /img/2.jpg
cover: https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250223045607120.png
---

# Proxy 使用（含 VSCode & PicList 迁移）

GitHub 上的文件（特别是 `raw.githubusercontent.com`）在国内访问较慢，甚至可能被屏蔽。本篇介绍如何使用 **GitHub Proxy 加速**，并提供 **VSCode & PicList 迁移** 以及 **全网页图床 URL 批量替换** 的完整流程。

---

## 1. GitHub Proxy 加速访问

如果在国内访问 `githubusercontent` 资源（例如 `raw.githubusercontent.com`）遇到问题，可以使用 GitHub Proxy 进行加速。
![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250223045633250.png)
### 1.1 常见的 GitHub Proxy 服务

以下是几个常用的 GitHub Proxy 代理地址：
[GitHub Proxy 最新地址发布](https://ghproxy.link/)
```
https://ghproxy.link
https://ghfast.top
https://ghp.ci # 已被墙
```

使用方法：

- 在 `raw.githubusercontent.com` 资源前添加 `https://ghfast.top/`
  >取决于GitHub Proxy 的最新地址
  
  **示例：**
  
  ```
  https://raw.githubusercontent.com/username/repository/main/file.txt
  ```
  
  替换为：
  
  ```
  https://ghfast.top/https://raw.githubusercontent.com/username/repository/main/file.txt
  ```
  
- 如果你使用 Git 克隆，也可以在 `git clone` 时加速：
  
  ```
  git clone https://ghfast.top/https://github.com/username/repository.git
  ```

## 2. VSCode 迁移 GitHub 代理

如果你的 VSCode 插件市场（Marketplace）或扩展下载速度慢，可以手动更改 VSCode 插件下载源。

### 2.1 通过 settings.json 修改代理

在 VSCode 中，打开 `settings.json`（**Ctrl + Shift + P** 输入 `settings.json` 并选择 `Preferences: Open Settings (JSON)`），然后添加以下配置：

```json
{
    "http.proxy": "http://你的代理地址",
    "https.proxy": "http://你的代理地址",
    "remote.SSH.showLoginTerminal": true
}
```

如果你使用的是 `GitHub Proxy`，可以手动修改 `extensionsGallery` 配置：

```json
{
    "extensionsGallery": {
        "serviceUrl": "https://ghfast.top/https://marketplace.visualstudio.com/_apis/public/gallery",
        "cacheUrl": "https://ghfast.top/https://vscode.blob.core.windows.net/gallery/index",
        "itemUrl": "https://ghfast.top/https://marketplace.visualstudio.com/items"
    }
}
```

## 3. PicList 迁移及图床 URL 替换

PicList 是一款支持多种图床（如 GitHub 图床、SM.MS、阿里云 OSS 等）的图片管理工具。由于 `raw.githubusercontent.com` 在国内访问缓慢，我们可以批量替换图床 URL。

### 3.1 批量替换 URL

如果你的网站或 Markdown 文档中大量使用了 `raw.githubusercontent.com`，可以使用 `sed` 或 VSCode 进行全局替换。

### 3.1.1 使用 sed 批量替换（适用于 Linux/macOS）

```sh
sed -i 's#https://raw.githubusercontent.com/#https://ghproxy.com/https://raw.githubusercontent.com/#g' *.md
```

### 3.1.2 使用 正则表达式进行替换

1. 在 PicList 中打开你的相册管理目录
2. 点击 **修改**（全局搜索替换）
![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250223210207666.png)
3. 搜索 `https://raw.githubusercontent.com/`，替换为 `https://ghfast.top/https://raw.githubusercontent.com/`
4.  点击**确定**
5. 在图床页面设置**自定义域名**![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250223210545813.png)
	>这一步是确保后续粘贴图像链接时更加方便稳定
---

## 4. 结论

通过 GitHub Proxy，我们可以有效加速 `raw.githubusercontent.com` 的访问，同时优化 VSCode 插件市场加载速度，并批量替换 PicList 及网页中的图片 URL，以改善 GitHub 资源的国内访问体验，获得体验更加稳定的图床源
