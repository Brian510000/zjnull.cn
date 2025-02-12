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

折腾了这么久，总算搞定了 GitHub 讨论（Discussion）的部署，真是一波三折。

有些东西果然还是得自己摸索，书本上学不到，也很难用文字精准描述那种一步步推理的过程。今儿我在自己发布的文章里看到头文件里有 `comment`，但文章下方或附近却完全找不到评论区。怎么回事？带着疑问去查了下 Wiki，结果发现：**评论也需要手动创建、对接、部署**……一想到又得折腾这一套流程，属实让人头大。

不过好在主题文档写得还算清晰。我先去 GitHub 应用那里安装了 **giscus**，然后创建了一个仓库来存储评论数据，接着开启了 discussion 映射关系。说来也神奇，虽然是第一次接触这个功能，甚至连名字都是第一次听说，但却自然而然地摸到了大概的位置。顺着感觉自然而然地一路点进 **Setting**，勾选 **Discussion**，再回到 giscus 刷新，完成对接。

接下来就是部署，也就是写代码的关键步骤了。我先在 `config.yml` 里进行部署，结果一点效果都没有 (っ- ‸ - ς)。这一刻我是真的卡住了，不知道该怎么办，迷迷糊糊地……还好去问了一手 GPT ，提醒我检查 **主题设置** 是否也需要配置。试着填上去后，一刷新——哈哈，果然好了！

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
