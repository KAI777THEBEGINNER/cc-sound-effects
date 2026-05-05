# CC Sound Effects
中文｜[English](./README.en.md) 

为你的 Claude Code 会话注入个性，使用自定义音频钩子。本项目配置了三款 meme 音效，在关键交互节点自动播放——Claude 需要你的注意时，终端不再沉默。

## 功能

| 事件 | 音效 | 触发时机 |
|------|------|---------|
| 任务完成 | Bad to the Bone | Claude 正常结束 |
| 任务失败 / 中断 | Surprise Motherfer | 出错或被中断 |
| 需要用户选择 | Rizz Sound Effect | Claude 询问确认或选择 |

所有音效通过 macOS 内置的 `afplay` 配合 Claude Code 原生钩子系统播放。无需后台进程，事件触发瞬间即响。

## 演示

1. 开启 Claude Code 会话，下达任务，等待完成——你将听到完成音效。
2. 任务执行中按下 `Ctrl+C`——中断音效响起。
3. 触发任何权限确认（例如让 Claude 执行 Bash 命令）——提示音效响起。

## 前提条件

- macOS（使用系统自带的 `afplay`）
- 已安装并配置 Claude Code
- 有点幽默感

## 快速开始

```bash
# 1. 克隆仓库
git clone https://github.com/KAI777THEBEGINNER/cc-sound-effects.git
cd cc-sound-effects

# 2. 安装音效文件到用户库
mkdir -p "$HOME/Library/Sounds"
cp sounds/*.aiff "$HOME/Library/Sounds/"

# 3. 打开 Claude Code 配置
open ~/.claude/settings.json

# 4. 粘贴下方 Hooks 配置

# 5. 重启 Claude Code —— 完成。
```

## Hooks 配置

将以下内容添加到 `~/.claude/settings.json`（与已有设置合并）：

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "sh -c 'afplay \"$HOME/Library/Sounds/bad-to-the-bone-meme.aiff\"'"
          }
        ]
      }
    ],
    "StopFailure": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "sh -c 'afplay \"$HOME/Library/Sounds/surprise-mother-fer.aiff\"'"
          }
        ]
      }
    ],
    "PermissionRequest": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "sh -c 'afplay \"$HOME/Library/Sounds/rizz-sound-effect.aiff\"'"
          }
        ]
      }
    ]
  }
}
```

> 为什么要用 `sh -c`？Claude Code 的 hooks 会在调用命令时附加额外参数，而 `afplay` 只能接收单个文件路径。`sh -c` 包裹可以吞掉多余参数，只把音效文件传给播放器。

## 文件路径说明

本方案刻意使用 `$HOME/Library/Sounds/` 而非 `/System/Library/Sounds/`：

- `/System/Library/Sounds/` 受 macOS SIP（系统完整性保护）限制，即使 `sudo` 也无法写入，不建议关闭 SIP。
- `~/Library/Sounds/` 用户可写，与 `afplay` 完全兼容，且 macOS 升级后不受影响。

## 使用你自己的音效

`afplay` 原生支持 MP3，无需转换。直接把 MP3 路径写进配置即可。参考 `settings.example.json`：

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "sh -c 'afplay -v 0.6 \"$HOME/Downloads/bruh.mp3\"'"
          }
        ]
      }
    ]
  }
}
```

如果你偏好 AIFF 格式，也可以用 `afconvert` 转换：

```bash
afconvert input.mp3 "$HOME/Library/Sounds/my-sound.aiff" -f AIFF -d BEI16
```

修改完 `~/.claude/settings.json` 后，重启 Claude Code 生效。

## 音效来源

三款音效均来自：

- https://www.myinstants.com/en/index/gb/

具体页面：
- [Bad to the Bone](https://www.myinstants.com/en/instant/bad-to-the-bone-meme-22189/)
- [Surprise Motherfer](https://www.myinstants.com/en/instant/surprise-mother-fer-69882/)
- [Rizz Sound Effect](https://www.myinstants.com/en/instant/rizz-sound-effect-54189/)

## 故障排查

**没有声音**
- 确认 `.aiff` 文件存在于 `$HOME/Library/Sounds/`
- 手动测试：`afplay "$HOME/Library/Sounds/bad-to-the-bone-meme.aiff"`
- 检查 `~/.claude/settings.json` 是否为合法 JSON（末尾逗号会导致解析失败）

**Hook 配置被 Claude Code 拒绝**
- 确保 `hooks` 的值是一个对象数组，每个对象内部包含 `hooks` 数组。结构必须严格符合 schema，普通对象无法通过验证。

**音量过大或过小**
- `afplay` 支持 `-v` 调节音量：`afplay -v 0.5 file.aiff`。将音量参数加入 `sh -c` 字符串内部即可。

## 自定义想法

- 在正式工作环境中把 meme 音效换成低调的提示音
- 为 Claude 的追问（`Elicitation`）增加钩子
- 如果你频繁使用 task 系统，可为 `TaskCompleted` / `TaskCreated` 增加音效
- 在不同项目中使用不同音效，只需在项目级别的 `.claude/settings.json` 中配置

## 许可证

本仓库仅为配置模板。音效为从 MyInstants 获取的网络 meme，版权归原始创作者所有，使用请遵守相应平台条款。
