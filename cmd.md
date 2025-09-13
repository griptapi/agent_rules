---
trigger: always_on
alwaysApply: true
weight: "8"
tags:
  - 命令执行
  - cmd
  - 终端
---


# CMD 命令调用规则

## 错误模式
- **错误**: 直接在PowerShell中使用CMD语法
- **问题**: 语法不兼容导致执行失败

## 正确模式

### 批处理文件方案
- **注意**: 批处理仅允许使用ANSI编码，否则会显示乱码
```batch
@echo off
cd /d "d:\workspace\projects\md5"
"D:\Programs\Python\Python3\python.exe" run_tests.py
pause
```

### 直接调用
```cmd
cmd /c "cd /d target && python script.py"
```

## 强制要求
- 复杂命令序列使用批处理文件
- Python执行使用完整路径
- 避免混用PowerShell和CMD语法