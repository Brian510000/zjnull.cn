---
title: 使用 IIS 搭建本地 Web 服务器
date: 2025-02-22 05:09:06
tags: ["Hexo"]
categories: ["技术"]
---

# 使用 IIS 搭建本地 Web 服务器

## 1. 搭建 IIS

### 1.1 启用 IIS 服务

1.  打开 **控制面板**，进入 **程序**
2.  点击 **启用或关闭 Windows 功能**
3.  在弹出的窗口中，勾选 **Internet 信息服务 (IIS)** 相关服务![](https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250222044445458.png)
4.  点击 **确定**，等待系统安装并配置 IIS

### 1.2 进入 IIS 管理器

1.  在 **开始菜单** 或 **运行 (Win + R)** 输入 `inetmgr` 并回车，打开 IIS 管理器。
2.  在左侧**连接**面板中，展开本地计算机的 IIS 站点管理。

### 1.3 添加网站（也可以直接使用 Default Web Site)

1.  在 **IIS 管理器** 中，右键 **网站**，选择 **添加网站**。
2.  在弹出的窗口中，配置以下参数：

    - **网站名称**：`yourname`
    - **物理路径**：选择本地存放网站文件的根目录。
    - **IP 地址**：
      - 可选择 `全部未分配`。
      - 也可以选择本机网卡地址（推荐），这样同一局域网下的设备可通过该地址访问。
    - **端口**：建议使用默认 `80` 端口，避免用户访问时手动输入端口号。
    - **主机名**：
      - 可以设置为自定义的访问网址。
      - 若端口不是 `80`，则访问时需使用 `网址:端口号` 形式。

3.  点击 **确定**，完成网站添加。

### 1.4 访问网站

- 在本地浏览器输入 `http://localhost`，若端口不是 80，则输入 `http://localhost:端口号` 访问。
- 在同一局域网的手机或其他设备上，可通过网卡地址访问网站，例如 `http://192.168.x.x`。
  > 在 CMD 中输入 ipconfig 可以直接查看本机 IP 地址
- 若设置了主机名，可直接在浏览器输入主机名访问（需确保本地 DNS 解析或 hosts 文件正确配置）。

---

至此，本地 IIS 服务器已成功搭建，并可在局域网内访问

## 2.基于 node.js 的静态文件部署方案

### 在 IIS 上运行 Hexo 博客

Hexo 生成的是纯静态文件（HTML、CSS、JS），而 IIS 主要用于运行 ASP.NET、PHP 或静态网站。因此，你可以按照以下步骤部署 Hexo

### 2.1 生成静态网页

在 Hexo 项目根目录执行以下命令，生成完整的静态网站：

```sh
hexo clean && hexo generate
```

生成的静态网站将存放在 `public/` 目录下。

### 2.2 部署到 IIS

1.  在 IIS 中创建一个新站点（或使用默认的 `wwwroot` 目录）。
2.  将 `public/` 目录的内容复制到 IIS 站点的根目录。
3.  确保 IIS 开启了静态文件服务：
    - 在 **IIS 管理器** 中，进入 **Handler Mappings**
      ![](https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250222045635576.png)
    - 确保 **StaticFile** 处理程序启用

### 2.3 绑定域名或访问

- 直接通过 `http://localhost/` 访问 Hexo
- 如果有域名，可以在 IIS 中绑定域名，并解析到你的服务器。

### 2.4 处理动态功能（如评论、搜索）

Hexo 是静态博客，没有数据库，IIS 也不需要运行 Node.js。但如果你有动态评论（如 **Valine**、**Gitalk**），它们仍然可以正常使用。

---

至此，本地 IIS 服务器已成功搭建，并支持 Hexo 博客的部署和访问

## 3. IIS 的 HTTP/HTTPS 规则

### 3.1 解决 API 访问问题（CORS 配置）

部分情况下，Hexo 博客的 **subtitles** 或其他远程 API 请求无法正确加载，这是因为 **IIS 默认禁止了远程 API 请求**。你可以通过 **CORS（跨域资源共享）** 配置来解决。

### **🛠 方法 1：修改 `web.config`（推荐）**

1.  **打开 Hexo 部署目录**（即 IIS 网站根目录）。
2.  **编辑 `web.config`**，如果文件不存在，则新建一个。
3.  **添加以下代码**：

```xml
<configuration>
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="Access-Control-Allow-Origin" value="*" />
        <add name="Access-Control-Allow-Methods" value="GET, POST, OPTIONS" />
        <add name="Access-Control-Allow-Headers" value="Content-Type, Authorization" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

4.  **保存文件并重启 IIS**：

```sh
iisreset
```

### **🛠 方法 2：IIS 管理器中手动配置**

如果你不想修改 `web.config`，可以在 **IIS 管理器** 中进行手动设置：

1.  **打开 IIS 管理器**：
    - 按 `Win + R`，输入 `inetmgr`，回车。
2.  **选择站点**：
    - 在左侧列表中，点击你的网站（如 `Default Web Site`）。
3.  **打开 HTTP 响应标头**：
    - 在 **功能视图（Features View）** 中，找到 **“HTTP 响应标头”** 并打开。
4.  **添加 CORS 相关标头**：
    - 右侧点击 **“添加”**，输入：
      - **名称：** `Access-Control-Allow-Origin`  
        **值：** `*`
      - **名称：** `Access-Control-Allow-Methods`  
        **值：** `GET, POST, OPTIONS`
      - **名称：** `Access-Control-Allow-Headers`  
        **值：** `Content-Type, Authorization`
5.  **重启 IIS**：

```sh
iisreset
```

---

大功告成！已成功配置 IIS 以支持 Hexo 全部渲染依赖，并允许远程 API 访问。如有需要，可进一步优化 HTTPS 配置，确保网站安全性
