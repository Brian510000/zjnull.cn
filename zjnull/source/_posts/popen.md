---
title: subprocess.Popen不在工作目录中的解决方法
date: 2025-03-02 22:55:29
tags: ["Python", "自动化"]
categories: ["技术"]
---

双击可以打开程序而 `subprocess.Popen` 无法打开程序，这里拿我安装在D盘目录下的OBS举例，有以下六个原因。以下是详细分析和解决方法：

>通常会是工作目录的问题，默认情况下，`Popen` 的工作目录是 Python 脚本的工作目录。如果程序依赖其工作目录中的文件（如配置文件、资源文件等），可能会导致程序无法正常运行
---

### **1. 路径问题**
- **双击打开**：
  - 双击快捷方式或可执行文件时，操作系统会自动解析路径并运行程序。
  - 如果路径中包含空格或特殊字符，操作系统会正确处理。

- **`subprocess.Popen`**：
  - 如果路径中包含空格或特殊字符，需要确保路径被正确传递。
  - 如果路径未正确转义或未用引号括起来，可能会导致路径解析失败。

**解决方法**：
- 使用原始字符串（`r"..."`）或双反斜杠（`\\`）来避免转义问题。
- 如果路径中包含空格，用引号将路径括起来。

**示例**：
```python
import subprocess

# 正确传递路径
obs_path = r'"D:\obs-studio\bin\64bit\obs64.exe"'
subprocess.Popen(obs_path, shell=True)
```

---

### **2. 工作目录问题**
- **双击打开**：
  - 双击运行时，程序的工作目录通常是程序所在的目录。
  - 例如，`D:\obs-studio\bin\64bit\obs64.exe` 的工作目录是 `D:\obs-studio\bin\64bit`。

- **`subprocess.Popen`**：
  - 默认情况下，`Popen` 的工作目录是 Python 脚本的工作目录。
  - 如果程序依赖其工作目录中的文件（如配置文件、资源文件等），可能会导致程序无法正常运行。


	![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250302231014206.png)

	>未能找到本地初始化文件

**解决方法**：
- 使用 `cwd` 参数指定程序的工作目录。

**示例**：
```python
import subprocess

# 指定工作目录
obs_path = r"D:\obs-studio\bin\64bit\obs64.exe"
working_dir = r"D:\obs-studio\bin\64bit"

subprocess.Popen([obs_path], cwd=working_dir)
```

---

### **3. 环境变量问题**
- **双击打开**：
  - 程序继承当前用户的桌面环境变量。
  - 如果程序依赖某些环境变量（如 `PATH`），双击运行时可以正常找到这些变量。

- **`subprocess.Popen`**：
  - 程序继承 Python 脚本的环境变量。
  - 如果 Python 脚本的环境变量与桌面环境变量不同，可能会导致程序无法正常运行。

**解决方法**：
- 使用 `env` 参数传递自定义环境变量。

**示例**：
```python
import subprocess
import os

# 自定义环境变量
custom_env = os.environ.copy()
custom_env['PATH'] = r"D:\obs-studio\bin\64bit;" + custom_env['PATH']

# 启动程序并传递环境变量
obs_path = r"D:\obs-studio\bin\64bit\obs64.exe"
subprocess.Popen([obs_path], env=custom_env)
```

---

### **4. 权限问题**
- **双击打开**：
  - 如果当前用户有权限运行程序，双击可以正常启动。

- **`subprocess.Popen`**：
  - 如果 Python 脚本没有管理员权限，而程序需要管理员权限才能运行，`Popen` 会失败。

**解决方法**：
- 以管理员权限运行 Python 脚本，或者使用 `runas` 命令提升权限。

**示例**：
```python
import subprocess

# 以管理员权限运行程序
obs_path = r"D:\obs-studio\bin\64bit\obs64.exe"
subprocess.Popen(['runas', '/user:Administrator', obs_path], shell=True)
```

---

### **5. 程序依赖问题**
- **双击打开**：
  - 程序可能依赖某些动态链接库（DLL）或其他资源文件，双击运行时可以正常加载这些依赖。

- **`subprocess.Popen`**：
  - 如果程序依赖的 DLL 或资源文件不在 `PATH` 环境变量中，或者工作目录不正确，可能会导致程序无法正常运行。

**解决方法**：
- 确保程序依赖的文件在 `PATH` 环境变量中，或者使用 `cwd` 参数指定正确的工作目录。

---

### **6. 调试和日志**
- 如果问题仍然存在，可以通过捕获程序的输出日志来进一步分析问题。

**示例**：
```python
import subprocess

# 启动程序并捕获输出
obs_path = r"D:\obs-studio\bin\64bit\obs64.exe"
process = subprocess.Popen([obs_path], stdout=subprocess.PIPE, stderr=subprocess.PIPE)
stdout, stderr = process.communicate()

print("标准输出:", stdout.decode())
print("标准错误:", stderr.decode())
```

---

### **总结**
| 问题类型         | 双击打开                          | `subprocess.Popen` 打开               | 解决方法                              |
|------------------|-----------------------------------|---------------------------------------|---------------------------------------|
| **路径问题**     | 自动解析路径                      | 需要正确转义路径                      | 使用原始字符串或引号括起路径          |
| **工作目录问题** | 使用程序所在目录                  | 使用 Python 脚本的工作目录            | 使用 `cwd` 参数指定工作目录           |
| **环境变量问题** | 继承桌面环境变量                  | 继承 Python 脚本的环境变量            | 使用 `env` 参数传递自定义环境变量     |
| **权限问题**     | 继承当前用户权限                  | 继承 Python 脚本权限                  | 以管理员权限运行脚本或程序            |
| **依赖问题**     | 自动加载依赖                      | 依赖文件可能未找到                    | 确保依赖文件在 `PATH` 或工作目录中    |

通过以上方法，你应该能够解决 `subprocess.Popen` 无法打开程序的问题。如果问题仍然存在，请提供更多详细信息（如错误日志、程序路径等），以便进一步分析

以下是示例代码

```python
import subprocess
import time

# 启动
obs_path = r"程序地址"
process = subprocess.Popen([obs_path], stdout=subprocess.PIPE, stderr=subprocess.PIPE)
time.sleep(5)

# 打印输出日志
stdout, stderr = process.communicate()
if stdout:
    print("输出:", stdout.decode())
if stderr:
    print("错误:", stderr.decode())
```