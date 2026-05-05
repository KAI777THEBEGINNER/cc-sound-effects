---
name: cc-sound-effects
description: Configure three custom sound effects for Claude Code hooks — task completion, failure/interruption, and user choice prompts.
---

# CC Sound Effects

Configure three meme sound effects as Claude Code audio hooks.

## Sound Effects

| Event | Sound Effect | File |
|-------|-------------|------|
| Task completed | Bad to the Bone | `bad-to-the-bone-meme.aiff` |
| Task failed / interrupted | Surprise Motherfer | `surprise-mother-fer.aiff` |
| User choice / permission prompt | Rizz Sound Effect | `rizz-sound-effect.aiff` |

**Source**: https://www.myinstants.com/en/index/gb/

## Prerequisites

- macOS (uses `afplay` for audio playback)
- `afconvert` available (comes with macOS)
- Claude Code installed

## Installation

### Step 1: Install Sound Files

Copy the three `.aiff` files from this repo's `sounds/` directory to your user's system sounds folder:

```bash
mkdir -p "$HOME/Library/Sounds"
cp sounds/bad-to-the-bone-meme.aiff "$HOME/Library/Sounds/"
cp sounds/surprise-mother-fer.aiff "$HOME/Library/Sounds/"
cp sounds/rizz-sound-effect.aiff "$HOME/Library/Sounds/"
```

> `afplay` supports MP3 natively. You can point directly to an MP3 file without conversion.
> If you prefer AIFF, convert with:
> ```bash
> afconvert input.mp3 "$HOME/Library/Sounds/output.aiff" -f AIFF -d BEI16
> ```

### Step 2: Add Hooks to Claude Code Settings

Open your Claude Code settings file at `~/.claude/settings.json` and add the `hooks` section:

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

> The `sh -c` wrapper prevents extra arguments from breaking `afplay`, which only accepts a single file argument.

### Step 3: Restart Claude Code

Hooks are loaded at startup. Restart Claude Code for the sounds to take effect.

## Hook Events Used

| Hook Event | Trigger | Sound |
|-----------|---------|-------|
| `Stop` | Session ends successfully | Bad to the Bone |
| `StopFailure` | Session ends with error / interrupted | Surprise Motherfer |
| `PermissionRequest` | Claude needs user to choose or confirm | Rizz Sound Effect |

## Customization

To swap in your own sounds:

1. Replace the `.aiff` files in `~/Library/Sounds/`
2. Update the filenames in `~/.claude/settings.json`
3. Restart Claude Code

## Troubleshooting

- **No sound plays**: Verify the `.aiff` files exist at `$HOME/Library/Sounds/` and `afplay` can play them directly.
- **macOS won't write to `/System/Library/Sounds`**: Use `~/Library/Sounds/` instead — it's user-writable and works with `afplay`.
- **Hook config rejected by Claude Code**: Ensure the `hooks` value is an array of objects with a `hooks` field, not a plain object. See the example above.
