---
title: "ZJNULL 更新评论区"
date: 2025-02-12 22:16:00
tags: ["Hexo", "博客"]
categories: ["技术"]
top_img: /img/2.jpg
cover: /img/5.jpg
toc: true
comments: true
highlight_shrink: false  # 这一篇文章的代码块默认展开
---


#  GitHub 讨论部署

今儿我在自己发布的文章里看到头文件里有 `comment`，但文章下方或附近却完全找不到评论区。怎么回事？带着疑问去查了下 Wiki，结果发现：**评论也需要手动创建、对接、部署**……一想到又得折腾这一套流程，属实让人头大。

不过好在主题文档写得还算清晰。先去 GitHub 应用那里安装了 **giscus**，然后创建了一个仓库来存储评论数据，接着开启了 discussion 映射关系。说来也神奇，虽然是第一次接触这个功能，甚至连名字都是第一次听说，但却自然而然地摸到了大概的位置。顺着感觉自然而然地一路点进 **Setting**，勾选 **Discussion**，再回到 giscus 刷新，完成对接。

接下来就是部署，也就是写代码的关键步骤了。Hexo 的大部分配置都在 **_config.yml** 文件中进行调整，包括主题、代码块高亮、站点配置、构建、部署等。然而，评论系统的配置却在`_config.butterfly.yml`里，我在 `config.yml` 里尝试进行部署，自然一点效果都没有 (っ- ‸ - ς)。这一刻是真的卡住了，不知道该怎么办，迷迷糊糊地……还好，去问了一手 GPT ，提醒我检查 **主题设置** 是否也需要配置。试着填上去后，一刷新——哈哈，果然好了！

顺便贴一下我的部署代码：
```  yml
# Giscus
# https://giscus.app/

comments:
	# 选择评论系统（这里选择 Giscus）
	use: Giscus
	# 显示评论系统的名称
	text: true
	# 滚动到评论区域时再加载（false 代表直接加载）
	lazyload: false
	# 在文章顶部图片处显示评论数量
	count: false
	# 在主页卡片中显示评论数量
	card_post_count: false

giscus:
	enable: true
	repo: "Brian510000/comments" # GitHub 仓库
	repo_id: "R_kgDON4YPQQ"
	category: "Announcements"
	category_id: "DIC_kwDON4YPQc4Cm5dh"
	mapping: "pathname"
	strict: "0"
	reactions_enabled: "1"
	emit_metadata: "0"
	input_position: "top"
	theme: "light"
	lang: "zh-CN"
```
