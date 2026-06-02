<img
    src="./images/BrentAndMandi.jpg"
    width="88"
    style="display: block; width: 88px; margin: auto; margin-bottom: 1em"
/><span style="display: block; text-align: center; font-size: 1.75em;"> Claude Code </span>


# Claude Code
- [Claude Code Overview](/agentic/claude_code/) - This page.
- [Claude Code Basics](/agentic/claude_code/claude_code_basics) - Installation, authentication, and core workflow.  
- [Context Management](/agentic/claude_code/context_management) - Context management in Claude.  
- [Configuration](/agentic/claude_code/configuration) - CLAUDE.md files, settings, and the permissions model.  
- [Channels](/agentic/claude_code/channels) - My notes on channels (i.e. bots) in Claude Code.  
- [Hooks](/agentic/claude_code/hooks) - Automating behavior around tool calls.
- [MCP Servers](/agentic/claude_code/mcp) - Extending Claude Code with external tool servers.

# What Is Claude Code?

Claude Code is Anthropic's official CLI-based agentic coding tool. Unlike a chat interface, Claude Code runs in your terminal and has direct access to your filesystem, shell, and git — it can read files, write code, run tests, make commits, and open PRs autonomously, with you approving actions along the way.

It uses a **human-in-the-loop** model: by default, risky or destructive actions (file writes, shell commands, git operations) are presented for your approval before execution. You can tune the permission level up or down based on how much you trust the current task.

Key differentiators from a plain LLM chat:
- Reads your actual codebase rather than relying on pasted snippets
- Executes shell commands, runs tests, and iterates on results
- Persists project instructions via `CLAUDE.md` files
- Extensible via MCP (Model Context Protocol) servers
- Supports hooks to automate behavior before/after tool calls
