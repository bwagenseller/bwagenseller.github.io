# Hooks

Hooks let you run shell commands automatically in response to Claude Code events — before or after tool calls, on session start/stop, etc. They are configured in `settings.json`.

## Hook Events

| Event | Fires |
|---|---|
| `PreToolUse` | Before any tool call executes |
| `PostToolUse` | After any tool call completes |
| `Stop` | When Claude Code finishes a response |
| `Notification` | When Claude Code sends a notification |

## Configuration

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Shell command completed' >> ~/claude_log.txt"
          }
        ]
      }
    ]
  }
}
```

The `matcher` field is a string matched against the tool name (`Bash`, `Edit`, `Write`, `Read`, etc.).

## Hook Output

Hooks can influence Claude Code's behavior by writing to stdout:

- A hook that exits non-zero will **block** the tool call (PreToolUse only)
- A hook that prints JSON with `{"decision": "block", "reason": "..."}` will block and show the reason
- Normal stdout output is passed back to Claude as context

## Example: Auto-run Tests After Edits

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit",
        "hooks": [
          {
            "type": "command",
            "command": "cd $PROJECT_ROOT && npm test --passWithNoTests 2>&1 | tail -5"
          }
        ]
      }
    ]
  }
}
```
