---
title: Deepseek-R1 API接口调用
date: 2025-03-14 00:49:21
categories: ["技术"]
cover: https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250316005244921.png
top_img: https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250307022542367.png
toc: true
comments: true
---

### 本站已成功集成 API 接口，并在导航栏部署了 **R1 大模型**，支持智能问答、代码辅助、学习交流等功能。点击即可直接体验 AI 互动，畅享高效便捷的智能对话！  


![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250316010707761.png)
## 👉 [**立即体验 R1 大模型**](https://www.zjnull.cn/ds-r1/) 🚀

# **进一步优化**

-   **加入聊天历史**：可以用 `localStorage` 存储聊天记录，让刷新后仍然可见。
-   **后端代理**：避免在前端暴露 API Key，可以用 Python Flask 或 Node.js 搭建一个中转后端。
-   **支持 Markdown**：用 `marked.js` 解析 GPT 返回的文本，让显示更美观。
-   **流式响应**：使用 `fetch` + `ReadableStream` 实现流式返回，提高体验
-  **更新CSS**：优化UI界面，显示思考时间与内容，提升输出反馈

# 写给大家
Hexo **本身** 不支持直接在同一页面中部分内容渲染、部分内容不渲染（即 Markdown 与 HTML 混合处理的控制）。但可以通过 **插件** 或 **特定语法** 来实现你的需求。

----------

## **方法 1：使用raw语法避免 Hexo 解析**

Hexo **默认** 会解析 Markdown 和特殊标签，但你可以使用 `{% 删raw %}...{% endraw %}` 让其中的内容不被 Hexo 处理：

```md
{% raw %}
	<p>这是一段不会被 Hexo 解析的 HTML</p>
{% endraw %}
```
这适用于 **部分 HTML 代码不想被 Hexo 转换** 的情况，比如 `<script>` 或 `<style>` 代码。

----------

## **方法 2：使用 Markdown 解析时忽略 HTML**

Hexo 默认支持 **Markdown 内嵌 HTML**，但如果你想让一部分 HTML 保持原样，可以直接写：

```md
正常的 Markdown 语法会被解析，比如 **加粗** 和 *斜体*

<div style="color: red;">
	这段 HTML 不会被 Hexo 转换，而是直接输出到页面。
</div>`
```
但是，某些复杂 HTML 可能会被 Hexo 转义，所以最好搭配 `{% 删raw %}` 使用。

----------

## **方法 3：在特定文件或页面禁用 Markdown**

如果你希望 **整个页面都不渲染 Markdown**，可以在 **Front Matter** 里加：

```yaml
--- title: 原始 HTML 页面 layout: false ---
```
这样，Hexo 就不会处理这个页面，它会被当作普通 HTML 页面。

----------

## **方法 4：使用 Hexo 的 `include_code` 插件**

如果你需要 **在 Markdown 里插入一段不渲染的代码**，可以用：

```md
{% include_code lang:html %}
<p>这段代码会直接显示，而不会被解析</p>
{% endinclude_code %}
```
但这个方法需要安装 `hexo-include-codeblock` 插件：

```sh
npm install hexo-include-codeblock --save
```

# 引入 **Marked.js**
一个用于解析和渲染 Markdown 的 JavaScript 库。



### **🔍 代码解析**

```html
<script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
```
-   `https://cdn.jsdelivr.net/npm/marked/marked.min.js`：  
    这是 **CDN（内容分发网络）** 上的 Marked.js 文件，它允许你直接在网页中使用 Marked.js 而无需下载或安装
-   `<script>`：  

    这行代码会在你的网页中加载 Marked.js，让你可以使用 `marked()` 方法来解析 Markdown。

----------

### **💡 Marked.js 用法示例**

假设你有一个 Markdown 格式的文本：

```js
const markdownText = "# 你好，GPT!\n\n这是 **加粗** 的文字。";
const htmlContent = marked.parse(markdownText);
// 解析 Markdown 为 HTML console.log(htmlContent);`
```
**输出 HTML：**

```html
<h1>你好，GPT!</h1>
<p>这是 <strong>加粗</strong> 的文字。</p>
```

----------

### **📌 在网页上渲染 Markdown**

如果你希望在网页上显示 Markdown 格式的内容：

```html
<textarea id="markdownInput"># 你好，GPT!</textarea>
<div id="output"></div>

<script>
	document.getElementById("markdownInput").addEventListener("input", function() {
		document.getElementById("output").innerHTML = marked.parse(this.value);
	});
</script>
```
### **🔹 运行效果**

-   当你在 `textarea` 里输入 Markdown，`div#output` 里会自动实时显示转换后的 HTML。

----------
1.  **引入 Marked.js**，让你可以在网页中解析 Markdown。
2.  **用 `marked.parse()` 方法**，把 Markdown 转换成 HTML，方便显示在网页里。
3.  **可以结合 `innerHTML`，实现 Markdown 实时预览功能**（比如写作编辑器）。

如果你的网页需要支持 Markdown（比如聊天窗口、博客内容等），这个库会很有用！🚀

# 前后端拆分方案



把聊天功能拆分成**前端页面**和**后端 API**，然后后端部署在 Hexo 上

**⚠️ 但是：Hexo 只能部署静态页面，不能运行后端代码（如 Node.js 服务器）。**  
你需要 **找个后端服务器**（如 VPS、Cloudflare Workers、Vercel、Render）来托管 API，再让前端页面请求这个 API。

----------

## **✅ 方案 1：前端部署在 Hexo，后端部署在 Cloudflare Workers**

1.  **前端**：在 Hexo 里保留 `index.html`，用户访问后端 API。
2.  **后端**：用 Cloudflare Workers 代理你的 API。

### **🌐 前端（放在 Hexo 里）**

在 `source/chat/index.html` 里写：

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GPT Chat</title>
    <script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; }
        .chatbox { border: 1px solid #ccc; padding: 10px; height: 300px; overflow-y: auto; }
        .input-box { width: 80%; padding: 5px; }
        .btn { padding: 5px 10px; background-color: blue; color: white; border: none; cursor: pointer; }
    </style>
</head>
<body>
    <h1>GPT Chat</h1>
    <div id="chatbox" class="chatbox"></div>
    <input class="input-box" type="text" id="userInput" placeholder="输入你的消息">
    <button class="btn" id="sendButton">发送</button>
    <script>
        const chatbox = document.getElementById("chatbox");
        const userInput = document.getElementById("userInput");
        const sendButton = document.getElementById("sendButton");

        function appendMessage(sender, message) {
            const msgDiv = document.createElement("div");
            msgDiv.innerHTML = `<strong>${sender}:</strong> <br>${marked.parse(message)}`;
            chatbox.appendChild(msgDiv);
            chatbox.scrollTop = chatbox.scrollHeight;
        }

        async function sendMessage() {
            const userMessage = userInput.value.trim();
            if (!userMessage) return;
            appendMessage("我", userMessage);
            userInput.value = "";

            try {
                const response = await fetch("https://your-workers-subdomain.workers.dev/chat", { 
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ message: userMessage })
                });

                if (!response.ok) throw new Error(`HTTP错误 ${response.status}`);
                const data = await response.json();
                appendMessage("AI", data.reply);
            } catch (error) {
                appendMessage("错误", `请求失败: ${error.message}`);
            }
        }

        sendButton.addEventListener("click", sendMessage);
        userInput.addEventListener("keypress", (event) => {
            if (event.key === "Enter") sendMessage();
        });
    </script>
</body>
</html>
```

**➡️ 这个前端页面可以放到 Hexo 里！**

----------

### **🌍 后端（Cloudflare Workers 代理 API）**

Cloudflare Workers 可以作为代理，隐藏你的 API Key，防止泄露。

#### **📌 部署步骤**

1.  **注册 Cloudflare Workers**：[https://workers.cloudflare.com/](https://workers.cloudflare.com/)
2.  **新建 Workers**
3.  **粘贴以下代码**

```js
export default {
    async fetch(request) {
        try {
            const { message } = await request.json();
            const response = await fetch("https://api.deepseek.com/v1/chat/completions", {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                    "Authorization": "Bearer sk-你的API密钥"
                },
                body: JSON.stringify({
                    model: "deepseek-reasoner",
                    messages: [{ role: "user", content: message }],
                    temperature: 0.5
                })
            });

            if (!response.ok) throw new Error(`API错误 ${response.status}`);
            const data = await response.json();
            return new Response(JSON.stringify({ reply: data.choices[0].message.content }), {
                headers: { "Content-Type": "application/json" }
            });

        } catch (error) {
            return new Response(JSON.stringify({ reply: "后端错误：" + error.message }), {
                headers: { "Content-Type": "application/json" }
            });
        }
    }
}
```

4.  **保存并部署**，你会得到一个 URL，比如：
    
    ```
    https://your-workers-subdomain.workers.dev/chat
    ```
    
5.  **修改前端代码，让它请求 Cloudflare Workers，而不是直接请求 DeepSeek API**。

----------

## **✅ 方案 2：前端放 Hexo，后端放 Vercel**

如果你想自己写一个 **Node.js 服务器**，可以部署到 Vercel 或 Render。

**后端代码（Node.js，Express）**：

```js
const express = require("express");
const fetch = require("node-fetch");
const app = express();
app.use(express.json());

app.post("/chat", async (req, res) => {
    try {
        const response = await fetch("https://api.deepseek.com/v1/chat/completions", {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
                "Authorization": "Bearer sk-你的API密钥"
            },
            body: JSON.stringify({
                model: "deepseek-reasoner",
                messages: [{ role: "user", content: req.body.message }],
                temperature: 0.5
            })
        });

        if (!response.ok) throw new Error(`API错误 ${response.status}`);
        const data = await response.json();
        res.json({ reply: data.choices[0].message.content });
    } catch (error) {
        res.status(500).json({ reply: "后端错误：" + error.message });
    }
});

app.listen(3000, () => console.log("后端运行在端口 3000"));
```

然后：

```sh
npm install express node-fetch
node server.js
```

再部署到 **Vercel** 或 **Render**。

----------


以下是整理后的表格：

| 方案     | 前端         | 后端                          | 适用情况                     |
|----------|--------------|-------------------------------|------------------------------|
| 方案 1   | Hexo（静态） | Cloudflare Workers            | 适合 Hexo，只需代理 API       |
| 方案 2   | Hexo（静态） | Vercel/Render（Node.js）       | 后端可自定义                 |


 **推荐：**

-   **如果你只是想代理 API，推荐**✅ **Cloudflare Workers**（简单+免费）
-   **如果想自己管理后端，推荐**✅ **Vercel/Render**

----------

这样，你的 Hexo 站点 **只负责前端页面**，后端 API 交给 Cloudflare Workers 或 Vercel。  
🚀 **这样不仅安全，访问速度也更快！**

