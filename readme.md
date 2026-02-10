
# BGI-V1-Translation-Toolkit

一个针对 BGI (Buriko General Interpreter) 旧引擎游戏的汉化工具箱。

基于 [xupefei/BGIKit](https://github.com/xupefei/BGIKit) 二次开发，核心修改了编码逻辑，配合自动化批处理脚本，旨在实现免 Hook、原生读取的汉化工作流。

## ✨ 核心特性

- **原生 Shift-JIS 支持**：将封包编码从原项目的 GBK (CP936) 修改为 Shift-JIS (CP932)。这意味着生成的脚本可以直接被日文原版游戏引擎读取，无需使用 DLL 注入或 Hook 工具进行边缘检测。
- **自动化工作流**：通过 `_Process.bat` 一键完成解包、封包、后缀清理和文件归档。
- **开箱即用**：内置预编译好的 `ScriptDecoder.exe` 和 `ScriptEncoder.exe`。

## 📂 目录结构与准备

在开始之前，请确保你的工作目录结构如下（运行脚本会自动创建缺失文件夹）：

```
ProjectRoot/
├── Tools/                  # 存放核心工具 (ScriptDecoder/Encoder)
├── 0_GARbro_Output/        # [输入] 放入从 data.arc 提取的原始脚本文件
├── 1_Decoded_TXT/          # [输出] 解包出的文本 (在此处提取待翻译文本)
├── 2_Translated_TXT/       # [输入] 放入翻译好的文本文件
├── 3_Encoded_Scripts/      # [输出] 封包后的临时文件 (.new)
├── 4_Final_Scripts/        # [最终] 可直接用于游戏的最终脚本
└── _Process.bat            # 核心批处理脚本
```

## 🚀 使用流程

### 第一步：准备原始脚本

使用解包工具（推荐 [GARbro](https://github.com/crskycode/GARbro)）从游戏的 `data.arc` 中提取所有脚本文件，并将它们直接放入 `0_GARbro_Output/` 文件夹中。

**注意**：原始脚本通常没有后缀名。

### 第二步：解包 (Decode)

运行 `_Process.bat` 并选择 **选项 1**。

- 程序会将 `0_GARbro_Output/` 中的脚本转换为可编辑的 `.txt` 文件
- 结果生成于 `1_Decoded_TXT/` 目录

### 第三步：翻译

对 `1_Decoded_TXT/` 中的文件进行翻译，完成后将文件放入 `2_Translated_TXT/`。

> 💡 **GalTransl 用户提示**
> 
> 如果你使用 [GalTransl](https://github.com/xd2333/GalTransl) 的 DumpInjector，请使用以下正则表达式来正确提取文本（仅提取第一个捕获组）：
> 
> ```regex
> <\d+,\d+,\d+>\s*([^\x00-\x7F].{3,})
> ```
> 
> 该规则用于过滤掉控制符，仅保留实际对话文本。

### 第四步：封包 (Encode)

运行 `_Process.bat` 并选择 **选项 2**。

- 程序会自动匹配 `0_GARbro_Output`（源文件）和 `2_Translated_TXT`（翻译文本）
- 生成的 `.new` 脚本文件位于 `3_Encoded_Scripts/`

### 第五步：清理与测试

运行 `_Process.bat` 并选择 **选项 3**。

- 程序会移除 `.new` 后缀并将最终文件移动到 `4_Final_Scripts/`

**如何测试**：

直接将 `4_Final_Scripts/` 中的所有文件复制到游戏根目录（与游戏 `.exe` 同级）。BGI 引擎会优先读取文件夹中的脚本，从而覆盖 `.arc` 包内的原始内容。

## 📦 打包与发布 (可选)

当汉化测试无误后，你可以选择以下方式发布补丁：

### 散文件补丁 (推荐)

- 直接将 `4_Final_Scripts/` 文件夹内的所有文件打包为 ZIP 压缩包
- 玩家只需解压到游戏根目录即可生效

### 单文件封装

- 为了更整洁的分发,可以使用虚拟化打包工具（如 [Molebox](https://github.com/sudachen/Molebox)）
- **操作方法**：将游戏主程序 (`.exe`) 和 `4_Final_Scripts/` 下的所有脚本文件打包成一个新的独立可执行文件
- 这样玩家无需替换文件，直接运行该 EXE 即可体验汉化版

## 🛠️ 技术细节

### 关于编码修改

原版 BGIKit 设计为输出 GBK 编码，这在没有 Hook 转换编码的情况下会导致日文游戏引擎崩溃。

本项目修改了 `ScriptEncoder` 的源码，强制输出 Shift-JIS (CP932) 编码。只要你的汉化文本包含在 Shift-JIS 字符集内（或使用了日文汉字替代），游戏即可原生显示中文，无需任何修改版 EXE 或 DLL。

### 编译说明 (可选)

本发布包已包含编译好的二进制文件。如果你需要自行编译修改版源码：

- **环境**：Visual Studio Build Tools (.NET Framework 3.5, MSBuild)
- **命令**：`msbuild /p:Configuration=Release`

## 📝 Credits

- 原始工具开发：[xupefei/BGIKit](https://github.com/xupefei/BGIKit)
- GARbro：[GARbro](https://github.com/crskycode/GARbro)
