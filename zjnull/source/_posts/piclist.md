---
title: 通过Gitee镜像加速图床
date: 2025-12-21 20:50:07
tags: ["Hexo", "博客"]
categories: ["技术"]
---

# 前言

由于某些特殊原因，国内的 Gitee Pages 暂时无法提供服务，导致 Markdown 文件中的图片无法正常显示。此外，Gitee 可能会对 `raw` 链接的外部访问进行限制，使得某些平台（如 GitHub 及部分 Markdown 解析器）无法直接引用 Gitee 上的图片。

![](https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/image.png)

为了解决这一问题，我们可以搭建一个图床，将图片上传至 GitHub Pages，并通过 Gitee 对 GitHub Pages 仓库进行镜像，以 Gitee 提供的 `raw` 链接来引用图片。这种方法不仅能够加快国内访问速度，还能提高图片链接的稳定性。

如果你希望在手机或电脑上轻松管理图床，**PicList** 是一个极佳的选择。它支持多种存储方案，其中 Gitee/GitHub 组合是免费的最佳方案。本教程将指导你如何使用 **PicList + Gitee** 搭建一个高效的个人图床。

> GitHub 上的图片在国内可能加载较慢，因此推荐使用 Gitee 进行加速。

# 基于 PicList + GitHub 搭建图床

对于个人博客作者来说，将图片上传到网上并以链接形式调用是十分重要的，尤其是当图片数量较多且不希望占用本地空间时，这种方法显得尤为实用。然而，许多网盘的下载速度较慢，预览效果也不理想。因此，本文推荐使用 GitHub 作为图床，并借助 PicList 工具进行管理。

本教程将介绍如何使用 GitHub 作为图床，并提供 PicList 的安装和配置方法，以便轻松管理和引用网络图片。

---

## 1. 准备工作

在开始之前，你需要准备以下内容：

- **GitHub 账号**（[注册 GitHub](https://github.com/)）

- **GitHub 仓库**（用于存储图片）

- **GitHub 个人访问令牌**（用于 PicList 授权上传图片）

- **PicList 应用**（[下载地址](https://piclist.cn/app#%E4%B8%8B%E8%BD%BD%E5%AE%89%E8%A3%85)）

---

## 2. 安装 PicList

1. 下载 **PicList** 安装包，安装后运行。

2. 运行后，PicList 会出现在系统托盘中。

3. 若提示更新，可以暂时忽略（测试版可能存在 Bug）。

---

## 3. 创建 GitHub 仓库

1. 登录 [GitHub](https://github.com/)，点击右上角 `+` → `New repository`。

2. 设置仓库名称（如 `image`），描述可不填。

3. 选择 `Public`（公共仓库），`README` 选填。

4. 点击 `Create repository` 完成创建。

---

## 4. 获取 GitHub 个人访问令牌

1. 进入 **GitHub 账户设置** → **Developer settings** → **Personal access tokens**。

2. 选择 `Generate new token`，然后点击 `Fine-grained tokens`。

3. 选择 **仓库（Repository）权限**：

- `Contents - Read and write`（读取和写入权限）。

- `Metadata - Read-only`（只读权限）。

4. 复制生成的令牌（**仅显示一次，请妥善保存**）。

---

## 5. 配置 PicList

1. 打开 **PicList**，进入 `图床` 页面

2. 选择 `GitHub` 插件，并点击 `设置`

3. 填写以下信息：

- **owner/repo**： GitHub 仓库名（如 `Brian510000/pic_bed`）

- **Token**：粘贴 GitHub 个人访问令牌

- **分支**：`main`（或你设定的默认分支）

4. 点击 `设为默认图床`

![](https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250221233542832.png)

---

## 6. 配置 CDN 加速

由于 GitHub 服务器位于美国，国内访问速度可能较慢，因此可以使用 CDN 加速。

CDN 访问规则：

```

https://cdn.jsdelivr.net/gh/你的用户名/你的仓库名/你的分支名/

```

示例：

```

https://cdn.jsdelivr.net/gh/yourusername/image/main/

```

填入 PicList 配置后，点击 `确定` 保存设置。

---

## 7. 测试上传

1. 进入 **PicList 上传区**，选择一张图片。
   ![](https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250221234136757.png)
2. 上传成功后，PicList 会自动生成 Markdown 格式的图片链接。

3. 复制该链接，并在博客或文档中使用。

示例 Markdown 代码：

```markdown
![示例图片](https://raw.githubusercontent.com/yourusername/image/main/your-image.png)
```

---

## 8. 验证上传结果

1. 进入 GitHub，打开 `image` 仓库。

2. 查看 `main` 分支，确认图片已成功上传。

3. 使用 CDN 加速链接在 Markdown 文章中插入图片。

---

## 结语

通过以上步骤，你已成功搭建 **PicList + GitHub** 个人图床，轻松实现图片的上传和引用。

这种方法不仅适用于 Markdown 编辑，也适合所有需要在线存储图片的用户。赶快试试吧，让你的图片管理更加便捷！
