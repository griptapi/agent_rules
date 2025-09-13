---
trigger: always_on
alwaysApply: true
weight: "8"
tags:
  - 命令执行
  - powershell
  - 终端
---

# PowerShell 调用规则

## 错误模式

### 1. 连续命令分隔符错误
- **错误**: `cd path && command` 
- **提示**: 标记"&&"不是此版本中的有效语句分隔符
- **解决**: 分步执行或使用批处理文件

### 2. 路径参数错误  
- **错误**: `dir d:\path /s`
- **提示**: 第二个路径段不得为驱动器或UNC名称
- **解决**: 使用 `Get-ChildItem -Recurse` 或切换到目标目录

### 3. 环境变量获取失败
- **错误**: 无法获取PATH等环境变量
- **解决**: 创建批处理文件(.bat)执行命令

## 正确模式

```powershell
# 分步执行
cd target_directory
python script.py

# 或使用批处理
@echo off
cd /d target_directory  
python script.py
```

## 强制要求
- 避免使用 `&&` 连接命令
- 复杂操作优先使用批处理文件
- 检测到PowerShell格式错误立即改用分步执行