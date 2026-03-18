# Videocut Skills

> 用 AI Agent 剪辑口播视频 — 支持 OpenClaw / Claude Code / Cursor

## 为什么做这个？

剪映的"智能剪口播"有两个痛点：
1. **无法理解语义**：重复说的句子、说错后纠正的内容，它识别不出来
2. **字幕质量差**：专业术语（Claude Code、MCP、API）经常识别错误

这个 Agent 用 AI 的语义理解能力解决第一个问题，用自定义词典解决第二个问题。

## 效果演示

**输入**：19 分钟口播原片（各种口误、卡顿、重复）

**输出**：
- 自动识别 608 处问题（静音 114 + 口误/重复 494）
- 剪辑后视频 72MB
- 全程 AI 辅助，人工只需确认

## 核心功能

| 功能 | 说明 | 对比剪映 |
|------|------|----------|
| **语义理解** | AI 逐句分析，识别重说/纠正/卡顿 | 只能模式匹配 |
| **静音检测** | >0.3s 自动标记，可调阈值 | 固定阈值 |
| **重复句检测** | 相邻句开头≥5字相同 → 删前保后 | 无此功能 |
| **句内重复** | "好我们接下来好我们接下来做" → 删重复部分 | 无此功能 |
| **词典纠错** | 自定义专业术语词典 | 无此功能 |
| **自更新** | 记住你的偏好，越用越准 | 无此功能 |

## 快速开始

### 安装 Skill

**OpenClaw（推荐）**：

```bash
# 通过 ClawHub 安装（发布后可用）
clawhub install videocut

# 或手动安装到 OpenClaw skills 目录
git clone https://github.com/Ceeon/videocut-skills.git ~/.openclaw/skills/videocut
```

**Claude Code**：

```bash
git clone https://github.com/Ceeon/videocut-skills.git ~/.claude/skills/videocut
```

**Cursor**：

```bash
git clone https://github.com/Ceeon/videocut-skills.git .cursor/skills/videocut
```

### 配置 API Key

**方式一：环境变量（通用）**

```bash
export VOLCENGINE_API_KEY=your_key
```

**方式二：OpenClaw 配置**

编辑 `~/.openclaw/openclaw.json`：

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

**方式三：.env 文件（兼容旧方式）**

```bash
cd ~/.openclaw/skills/videocut  # 或对应安装路径
echo "VOLCENGINE_API_KEY=your_key" > .env
```

### 安装依赖

告诉 Agent：

```
安装环境
```

AI 会自动检查并安装 Node.js、FFmpeg。

## 使用流程

```
┌─────────────────────────────────────────────────────────┐
│  "安装环境"  →  首次使用，安装依赖                       │
└─────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────┐
│  "帮我剪这个口播视频 视频.mp4"                          │
│                                                         │
│  1. 提取音频 → 上传云端                                 │
│  2. 火山引擎转录 → 字级别时间戳                         │
│  3. AI 审核：静音/口误/重复/语气词                      │
│  4. 生成审核网页 → 浏览器打开                           │
└─────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────┐
│  【人工审核 + 执行剪辑】                                │
│                                                         │
│  - 单击跳转播放                                         │
│  - 双击选中/取消                                        │
│  - Shift 拖动多选                                       │
│  - 确认后点击「执行剪辑」→ 自动 FFmpeg 剪辑            │
└─────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────┐
│  "给视频加字幕"                                         │
│                                                         │
│  - 火山引擎转录（带热词）                               │
│  - AI 校对 + 词典纠错                                   │
│  - 人工确认 → 烧录字幕                                  │
└─────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────┐
│  "记录一下刚才的问题"  （可选）                         │
│                                                         │
│  告诉 AI 你的偏好，它会记住：                           │
│  - "静音阈值改成 1 秒"                                  │
│  - "保留适量嗯作为过渡"                                 │
└─────────────────────────────────────────────────────────┘
```

## 目录结构

```
videocut-skills/
├── SKILL.md            # OpenClaw 入口（功能路由）
├── README.md           # 本文件
├── 安装/
│   └── guide.md        # 环境安装流程
├── 剪口播/
│   ├── guide.md        # 转录 + AI 审核 + 剪辑流程
│   ├── scripts/        # 脚本
│   │   ├── volcengine_transcribe.sh
│   │   ├── generate_subtitles.js
│   │   ├── generate_review.js
│   │   ├── review_server.js
│   │   └── cut_video.sh
│   └── 用户习惯/       # 审核规则（可自定义）
│       ├── 1-核心原则.md
│       ├── 2-语气词检测.md
│       ├── 3-静音段处理.md
│       ├── 4-重复句检测.md
│       ├── 5-卡顿词.md
│       ├── 6-句内重复检测.md
│       ├── 7-连续语气词.md
│       ├── 8-重说纠正.md
│       └── 9-残句检测.md
├── 字幕/
│   ├── guide.md        # 字幕生成与烧录流程
│   ├── scripts/
│   │   └── subtitle_server.js
│   └── 词典.txt        # 自定义热词词典
└── 自进化/
    └── guide.md        # 自我学习机制
```

## 依赖

| 依赖 | 用途 | 安装方式 |
|------|------|----------|
| Node.js 18+ | 运行脚本 | `brew install node` |
| FFmpeg | 音视频处理 | `brew install ffmpeg` |
| 火山引擎 API | 语音转录 | [申请 Key](https://console.volcengine.com/) |

## 常见问题

### Q: 火山引擎转录超时？

上传音频到 uguu.se（脚本默认），不要用 catbox.moe（火山引擎访问慢）。

### Q: 审核网页打不开？

检查端口 8899 是否被占用：`lsof -i :8899`

### Q: 剪辑后音画不同步？

使用 `filter_complex + trim` 而非 `concat demuxer`，脚本已处理。

### Q: 如何添加自定义词典？

编辑 `字幕/词典.txt`，每行一个词：
```
Claude Code
MCP
API
```

## License

MIT
