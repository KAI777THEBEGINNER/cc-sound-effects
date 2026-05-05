# CC Sound Effects
English | [中文](./README.md)

Add personality to your Claude Code sessions with custom audio hooks. This project configures three meme sound effects that play at key interaction moments — no more silent terminals when Claude needs your attention.

## What It Does

| Event | Sound Effect | When It Plays |
|-------|-------------|---------------|
| Task Completed | Bad to the Bone | Claude finishes successfully |
| Task Failed / Interrupted | Surprise Motherfer | Something goes wrong or gets cut off |
| User Choice Needed | Rizz Sound Effect | Claude asks you to pick or confirm |

All sounds are played via macOS `afplay` through Claude Code's native hook system. Nothing runs in the background — the audio fires once, instantly, at the exact moment the event triggers.

## Demo

1. Start a Claude Code session, ask it to do something, then wait for it to finish — you will hear the completion sound.
2. Hit `Ctrl+C` mid-stream — the interruption sound fires.
3. Trigger any permission prompt (e.g., ask Claude to run a Bash command) — the prompt sound fires.

## Prerequisites

- macOS (uses built-in `afplay`)
- Claude Code installed and configured
- A sense of humor

## Quick Start

```bash
# 1. Clone this repo
git clone https://github.com/KAI777THEBEGINNER/cc-sound-effects.git
cd cc-sound-effects

# 2. Install sound files to your user library
mkdir -p "$HOME/Library/Sounds"
cp sounds/*.aiff "$HOME/Library/Sounds/"

# 3. Open your Claude Code settings
open ~/.claude/settings.json

# 4. Paste the hooks block from the section below

# 5. Restart Claude Code — done.
```

## Hook Configuration

Add this block to `~/.claude/settings.json` (merge it with any existing settings):

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

> Why `sh -c`? Claude Code hooks append extra arguments when calling the command. `afplay` rejects anything beyond a single file path, so the `sh -c` wrapper swallows the noise and only passes your sound file to the player.

## File Paths

This setup intentionally uses `$HOME/Library/Sounds/` instead of `/System/Library/Sounds/`:

- `/System/Library/Sounds/` is protected by macOS SIP (System Integrity Protection). Even `sudo` cannot write there without disabling SIP, which is not recommended.
- `~/Library/Sounds/` is user-writable, works identically with `afplay`, and survives macOS updates.

## Using Your Own Sounds

`afplay` supports MP3 natively — no conversion needed. Just point the config to your MP3 file. See `settings.example.json`:

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

If you prefer AIFF, you can still convert with `afconvert`:

```bash
afconvert input.mp3 "$HOME/Library/Sounds/my-sound.aiff" -f AIFF -d BEI16
```

Update `~/.claude/settings.json` and restart Claude Code.

## Sound Sources

All three sound effects are sourced from:

- https://www.myinstants.com/en/index/gb/

Individual pages:
- [Bad to the Bone](https://www.myinstants.com/en/instant/bad-to-the-bone-meme-22189/)
- [Surprise Motherfer](https://www.myinstants.com/en/instant/surprise-mother-fer-69882/)
- [Rizz Sound Effect](https://www.myinstants.com/en/instant/rizz-sound-effect-54189/)

## Troubleshooting

**No sound plays**
- Verify the `.aiff` files exist at `$HOME/Library/Sounds/`
- Test manually: `afplay "$HOME/Library/Sounds/bad-to-the-bone-meme.aiff"`
- Check that `~/.claude/settings.json` is valid JSON (trailing commas break it)

**Hook config rejected by Claude Code**
- Ensure the `hooks` value is an array of objects with a nested `hooks` array. The schema is strict — a plain object will fail validation.

**Sounds too loud or too quiet**
- `afplay` supports `-v` for volume: `afplay -v 0.5 file.aiff`. Add the flag inside the `sh -c` string.

## Customization Ideas

- Replace the meme sounds with subtle chimes for a professional environment
- Add `Elicitation` hooks for when Claude asks clarifying questions
- Add `TaskCompleted` / `TaskCreated` hooks if you use the task system heavily
- Use different sounds for different projects by placing project-level `.claude/settings.json` hooks

## License

This repository is a configuration template. The sound effects are memes sourced from MyInstants and remain the property of their original creators. Use them under the terms provided by the respective source platform.
