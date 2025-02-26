---
title: ZJNULL 项目说明
categories: [技术]
date: 2025-02-19 14:50:29
tags: ["Hexo"]
sticky: 999
---

# Hexo 项目说明

目前，zjnull本项目在 GitHub 仓库有两个分支：

>main 分支：Hexo 自动生成的静态网页文件（hexo clean && hexo g && hexo d）

>source 分支：在 VS Code 里手动推送的全部 Hexo 源码

## 关于 Hexo
Hexo 是一个基于 Node.js 高效的web服务器框架，可以快速生成静态网页。

Hexo 使用 Markdown（或其他语言）来写文章，并在渲染时将其转换为 HTML 文件。

Hexo 支持丰富的插件，可以实现各种功能，如评论、搜索、统计、分类、标签、分页等。

Hexo 支持自定义页面，可以搭建个人博客或企业网站。

Hexo 支持本地预览，可以实时看到修改后的效果。

Hexo 支持部署到 GitHub、GitLab、Coding 等平台。

# Hexo 部署分支修改

Hexo 的部署分支通常是在 `_config.yml` 配置文件中的 `deploy` 部分进行设置。例如：

```yaml
# _config.yml
deploy:
  type: git
  repo: https://github.com/Brian510000/zjnull.cn.git
  branch: main
```

## 修改部署分支

如果希望 Hexo 部署到 `source` 分支，而不是 `main`，可以修改 `branch` 参数，如下所示：

```yaml
# _config.yml
deploy:
  type: git
  repo: https://github.com/Brian510000/zjnull.cn.git
  branch: source
```

然后运行以下命令，使修改生效并部署到 `source` 分支：


```bash
hexo clean && hexo generate && hexo deploy
```
> 🚀 **注意：** 部署后会影响原先的旧文件，这里只是示例

这样 Hexo 生成的静态文件就会推送到 `source` 分支，而不是 `main`。

## 检查当前 Hexo 配置

可以在 Hexo 项目根目录下运行以下命令，查看当前 `branch` 的设置：

```bash
cat _config.yml | grep deploy -A 3
```

默认情况下，Hexo 一般会部署到 `main` 或 `master` 分支，用户可根据需求修改。









# 远程端部署文件位置

Hexo 部署到 `main` 分支的静态文件默认存放在 `public/` 目录。

当运行 `hexo generate` (`hexo g`) 时，Hexo 会在 `public/` 目录内生成完整的静态网站文件（HTML、CSS、JS、图片等）。如果再执行 `hexo deploy` (`hexo d`)，它会把 `public/` 目录的内容推送到 GitHub 指定的 `main` 分支（或者 `_config.yml` 里设置的分支）。

## 流程总结

### 1. Hexo 生成静态文件

```bash
hexo clean && hexo generate
```

- `hexo clean` 清理 `public/` 目录，确保没有旧文件干扰。
- `hexo generate` 重新生成完整的静态网站，存放在 `public/` 目录。

### 2. Hexo 部署到 GitHub

```bash
hexo deploy
```

- `hexo deploy` 会读取 `_config.yml` 里的 `deploy` 设置。
- 它会把 `public/` 目录的内容推送到 `main` 分支（或你指定的分支）。

## 如何查看 `public/` 目录

可以在 Hexo 项目根目录下运行以下命令：

```bash
ls public/
```

或者使用 `tree` 命令（如果已安装）查看目录结构：

```bash
tree public/
```

如果手动打开 `public/` 目录，你会看到类似以下的文件结构：

```plaintext
public/
├── index.html
├── archives/
├── categories/
├── css/
├── images/
├── js/
├── tags/
└── about/
```

这些就是最终部署到 GitHub Pages 上的静态网站文件。

## 如何本地预览 `public/` 目录

可以使用 Hexo 提供的 `hexo server` 命令在本地测试：

```bash
hexo server
```

然后在浏览器打开 `http://localhost:4000` 预览网站。

如果想手动查看 `public/` 目录的 HTML 代码，也可以直接用浏览器打开 `public/index.html` 文件。

## 总结

- Hexo 生成的静态网站文件存放在 `public/` 目录。
- `hexo g` 生成 `public/` 目录的内容。
- `hexo d` 部署 `public/` 目录的内容到 GitHub（通常是 `main` 分支）。
- 可以用 `hexo server` 在本地预览 `public/` 目录的内容。






# Hexo 快速入门

欢迎来到 [Hexo](https://hexo.io/)! 这是一篇示例文章，帮助你快速上手 Hexo。查看 [官方文档](https://hexo.io/docs/) 获取更多信息。如果在使用 Hexo 过程中遇到问题，你可以在 [故障排查](https://hexo.io/docs/troubleshooting.html) 页面查找答案，或者在 [GitHub](https://github.com/hexojs/hexo/issues) 提问。

## 快速开始

### 创建新文章

使用以下命令创建一篇新文章：

```bash
$ hexo new "My New Post"
```

更多详情： [如何撰写文章](https://hexo.io/docs/writing.html)

### 启动本地服务器

运行本地服务器以预览你的博客：

```bash
$ hexo server
```

更多详情： [服务器](https://hexo.io/docs/server.html)

### 生成静态文件

Hexo 生成静态网站文件的命令如下：

```bash
$ hexo generate
```

更多详情： [生成静态文件](https://hexo.io/docs/generating.html)

### 部署到远程站点

你可以使用以下命令将网站部署到远程服务器或 GitHub Pages：

```bash
$ hexo deploy
```

更多详情： [一键部署](https://hexo.io/docs/one-command-deployment.html)

