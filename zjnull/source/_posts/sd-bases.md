---
title: Stable Diffusion 十分钟上手
date: 2025-03-07 02:09:35
tags: ["Stable Diffusion", "ComfyUI"]
categories: ["技术"]
comments: true
cover: https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250307022542367.png
---



# ComfyUI

ComfyUI 和 WebUI都属于 **Stable Diffusion 前端界面（Frontends）**，ComfyUI 相较于传统的 WebUI 具有显著优势，其基于节点的可视化工作流设计，类似于 Blender 和 UE5 的节点编辑器，有更强的可控性，可以自由调整模型的每个处理阶段，如调整噪声、改变 CFG 影响力、插入额外 Lora 处理等，而无需依赖特定插件。此外，**ComfyUI 在显存管理上更为高效**，节省 VRAM，支持更大尺寸的图片生成，并且由于是纯 Python + CUDA 直接运行，避免了 Web 端加载 JS 组件带来的性能开销。ComfyUI 原生支持 SDXL，可以无缝切换 Base Model 和 Refiner，支持多模型混合，后期依照工作流自定义生成逻辑，拆分 ControlNet 处理到前置步骤或对噪声输入进行特殊调整，**使用起来真的非常非常一目了然**，试试便知，感觉比WebUI有意思。所以，我选择使用ComfyUI来开始我们的Stable Diffusion之旅
![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250307015435938.png)

## 1. 准备环境

ComfyUI 需要 **Python 3.10+**，并且建议使用 **CUDA 兼容的 NVIDIA GPU** 以获得更快的推理速度

> 本篇全程需要网络代理，第一次代理请参考[Clash 网络代理 | ZJNULL](https://zjnull.cn/clash/)


### **Windows**

1.  **安装 Python**
    
    -   下载 [Python 3.10+](https://www.python.org/downloads/) 并安装。
    -   勾选 **Add Python to PATH**，以便命令行可用 Python。
2.  **安装 Git**（可选）
    
    -   下载 [Git](https://git-scm.com/downloads) 并安装。
    -   可用于克隆仓库。
3.  **下载并安装 ComfyUI**

	**系统解压安装：**
	
    [点击这里下载发行版](https://github.com/comfyanonymous/ComfyUI/releases/download/v0.3.23/ComfyUI_windows_portable_nvidia.7z)
    >解压安装到你存储比较大的盘符，因为我们需要大量的空间来保存数据和模型
    
	![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250307004800918.png)


	**命令行安装：**

	```bash
    git clone https://github.com/comfyanonymous/ComfyUI.git
    cd ComfyUI
    python -m venv venv
    venv\Scripts\activate  # 激活虚拟环境
    pip install -r requirements.txt  # 安装依赖
    ```


	启动 ComfyUI
    
    
    ```bash
    python main.py
    ```
    ![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250307014359258.png)
    启动后，打开浏览器访问 [http://127.0.0.1:8188](http://127.0.0.1:8188/) 进入 GUI 界面。
    

### **Linux (Ubuntu/Debian)**

1.  **安装依赖**
    
    ```bash
    sudo apt update && sudo apt install git python3 python3-venv
    ```
    
2.  **克隆并安装 ComfyUI**
    
    ```bash
    git clone https://github.com/comfyanonymous/ComfyUI.git
    cd ComfyUI
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    ```
    
3.  **运行 ComfyUI**
    
    ```bash
    python main.py
    ```
    

----------

# Stable Diffusion模型

## 从 Hugging Face 或 Civitai 下载

ComfyUI 需要 **Stable Diffusion** 相关模型（Checkpoint、LoRA 等），可从以下站点下载：

-   [Hugging Face](https://huggingface.co/)
-   [Civitai](https://civitai.com/)
>这里提供一个比较主流的模型SD1.5，[stable-diffusion-v1-5](https://huggingface.co/Comfy-Org/stable-diffusion-v1-5-archive/tree/main)

![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250307011557016.png)

>该模型经过 裁剪（Pruned），去掉了一些不必要的参数，减少了模型大小，采用 FP16（半精度浮点数）进行存储，相比 FP32（全精度浮点数）可以 **减少显存占用，提高推理速度**，但可能会有 极小的精度损失，且仅保留了 EMA（Exponential Moving Average）权重，EMA 版的模型在训练时使用指数移动平均计算权重，通常比未使用 EMA 的模型更稳定、生成的图像质量更高，**适合新手的快速启动、试水**
## 常见模型存放位置

-   **主模型（Checkpoint）** → 放入 `ComfyUI/models/checkpoints/`
-   **VAE 模型** → 放入 `ComfyUI/models/vae/`
-   **LoRA/Refiner** → 放入 `ComfyUI/models/lora/`
![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250307013253363.png)
## 如何加载模型

-   进入 ComfyUI，在 `Checkpoint Loader` 选择你的 `.safetensors` 模型
-   如果安装 `ComfyUI-Manager` 插件，可直接管理模型
	>详情戳此到达[ComfyUI进阶]
----------

# 大模型介绍
>简单讲讲，各类型的关系会放到另外一篇，本篇主要面向快速上手，现在简单会区分一下就行
## 1. **Checkpoint（基础模型）**

Checkpoint（.safetensors/.ckpt）是 Stable Diffusion 训练出来的主模型。

-   **SD 1.5** → 适合动漫、写实风格
-   **SDXL（Stable Diffusion XL）** → 适合高清、写实和细节丰富的图片

## 2. **Refiner（细化器）**

Refiner 是 **二阶段处理**，用于增强细节

-   **SDXL Base + SDXL Refiner** 组合可以提升质量。
-   Refiner 也可以与其他 Checkpoint 结合使用

## 3. **LoRA（局部调整模型）**

LoRA 可用于 **增强特定风格或人物**，占用显存小，适合细节补充

## 4. **VAE（变分自编码器）**

VAE 主要用于修复色彩、增强细节

----------

# 工作流

## 文本生成图片（txt2img）
> 首先按照流程图搭建工作流

![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250307015435938.png)

在这里创建或检索需要的节点
![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250307020320168.png)
### **步骤 1：加载模型**

-   **添加 `Checkpoint Loader` 节点**
-   **选择 `SD 1.5` 或 `SDXL` 模型**

### **步骤 2：输入 Prompt**

-   **添加 `CLIP Text Encode` 节点**
-   在 `Positive Prompt` 中输入提示词
-   在 `Negative Prompt` 中输入屏蔽词

### **步骤 3：配置 K 采样器**

-   连接 `CLIP Text Encode` 到 `KSampler`
-   选择 `Euler A`、`DPM++ 2M Karras` 采样方法
-   设定 `Steps=30` `CFG=7`

### **步骤 4：解码与输出**

-   连接 `KSampler` 到 `VAE Decode`
-   连接 `VAE Decode` 到 `Image Output`
-   运行 ComfyUI，查看生成结果

----------

## **结语**

简而言之，言而简之，我们需要的其实总共就三样东西，**ComfyUI、主模型、工作流**，上手简易直观，祝你游玩愉快。
以上就是 ComfyUI 的基本安装、模型下载和基础工作流搭建，后续可以探索 `img2img`、`ControlNet` 等更高级的用法！ 🚀