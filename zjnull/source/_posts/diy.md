---
title: Hexo 跳过渲染
date: 2025-02-01 16:05:43
tags: ["Hexo", "博客"]
categories: ["技术"]
---

# Hexo 自定义网页

## 概述

Hexo 是一个基于 Node.js 的静态博客生成器，它通过主题渲染生成静态网页。虽然 Hexo 博客通常具有高度的统一化和规范化，但 Hexo 提供了跳过渲染的功能，允许用户在博客中嵌入自定义网页。这使得用户可以在博客中添加图片、自定义 404 页面、关于页面、简历等内容。

## 创建自定义网页

### 1. 编写网页

首先，编写想要嵌入到 Hexo 博客中的网页。这个网页可以是自己编写的，也可以是从网上下载的现成源码

**创建新页面**
执行以下命令：

```bash
hexo new page "about"
```
这样会在 source 目录下生成一个 about 文件夹，并在其中创建 index.md 文件：


### 2. 放置网页文件

将编写好的网页文件放置在 Hexo 的 `source` 目录下。可以创建一个新的文件夹来存放这些文件，文件夹的名称可以任意，例如 `about`

```plaintext
Hexo
├── source
│   ├── about
│   │   └── index.html
│   └── other_files
```
### 3. 重命名文件

将 HTML 文件重命名为 `index.html`。这样，当部署博客后，访问 `http://zjnull.cn/about` 时，就会显示这个自定义网页。

## 跳过渲染

为了让 Hexo 在生成静态网页时跳过对自定义网页的渲染，可以使用以下两种方法：

### 方法一：在自定义页面中添加指令

在自定义页面的开头添加如下指令：


```
---
layout: false
---
```
添加该指令后，执行 `hexo g` 命令时，Hexo 会跳过对该 `index.html` 文件的渲染，使其不受当前 Hexo 主题的影响，成为一个完全独立的网页。

**注意**：如果网页中引用了 CSS 或 JS 文件，这些文件需要使用外链，或者将它们放置在 `index.html` 同目录下进行引用。引用图片时也是如此。

### 方法二：在 `_config.yml` 文件中设置 `skip_render`

打开 Hexo 目录下的 `_config.yml` 文件，找到 `skip_render` 配置项。`skip_render` 通常有以下四种常用参数：

1.  **跳过 `source` 目录下的 `test.html` 文件**：
	```
    skip_render: test.html
    ```
2.  **跳过 `source` 目录下 `test` 文件夹内的所有文件**：
    
    ```
    skip_render: test/*
    ```
3.  **跳过 `source` 目录下 `test` 文件夹内的所有文件，包括子文件夹及子文件夹内的文件**：
    
    ```
    skip_render: test/**
    ```
4.  **跳过多个路径**：
    
    ```
    skip_render:
      - test.html
      - test/*
    ```

### 4. 部署博客

完成上述设置后，执行以下命令生成并部署博客：

```
hexo clean && hexo generate && hexo deploy
```
## 总结

通过以上步骤，可以在 Hexo 博客中嵌入自定义网页，并通过跳过渲染的方式使其不受主题影响。这种方法适用于添加图片、自定义 404 页面、关于页面、简历等内容，极大地丰富了 Hexo 博客的功能和个性化程度。