---
title: DS-R1
date: 2025-03-08 14:14:13
type: 'DS-R1'
---

{% raw %}

<style>
    #chatbox {
        width: 80%;
        height: 400px;
        border: 1px solid #ccc;
        overflow-y: auto;
        margin: 20px auto;
        padding: 10px;
    }
    #userInput {
        width: 70%;
        padding: 10px;
    }
    #sendButton {
        padding: 10px;
        cursor: pointer;
    }

    .input-box {
        width: 100%;
        /* max-width: 350px; */
        /* 限制最大宽度，适配桌面和移动端 */
        padding: 12px 16px;
        font-size: 16px;
        color: #333;
        border: 2px solid #ccc;
        border-radius: 15px;
        /* 圆角效果 */
        outline: none;
        background: #f9f9f9;
        transition: all 0.3s ease-in-out;
        /* 动画过渡 */
    }
    /* 🌟 聚焦时：边框变色 + 放大 + 高亮效果 */
    .input-box:focus {
        border-color: #4caf50;
        /* 聚焦时边框变绿色 */
        box-shadow: 0 0 8px rgba(76, 175, 80, 0.5);
        /* 高亮阴影 */
        background: #ffffff;
        /* 背景变白 */
    }
    /* 🌟 输入框悬停时：轻微放大 + 阴影 */
    .input-box:hover {
        transform: scale(1.05);
        /* 悬停时放大 */
        box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
        /* 悬停时阴影效果 */
    }
    /* 🌟 占位符样式 */
    .input-box::placeholder {
        color: #aaa;
        /* 轻灰色占位符 */
        transition: opacity 0.3s ease-in-out;
    }
    /* 🌟 聚焦时隐藏占位符 */
    .input-box:focus::placeholder {
        opacity: 0;
        /* 聚焦时隐藏占位符 */
    }
    /* 🌟 与下行文本保持合理间距 */
    .input-container {
        display: flex;
        flex-direction: column;
        gap: 12px;
        /* 设置输入框与下行文本的间距 */
    }
    /* 基础按钮样式 */
    .btn {
        width: 15%;
        /* 宽度占满父容器 */
        padding: 12px 20px;
        /* 内边距 */
        border: none;
        /* 去除边框 */
        border-radius: 30px;
        /* 圆角 */
        padding: 12px 30px;

        font-size: 16px;
        /* 字体大小 */
        font-weight: bold;
        /* 字体加粗 */
        text-align: center;
        /* 文字居中 */
        color: #fff;
        /* 文字颜色 */
        background: linear-gradient(135deg, #5cbe5f, #45a049);
        /* 渐变绿色背景 */
        cursor: pointer;
        /* 鼠标指针变成点击手型 */
        transition: all 0.3s ease;
        /* 过渡效果 */
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        /* 阴影效果 */
    }
    /* 悬停效果 */
    .btn:hover {
        background: linear-gradient(135deg, #45a049, #388e3c);
        /* 悬停时深绿色渐变 */
        transform: scale(1.2);
        /* 按钮放大 */
        box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
        /* 阴影加深 */
    }
    /* 点击效果 */
    .btn:active {
        transform: scale(0.98);
        /* 点击时稍微缩小 */
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        /* 还原阴影 */
    }
    /* 禁用状态 */
    .btn:disabled {
        background-color: #ccc;
        /* 灰色背景 */
        cursor: not-allowed;
        /* 禁用时鼠标指针变成不可点击样式 */
        box-shadow: none;
        /* 取消阴影 */
    }
    /* 按钮文本 */
    .btn span {
        font-size: 18px;
    }
</style>

<body>
    <span style="font-family: Arial, sans-serif;text-align: center;">
    <h1>GPT Chat</h1>
    <div id="chatbox"></div>
    <div  style="font-family: Arial, sans-serif;text-align: center;">
    <input class="input-box" type="text" id="userInput" placeholder="输入你的消息">
    <button class="btn" style="width: 15%;" id="sendButton">发送</button>
    <p></p>
    <a class="btn" href="https://zjnull.cn" style="text-decoration: none; color: #fff;" target="_blank">返回主页</a>
    <a class="btn" href="index0.html" style="text-decoration: none; color: #fff;" target="_blank">旧版页面</a>
    </div>
    <script>
        // 🚨 关键配置
        const apiKey = "sk-ba784d45abaa4518b5714f5e74ba9c5b"; // 没钱开服务器，只能写在前端QAQ
        const chatbox = document.getElementById("chatbox");
        const userInput = document.getElementById("userInput");
        const sendButton = document.getElementById("sendButton");
        // 消息展示函数
        function appendMessage(sender, message) {
            const msgDiv = document.createElement("div");
            msgDiv.innerHTML = `<strong>${sender}:</strong> <br>${message}`;
            chatbox.appendChild(msgDiv);
            chatbox.scrollTop = chatbox.scrollHeight;
        }
        // 发送消息函数
        async function sendMessage() {
            const userMessage = userInput.value.trim();
            if (!userMessage) return;
            appendMessage("我", userMessage);
            userInput.value = "";
            try {
                const response = await fetch("https://api.deepseek.com/v1/chat/completions", {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json",
                        "Authorization": `Bearer ${apiKey}`,
                        "Accept": "application/json"
                    },
                    body: JSON.stringify({
                        model: "deepseek-reasoner",
                        messages: [{ role: "user", content: userMessage }],
                        temperature: 0.5
                    })
                });
                // 处理HTTP错误状态码
                if (!response.ok) {
                    throw new Error(`HTTP错误 ${response.status}`);
                }
                const data = await response.json();
                if (data.choices?.[0]?.message?.content) {
                    appendMessage("DeepSeek", data.choices[0].message.content);
                } else {
                    appendMessage("错误", "响应结构异常：" + JSON.stringify(data));
                }
            } catch (error) {
                appendMessage("错误", `请求失败: ${error.message}`);
            }
        }
        // 事件绑定
        sendButton.addEventListener("click", sendMessage);
        userInput.addEventListener("keypress", (event) => {
            if (event.key === "Enter") sendMessage();
        });
    </script>
</span>
</body>

{% endraw %}
