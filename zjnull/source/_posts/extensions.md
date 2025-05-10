---
title: 关于VS code插件的碎碎念
date: 2025-02-23 05:33:38
tags: ["Hexo", "博客"]
categories: ["技术"]
cover: https://unsplash.com/photos/_SgRNwAVNKw/download?ixid=M3wxMjA3fDB8MXxhbGx8fHx8fHx8fHwxNzQwNzY2MzQ4fA&force=true
---


# 前言
从Pycharm转VScode之后，发现轻量化才是神中神，插件丰富适合俺这样的半吊子嘿嘿，而且秒开秒关，内存占用也不高，以前过的都是什么苦日子哇，Pycharm，等俺学成归来再爱你吧哈哈哈哈哈
# 使用方法
## 1.保存类requirement文件
复制下面的代码，保存在新建的extensions.txt文件中
```
adpyke.codesnap
alefragnani.bookmarks
buuug7.gbk2utf8
christian-kohler.path-intellisense
cschlosser.doxdocgen
esbenp.prettier-vscode
fittentech.fitten-code
kisstkondoros.vscode-gutter-preview
ms-ceintl.vscode-language-pack-zh-hans
ms-python.autopep8
ms-python.debugpy
ms-python.python
ms-python.vscode-pylance
pkief.material-icon-theme
shd101wyy.markdown-preview-enhanced
usernamehw.errorlens
zhuangtongfa.material-theme
```
## 2.重新安装插件

在另一台设备上，或者重装 VS Code 后，可以用下面的命令批量安装这些插件：

```
cat extensions.txt | xargs -n 1 code --install-extension
```
或者：


```
xargs -n 1 code --install-extension < extensions.txt
```
这样，你的所有插件就会被自动安装回来。
![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250223053704763.png)

# 目录
## Prettier - Code formatter
评分**9.0**，这个作者审美没得说，好看不累
有点困，先睡觉了，明天再写吧，等等，明天放假！！好像三角洲也刚好更新，哇塞哇塞，那等俺想起来了再碎碎念吧阿巴阿巴哈哈哈哈哈