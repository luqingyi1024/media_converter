# 媒体格式转换器 (Media Converter)

基于 **Visual Studio 2022/2026 + 纯 Win32 C++** 开发的 Windows 桌面软件，
支持**图片 / 音频 / 视频**三大类文件的任意格式互转，界面采用扁平化设计。

## 功能特性

### 1. 图片格式转换（基于 Windows WIC 组件）
- 输入任意常见格式图片，输出为任意目标格式
- 支持：**PNG / JPEG / BMP / GIF / TIFF / HEIC** 等（依赖系统 WIC 编码器）
- **WebP / ICO / AVIF** 通过内置 ffmpeg 兜底
- 内置图片预览，转换后自动刷新
- JPEG 输出采用 0.92 高质量参数

### 2. 音频格式转换（基于内置 ffmpeg）
支持格式：**MP3 / WAV / FLAC / AAC / M4A / OGG / OPUS / WMA / AC3 / AIFF / AMR / MKA**

### 3. 视频格式转换（基于内置 ffmpeg）
支持格式：**MP4 / MKV / MOV / AVI / WMV / FLV / WebM / M4V / MPEG / TS / M2TS / OGV / 3GP / VOB / MXF / GIF 动图**
另支持「仅提取音频 → MP3」。

## 界面设计
- 扁平化风格，圆角卡片 + 自绘按钮
- 顶部深色标题栏
- 三个标签页（图片 / 音频 / 视频）切换
- 实时转换日志输出
- 状态栏用颜色区分成功/失败
- 自定义应用图标(标题栏 / 任务栏 / 文件资源管理器)

## 目录结构

```
MediaConverter/
├── src/
│   ├── app.h          # 公共接口声明
│   ├── util.cpp       # 路径/编码工具
│   ├── image.cpp      # 图片转换(WIC + ffmpeg 兜底)
│   ├── ffmpeg.cpp     # ffmpeg 进程调用封装
│   ├── audio.cpp      # 音频转换
│   ├── video.cpp      # 视频转换
│   ├── main.cpp       # 主界面与交互
│   └── testmain.cpp   # 命令行测试工具
├── res/
│   ├── app.ico        # 应用程序图标(多尺寸)
│   ├── app.rc         # 图标资源脚本
│   └── app.res        # 编译后的资源
├── build/
│   ├── MediaConverter.exe
│   └── ffmpeg.exe     # 音频/视频转换依赖
├── build.bat          # 一键编译脚本
└── ffmpeg.exe         # 音频/视频转换依赖(源)
```

## 编译方法

在项目目录执行：

```bat
build.bat
```

或手动使用 MSVC（需 Visual Studio 2022+）：

```bat
rem 先编译资源(图标)
rc /nologo /fo res\app.res res\app.rc

rem 再编译链接主程序
cl /nologo /EHsc /std:c++17 /O2 /MT /utf-8 /DUNICODE /D_UNICODE ^
   /Fo:build\ /Fe:build\MediaConverter.exe ^
   src\main.cpp src\util.cpp src\image.cpp src\ffmpeg.cpp src\audio.cpp src\video.cpp ^
   res\app.res ^
   /link /SUBSYSTEM:WINDOWS ^
   ole32.lib oleaut32.lib windowscodecs.lib shlwapi.lib comctl32.lib comdlg32.lib shell32.lib user32.lib gdi32.lib advapi32.lib
```

## 运行依赖

- **图片转换**：无需额外依赖（使用系统 WIC）
- **音频 / 视频转换**：需将 `ffmpeg.exe` 放在与 `MediaConverter.exe` 相同目录

## 技术要点

| 模块 | 技术方案 |
|------|----------|
| 图片编解码 | Windows Imaging Component (WIC) |
| 音频/视频编解码 | ffmpeg（子进程 + 管道实时读取日志） |
| 界面 | 纯 Win32 API + Common Controls，自绘扁平化控件 |
| 并发 | 转换在独立工作线程执行，避免界面卡死 |
