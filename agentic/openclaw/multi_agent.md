# Multi-Agent  

# Channel Bindings

A **binding** is what connects an inbound message to a specific agent. Without bindings, everything goes to `main`.

Example use cases:
* **Telegram Rick** (main) handles your personal Telegram bot
* A second agent on a different Telegram bot handles, say, family queries
* One WhatsApp number, different contacts routed to different agents by sender phone number

For separate Telegram bots per agent: create a new bot via BotFather, get its token, and the wizard will wire it up.

For routing different DMs on the *same* WhatsApp number to different agents, you can match on sender phone number (`peer.kind: "direct"`).

---

# Multiple Agents = Multiple Personalities

Each agent has its own workspace, so it has its own `SOUL.md`, `AGENTS.md`, `USER.md` — completely separate identity and behavior. Useful if:

* You want a "work mode" agent with different personality/tools than your personal one
* Multiple family members sharing one Gateway but each getting their own isolated assistant
* A purpose-built agent for a specific task (coding assistant, media manager, etc.)

---

---

# Quick Reference

| Task | Command |
|---|---|
| List agents + bindings | `openclaw agents list --bindings` |
| Check channel status | `openclaw channels status --probe` |

