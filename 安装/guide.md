<!--
子流程：安装
input: 无
output: 环境就绪
由根目录 SKILL.md 路由到此文件
-->

# 安装

> 首次使用前的环境准备

## 快速使用

```
用户: 安装环境
用户: 初始化
```

## 依赖清单

| 依赖 | 用途 | 安装命令 |
|------|------|----------|
| Node.js | 运行脚本 | `brew install node` |
| FFmpeg | 视频剪辑 | `brew install ffmpeg` |
| curl | API 调用 | 系统自带 |

## API 配置

### 火山引擎语音识别

控制台：https://console.volcengine.com/speech/new/experience/asr?projectName=default

1. 注册火山引擎账号
2. 开通语音识别服务
3. 获取 API Key
4. 设置环境变量 `VOLCENGINE_API_KEY`

## 安装流程

```
1. 安装 Node.js + FFmpeg
       ↓
2. 配置火山引擎 API Key
       ↓
3. 验证环境
```

## 执行步骤

### 1. 安装依赖

```bash
# macOS
brew install node ffmpeg

# 验证
node -v
ffmpeg -version
```

### 2. 配置 API Key

通过 OpenClaw 配置注入环境变量，或直接设置：

```bash
export VOLCENGINE_API_KEY=your_key
```

OpenClaw 用户可在 `~/.openclaw/openclaw.json` 中配置：

```json
{
  "skills": {
    "entries": {
      "videocut": {
        "env": { "VOLCENGINE_API_KEY": "your_key" }
      }
    }
  }
}
```

### 3. 验证环境

```bash
node -v
ffmpeg -version
echo $VOLCENGINE_API_KEY
```

## 常见问题

### Q1: API Key 在哪获取？

火山引擎控制台 → 语音技术 → 语音识别 → API Key

### Q4: OpenClaw 里怎么配置 API Key？

在 `~/.openclaw/openclaw.json` 的 `skills.entries.videocut.env` 中设置 `VOLCENGINE_API_KEY`。

### Q2: ffmpeg 命令找不到

```bash
which ffmpeg  # 应该输出路径
# 如果没有，重新安装：brew install ffmpeg
```

### Q3: 文件名含冒号报错

FFmpeg 命令需加 `file:` 前缀：

```bash
ffmpeg -i "file:2026:01:26 task.mp4" ...
```
