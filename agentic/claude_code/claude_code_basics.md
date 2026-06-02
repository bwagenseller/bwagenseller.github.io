# Claude Code Basics

# What is it?  

<font color="green">Claude Code</font> is an [AI Agent](agentic/general/concepts?id=ai-agent) developed by Anthropic. It runs in a terminal('CLI'), and can perform many types of tasks from investigating issues to writing code.  

> While <font color="green">Claude Code</font> - or any other agent - is helpful, they _can and do_ make mistakes. This can be mitigated by a well-designed [CLAUDE.md](agentic/claude_code/configuration?id=claudemd) file, but even then - its not a God.  

# Installation

> Both methods produce the same `claude` CLI — the install script is just a wrapper around the npm install.  

## Method 1: Install Script  

> This is recommended by Anthropic.  

Anthropic provides a convenience install script that handles Node.js version checking, PATH setup, and the npm install for you:

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

## Method 2: npm

If you prefer to install manually (requires Node.js 18+):

```bash
npm install -g @anthropic-ai/claude-code
```

# Authentication  

Claude Code supports two authentication methods — and the one you choose has a direct impact on cost.

## Method 1: OAuth Login  

> This is recommended by Anthropic; it uses your subscription, which is what you want.  

On first run, Claude Code will walk you through a browser-based OAuth flow tied to your Anthropic account:

```bash
claude
```

If you have a <font color="green">Pro</font> or <font color="green">Max</font> subscription, usage is included in your monthly plan at no extra charge. This is the preferred method for most users.

## Method 2: API Key (Pay-per-token)

You can instead authenticate with an Anthropic API key:

```bash
export ANTHROPIC_API_KEY="sk-ant-..."
```

This bills per token consumed and is **completely separate** from your subscription — meaning you can be paying for a Pro subscription and still rack up API charges on top of it if you use this method. Only use an API key if you have a specific reason to (e.g., automating Claude Code in a CI pipeline with no interactive login available).

## Verify Which Method You're Using

If `ANTHROPIC_API_KEY` is unset, you're on OAuth/subscription:

```bash
echo $ANTHROPIC_API_KEY   # empty = OAuth, populated = API key billing
```

## API Key Takes Precedence  

!> <font color="red">Watch Out</font>! The API Key takes precedence over OAuth!  

If `ANTHROPIC_API_KEY` is exported globally in your `.bashrc` or `.zshrc` (e.g., for use with Aider or OpenClaw), Claude Code will use it and bill against your API credits — even if you have an active OAuth login.

> **Do not export `ANTHROPIC_API_KEY` globally.** Instead, scope it only to the tools that need it.

Set it inline per command:

```bash
ANTHROPIC_API_KEY="sk-ant-..." aider
```

Or use shell aliases:

```bash
alias aider='ANTHROPIC_API_KEY="sk-ant-..." aider'
alias openclaw='ANTHROPIC_API_KEY="sk-ant-..." openclaw'
```

This way Claude Code launched normally will never see the key and will always fall back to your OAuth subscription.

---  

# Launching

!> Claude is centered around 'projects'. What defines a project: the directory from which you launch `claude`. Everything is centered around the project, and everything about that project is stored in `~/.claude/projects/THE_DIRECTORY_OF_THE_PROJECT_WITH_DASHES_INSTEAD_OF_SLASHES`.  

Start an interactive session in any project directory:

```bash
cd /your/project
claude
```

Claude Code will read the directory, load any `CLAUDE.md` instructions, and drop you into an interactive prompt.

# Core Workflow

The basic loop is:
1. You describe a task in natural language
2. Claude Code proposes a plan and/or takes actions (file reads, edits, shell commands)
3. You approve, deny, or redirect
4. Repeat

## Common Commands

| Command | Description |
|---|---|
| `claude` | Start interactive session |
| `claude "do something"` | Run a one-shot prompt and exit |
| `claude --continue` | Resume the last conversation |
| `claude --resume` | Allows you to pick a previous session and start where you left off. |
| `/help` | Show available slash commands inside a session |
| `/clear` | Clear conversation history |
| `/compact` | Summarize history to save context |

# Permission Modes

Claude Code has three permission levels you can toggle during a session:

- **Default** — approves safe reads automatically; prompts for writes, shell commands, git ops
- **Auto-approve** — approves everything automatically (use carefully)
- **Manual** — prompts for every action

You can also set per-tool permissions permanently in `settings.json` (see [Configuration](/agentic/claude_code/configuration)).
