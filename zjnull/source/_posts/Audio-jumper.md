---
title: Audio jumper
date: 2025-02-18 21:06:33
categories: ["技术"]
tags: ["Hexo", "博客","防遗忘"]
---

# Voicemeeter 音频跳线

## 第一步：启动 Voicemeeter
- 先打开 `voicemeeter8.exe` 软件，确保其正常运行。

## 第二步：更改系统声音设置
### 录制设备
- **只启用** `Voicemeeter Output`，并将其**设为默认设备**。

### 播放设备
- **只启用** `Voicemeeter VAIO3 INPUT`。
- **注意**：在播放设备中，还需确保自己的耳机**已启用**，但**无需设为默认设备**，否则可能影响正常使用。

## 第三步：软件设置
- 进入 Voicemeeter 软件的**HARDWARE OUT（硬件输出）**设置。
- 在**通道 A1** 处，选择带有**前缀 WDM** 的耳机设备。
  - 例如，如果你的耳机设备是 `音响(Realtek(R) Audio)`，则应选择 `WDM: 音响(Realtek(R) Audio)`。
- **此步骤至关重要**，确保正确选择输出设备，以避免音频异常。

设置完成后，即可正常使用 Voicemeeter 进行音频管理！