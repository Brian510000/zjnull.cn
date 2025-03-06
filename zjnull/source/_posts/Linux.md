---
title: WSL2 Linux install
date: 2025-02-25 20:54:26
tags: ["Hexo", "博客"]
categories: ["技术"]
comments: true
cover: https://unsplash.com/photos/ZV_64LdGoao/download?ixid=M3wxMjA3fDB8MXxhbGx8fHx8fHx8fHwxNzQwNzY1MjQ1fA&force=true
---

# WSL2：将 Windows 与 Linux 无缝结合

在 Windows 环境中，如何高效运行 Linux 命令、使用 Windows 软件、并实现显卡直通等操作？答案就是 **WSL2**，即 **Windows Subsystem for Linux 2**，它让 Windows 与 Linux 的无缝结合成为可能。本文将深入了解 WSL2 的工作原理、安装步骤、使用技巧及高级功能。

## 1. WSL

WSL（Windows Subsystem for Linux）分为两个版本：WSL1 和 WSL2。WSL1 通过模拟 Linux 内核并将其系统调用翻译为 Windows NT 内核指令来实现兼容性。然而，这种方式并非完美，导致了某些功能无法支持（如 Docker），且性能和兼容性有所限制。

WSL2 相比 WSL1 具有显著的改进，它基于 Hyper-V 虚拟化平台，能够真正运行 Linux 内核，从而大大提升了兼容性和性能，解决了 WSL1 无法运行 Docker 等问题。

![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250225211326163.png)

## 2. **安装 WSL2 的前提条件**

要使用 WSL2，首先需要满足以下条件：

-   **开启 CPU 虚拟化**：检查 Windows 的 CPU 虚拟化是否已启用。可以在任务管理器中查看 CPU 选项中的“虚拟化”状态。若未开启，需要进入 BIOS 启用虚拟化。
> 一般情况下默认开启
-   **启用 Windows 功能**：在“启用或关闭 Windows 功能”中启用“适用于 Linux 的 Windows 子系统”和“虚拟机平台”。
![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250224205957695.png)
## 3. **WSL2 安装步骤**

1.  **检查并启用虚拟化**：确认 CPU 虚拟化已启用，在任务管理器中查看 CPU 性能。如果未启用，进入 BIOS 开启 Intel VMX 或 AMD-V。
2.  **安装 WSL2**：在命令提示符中运行以下命令：
	```
	wsl --install
	```
	或**更新**
	```
	wsl --update
	```
	![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250225194911175.png)
	>该命令会默认安装 Ubuntu 作为 Linux 发行版。如果网络环境不佳，可以加上 `--web-download` 选项以避免下载失败。
4.  **选择 Linux 发行版**：运行以下命令查看可用的发行版：

    ```
    wsl --list --online
    ```
    安装你选择的 Linux 发行版（如 Ubuntu-24.04、Kali Linux）：

    ```
    wsl --install Ubuntu-24.04
    ```
	![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250225194326821.png)
### 4. **管理 WSL2 子系统**

WSL2 允许你同时安装多个 Linux 子系统，可以通过命令查看当前安装的发行版列表：

```
wsl --list
```
![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250225195828630.png)
你可以设置默认的子系统：

```
wsl --setdefault <distribution_name>
```
启动 Linux 子系统，只需输入：

```
wsl
```
要停止子系统，只需关闭终端或使用：

```
wsl --shutdown
```
至此，Linux发行版安装成功，**wsl** 进入操作系统后，按照提示输入**用户名**、**密码**
![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250225200406366.png)
>NAT 模式下的 WSL 不支持 localhost 代理，WSL 2 可能检测到你在 Windows 上配置了代理（如 Clash、Proxifier），但 **如果 WSL 能正常运行，请忽略这个提示**

>默认情况下，WSL 只接受 **由小写字母（a-z）开头，且仅包含小写字母、数字和下划线（_）的用户名**
## 5. **文件共享与跨平台操作**

WSL2 支持 Windows 与 Linux 之间的文件共享。你可以在 Linux 中访问 Windows 文件系统（例如 C:\盘），并且可以通过 Windows 资源管理器访问 Linux 子系统的文件。
![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250225201200054.png)
例如，在 Linux 中访问 Windows 文件：
```
cd /mnt/c/Users/<username>/Documents
```
你还可以在 Windows 中直接调用 Linux 命令，反之亦然。例如，在 PowerShell 中使用 Linux 命令：
```
wsl ls
```
## 6. **显卡直通与 Docker 支持**

WSL2 支持显卡直通，特别是对于使用 NVIDIA 显卡的用户，可以直接在 Linux 环境中利用显卡进行计算（如 AI 模型训练等）。只需确保安装了相应的驱动程序和 CUDA 工具。

WSL2 还完美支持 Docker，你可以在 WSL2 中运行 Docker 容器，并利用 Docker Desktop 进行管理，享受高效的容器化开发环境。

## 7. **WSLG 与 GUI 支持**

通过 **WSLG**（Windows Subsystem for Linux GUI），你可以在 Windows 上运行带有 GUI 的 Linux 应用程序。WSLG 利用 RDP（远程桌面协议）使 Linux 图形界面应用能够在 Windows 环境中原生显示，提供更加流畅的用户体验。

例如，在 WSL2 中运行 GIMP（图像编辑软件）：

```
sudo apt install gimp gimp
```
该程序将直接以 Windows 窗口形式打开。

## 8. **高级配置与网络设置**

WSL2 提供了多种配置选项，可以进一步优化系统性能与兼容性。你可以编辑 `wsl.conf` 配置文件，启用 `systemd` 支持或修改网络配置，使虚拟机与宿主机共享同一 IP 地址，从而实现更加灵活的网络通信。
### 镜像代理配置
修改网络配置文件示例：
```.wslconfig
[wsl2]
networkingMode=mirrored
```
```
[network] generateResolvConf = false
```
>WSL 2 目前运行在 **NAT 网络模式**，若没有成功启用 **Mirrored 网络模式**，原因是你的 **Windows 版本（例如Windows 22621.1848）不支持该功能**

如果你希望 WSL 内部也能访问网络，并且你确实在 Windows 上启用了 Clash/Proxifier 之类的代理，可以手动设置代理：
```
echo "export http_proxy=http://127.0.0.1:7890" >> ~/.bashrc 
echo "export https_proxy=http://127.0.0.1:7890" >> ~/.bashrc
source ~/.bashrc`
```
>假设你的代理端口是 `7890`，如果不同，修改 `127.0.0.1:端口号`

>注意：应该使用 **无线局域网适配器 WLAN** 的 **IP 地址**，它是你的 **Windows 机器** 在局域网中的地址，是所有网络通信的基础。WSL 需要通过这个地址来访问代理，而**不是**Clash的代理地址


![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250225204024544.png)
这样 WSL 也能走代理访问网络，设置后，WSL 内部的所有 `curl`、`wget`、`apt update` 等命令都会走代理

### 关闭代理
在 WSL 中，运行以下命令来取消代理设置：
```
unset http_proxy
unset https_proxy
```
或者，你可以删除 `~/.bashrc` 中的代理设置行，然后重新加载配置文件：

```
sed -i '/http_proxy/d' ~/.bashrc
sed -i '/https_proxy/d' ~/.bashrc
source ~/.bashrc
```
### tips
**确认 Windows 代理是否开启**

在 Windows 上检查你的代理软件（Clash、V2rayN、Proxifier 等）是否**正确开启**，然后：

1.  **打开 CMD 或 PowerShell**（在 Windows 里，不是WSL），运行：
    ```powershell
    netstat -ano | findstr :7890
    ```
    **如果返回类似下面的结果，说明代理进程在监听 7890 端口：**
    ``` nginx
	TCP    127.0.0.1:7890       0.0.0.0:0       LISTENING       12345
    ```
    -   **如果没输出**，说明你的代理软件 **没有监听 7890 端口**，就需要检查代理软件的配置。


## 9. **总结**

WSL2 通过提供一个虚拟化的 Linux 内核，使得 Windows 用户能够享受原生的 Linux 环境，解决了许多兼容性问题，特别是在 Docker、显卡直通以及 GUI 应用程序支持方面。它大大提升了开发者的工作效率，使 Windows 成为一个理想的 Linux 开发平台。

无论你是开发者、系统管理员，还是 Linux 爱好者，WSL2 都能为你提供强大的功能和便捷的工作流程。

----------
