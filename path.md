---
trigger: always_on
alwaysApply: true
tags:
  - 开发环境
  - 编译器路径
weight: "8"
---


# 开发环境路径信息

## Visual Studio 2019 编译环境
- **企业版路径**: `C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise`
- **x64编译环境**: `%comspec% /k "C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Auxiliary\Build\vcvars64.bat"`
- **x86编译环境**: `%comspec% /k "C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\Common7\Tools\VsDevCmd.bat"`
- **状态**: 已确认安装Enterprise版本
- **调用方式**: 使用批处理文件设置环境变量后调用MSBuild/cl.exe
- **备注**: 解决了之前MSBuild和cl.exe不在PATH中的问题

## 编译工具具体路径
- **cl.exe**: `C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.28.29333\bin\Hostx64\x64\cl.exe`
- **MSBuild.exe (VS)**: `C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\MSBuild\Current\Bin\MSBuild.exe`
- **MSBuild.exe (.NET)**: `C:\Windows\Microsoft.NET\Framework64\v4.0.30319\MSBuild.exe`
- **devenv.exe**: `C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\Common7\IDE\devenv.exe`

## 强制要求
- 使用已知路径信息
- 遇到环境问题直接创建批处理解决
- Visual Studio编译环境优先使用vcvars64.bat设置环境

## PowerShell调用方式
- **设置x64环境并执行命令**: `Start-Process cmd -ArgumentList '/c', '""C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Auxiliary\Build\vcvars64.bat" && 要执行的命令"' -NoNewWindow -Wait`
- **注意事项**: PowerShell中不能直接使用&&连接命令，需要通过上述方式调用cmd执行批处理文件