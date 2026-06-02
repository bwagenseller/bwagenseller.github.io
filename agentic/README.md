<img
    src="./images/BrentAndMandi.jpg"
    width="88"
    style="display: block; width: 88px; margin: auto; margin-bottom: 1em"
/><span style="display: block; text-align: center; font-size: 1.75em;"> Agentic AI Tools </span>

# What Are Agentic AI Tools?

Traditional AI tools (like a chatbot) are purely reactive — you ask, they answer. **Agentic AI** tools go further: they can take actions, use tools, write and run code, browse the web, manage files, and chain multiple steps together to complete complex goals with minimal hand-holding.

The spectrum of autonomy varies widely across tools:

| Tool | Type | Autonomy Level |
|---|---|---|
| [Claude Code](/agentic/claude_code/) | Human-in-the-loop coding agent (terminal) | Low — you approve actions |
| [Aider](/agentic/aider/) | Human-in-the-loop coding agent (terminal) | Low — you approve actions |
| [OpenClaw](/agentic/openclaw/) | Multi-agent orchestration framework | High — agents act semi-autonomously |

The right level of autonomy depends on your use case. Claude Code and Aider are great for focused coding sessions where you want control. OpenClaw is for when you want to spin up a team of agents, point them at a goal, and let them run — potentially waking you up at 2 AM when they need a decision.

# General  
* [Concepts](agentic/general/concepts) - Some general concepts behind agentic AI.  
* [API Keys and Tokens](agentic/general/keys_tokens) - How to get API Keys and Tokens, and other identifiers you will need for using agents.  
* [MCP](agentic/general/mcp) - And overview of the Model Context Protocol (MCP).  

# Claude Code
* [Claude Code Overview](/agentic/claude_code/) - Anthropic's official CLI agentic coding tool.
* [Claude Code Basics](/agentic/claude_code/claude_code_basics) - Installation, authentication, and core workflow.  
* [Context Management](/agentic/claude_code/context_management) - Context management in Claude.  
* [Configuration](/agentic/claude_code/configuration) - CLAUDE.md files, settings, and the permissions model.  
- [Channels](/agentic/claude_code/channels) - My notes on channels (i.e. bots) in Claude Code.  
* [Hooks](/agentic/claude_code/hooks) - Automating behavior around tool calls.
* [MCP (Claude)](/agentic/claude_code/mcp) - MCP, specifically for Claude Code.  

# Codex CLI  
- [Overview](/agentic/codex_cli/) - OpenAI's (ChatGPT's) agentic coding tool.  
- [Basics](/agentic/codex_cli/codex_cli_basics) - Installation, authentication, and core workflow.  

# Aider
- [Aider Overview](/agentic/aider/) - Coming soon.

# OpenClaw
- [Overview](/agentic/openclaw/) - The main page for OpenClaw.  
- [Install](/agentic/openclaw/install) - Install and basic setup.  
- [Agents](/agentic/openclaw/agents) - All about agents.  
- [Models](/agentic/openclaw/models) - Switching LLM providers (Grok, Gemini, etc.) and setting defaults.  
- [Bindings](/agentic/openclaw/bindings) - Discussing bindings, which ties agents to communication channels (Slack, Discord, Telegram, and more).  