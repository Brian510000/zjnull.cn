---
title: "Hexo 运维状况"
date: 2025-02-12 20:00:00
tags: ["Hexo", "博客"]
categories: ["技术"]
top_img: /img/2.jpg
cover: /img/5.jpg
toc: true
comments: true
---
测试中，git是否正确提交分支

测试保存源码是否会覆盖main或上传main

现在vscode提交到分支source上，不影响main分支的代理

# 使用教程

``` bash
$ hexo s
```
可以直接边运维边查看实际效果
***
``` bash
$ hexo clean && hexo generate && hexo deploy
```
可以一键部署到远程服务器



## **运维状况报告**

**日期：** 2025年2月8日\
**作者：** 花开一百年

### **1. 服务器状态**

阿里云SSL证书还剩19张
华为云SSL证书20张
### **2. 近期事件**


#### **2.1 服务器维护**

- **时间：** 2025-02-07 23:00 - 2025-02-08 02:00
- **内容：** 例行安全补丁更新 + 服务器重启
- **影响：** 期间短暂宕机 5 分钟

#### **2.2 发现的问题**

优化界面UI，部分标题导航栏无法正确打开

### **3. 解决方案与优化**

[使用文档](https://butterfly.js.org/posts/dc584b87/)

### **4. 未来改进方向**

- 优化界面UI，提升用户体验

---

## **建议（给 Markdown 新手）**

1. **使用 Markdown 编辑器**：推荐使用 VSCode + Markdown Preview Enhanced 插件，可以实时预览效果。
   ```
4. **插入图片**：用 `![描述](图片URL)` 添加截图或图表，有助于直观展示问题。
5. **多用分级标题**（`# 一级标题`、`## 二级标题`）让内容层次清晰。
6. **尽量简洁**：Markdown 的优点是轻量级，避免冗长复杂的格式。

如果你需要 Markdown 更详细的学习资料，可以看这个：\
📌 [Markdown 官方指南](https://www.markdownguide.org/)

希望这些建议对你有帮助！🚀

