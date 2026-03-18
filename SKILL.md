---
name: videocut
description: 口播视频剪辑 Agent。转录语音、AI 识别口误/重复/卡顿、网页审核、FFmpeg 剪辑、字幕生成与烧录。触发词：剪口播、处理视频、识别口误、加字幕、生成字幕
metadata: {"openclaw": {"emoji": "🎬", "requires": {"bins": ["node", "ffmpeg"], "env": ["VOLCENGINE_API_KEY"]}, "os": ["darwin", "linux"]}}
---

# Videocut — 口播视频剪辑 Agent

> 火山引擎转录 + AI 口误识别 + 网页审核 + FFmpeg 剪辑 + 字幕烧录

## 功能路由

根据用户意图选择对应流程：

| 意图 | 关键词 | 执行流程 |
|------|--------|----------|
| 环境安装 | 安装、初始化、环境准备 | → 读 [安装/guide.md](安装/guide.md) |
| 剪口播 | 剪口播、处理视频、识别口误 | → 读 [剪口播/guide.md](剪口播/guide.md) |
| 加字幕 | 字幕、加字幕、生成字幕 | → 读 [字幕/guide.md](字幕/guide.md) |
| 更新规则 | 更新规则、记录反馈、改进 | → 读 [自进化/guide.md](自进化/guide.md) |

**读取对应 guide.md 后，按其中步骤执行。**

## 快速使用

```
用户: 帮我剪这个口播视频 /path/to/video.mp4
用户: 给这个视频加字幕
用户: 安装环境
用户: 记录一下刚才的问题
```

## 脚本路径

所有脚本位于 `{baseDir}/剪口播/scripts/` 和 `{baseDir}/字幕/scripts/`：

| 脚本 | 用途 |
|------|------|
| `剪口播/scripts/volcengine_transcribe.sh` | 火山引擎语音转录 |
| `剪口播/scripts/generate_subtitles.js` | 生成字级别字幕 JSON |
| `剪口播/scripts/generate_review.js` | 生成审核网页 |
| `剪口播/scripts/review_server.js` | 审核服务器 + 剪辑执行 |
| `剪口播/scripts/cut_video.sh` | FFmpeg 剪辑（独立调用） |
| `字幕/scripts/subtitle_server.js` | 字幕审核服务器 |

调用示例（`{baseDir}` 为本 skill 目录）：

```bash
bash "{baseDir}/剪口播/scripts/volcengine_transcribe.sh" "<audio_url>"
node "{baseDir}/剪口播/scripts/generate_subtitles.js" volcengine_result.json
```

## 依赖

| 依赖 | 用途 | 安装 |
|------|------|------|
| Node.js 18+ | 运行脚本 | `brew install node` |
| FFmpeg | 音视频处理 | `brew install ffmpeg` |
| 火山引擎 API Key | 语音转录 | 环境变量 `VOLCENGINE_API_KEY` |

## 配置

API Key 通过环境变量 `VOLCENGINE_API_KEY` 注入（OpenClaw 的 `skills.entries.videocut.env` 或系统环境变量）。

词典文件：`{baseDir}/字幕/词典.txt`，每行一个热词，转录时自动加载。

## 输出目录结构

```
output/
└── YYYY-MM-DD_视频名/
    ├── 剪口播/
    │   ├── 1_转录/        # audio.mp3, volcengine_result.json, subtitles_words.json
    │   ├── 2_分析/        # readable.txt, auto_selected.json, 口误分析.md
    │   └── 3_审核/        # review.html
    └── 字幕/
        ├── 1_转录/        # audio.mp3, volcengine_result.json
        ├── subtitles_with_time.json
        └── 3_输出/        # .srt, _字幕.mp4
```
