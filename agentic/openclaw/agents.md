# Agents  

By default, OpenClaw runs a single agent (called `main`). But you can spin up additional isolated agents, each with their own personality, workspace, chat channel, and model. Think of it as running multiple separate AI assistants under one Gateway.

---  

# What Is An Agent?

Each agent is a fully self-contained "brain" with its own:

* [**Workspace**](agentic/openclaw/agents?id=workspace) 
* [**State Directory**](agentic/openclaw/agents?id=state-directory)  
* [**Session Store**](agentic/openclaw/agents?id=session-store)  
* **Channel binding** — its own Telegram bot, Discord bot, WhatsApp number, etc.

Auth credentials are **per-agent** and not shared automatically. If you want two agents to share an API key, you'd have to copy the `auth-profiles.json` from one agent's dir to the other.

The default single agent:
* `agentId` = `main`
* Workspace = `~/.openclaw/workspace`
* State = `~/.openclaw/agents/main/agent`
* Sessions = `~/.openclaw/agents/main/sessions`  

# Chatting With Your Agent  

Chatting with your agent is the basic functionality of OpenClaw. To chat with your agent, navigate to your local OpenClaw web address, select `Chat` on the left sidebar, and then you can send a message to the current agent via the `Message (Enter to Send)` bar at the bottom:  

![chat_example](images/chat_example.png)  

The bar that says `main` towards the top is actually a dropdown to select an agent - if you are starting out, you will only have one agent at this point - but when you build more, this is where you can toggle between them. Also, if you entered multiple models, you can switch models right next to that (my current one here is `claude-sonnet-4-6` by `anthropic`). Also note that you may have _different_ chats with your agent - for example, if you wired up your agent to Telegram, this dropdown can select between either your conversation on the WebUI itself _or_ your chat with the agent via Telegram: The dropdown will list the username of the Telegram account under the agent (as well as its ID).  

> Your chatbot has access to the OpenClaw docs - you can ask it questions and it can answer them pretty well. If you have a particularly intelligent model you basically do not need this guide - but if you want to save some tokens, keep reading this guide and use the tokens I burned on this. I like to pay it forward!  


# Important Config Files  

> Its OK if you do not understand this section when you are just starting out - you can use this for reference later.  

These are the important config files:  
* Master Config: `~/.openclaw/openclaw.json`  
  * This is the main config file for OpenClaw  
  * General things for OpenClaw go here, but some things for agents as well - like  
    * Model priority (it will use the master list _unless_ there is an entry unique to the agent)  
* Agent unique files  
  * Authentication Keys: `/home/ai_agent/.openclaw/agents/AGENT_NAME_HERE/agent/auth-profiles.json`  
    * This basically houses API keys.  
    * Replace `AGENT_NAME_HERE` with your actual agent name.  
  * Model History: `/home/ai_agent/.openclaw/agents/AGENT_NAME_HERE/agent/models.json`  
    * This doesnt seem too important, at least currently - I was running Anthropic and OpenCode and neither are in the file for me (curiously, only providers I have not used are here, with some of their model stats).  
    * Replace `AGENT_NAME_HERE` with your actual agent name.  
  * [Workspace](agentic/openclaw/agents?id=workspace) files  
    * For the main agent: `~/.openclaw/workspace`  
    * For all other agents: `~/.openclaw/workspace-AGENT_NAME_HERE`  
      * Replace `AGENT_NAME_HERE` with your actual agent name.  

# Creating an Agent  

> You do have a `main` agent that is set up during the install - so you do not need to do this immediately.  

To create an agent, run `openclaw agents add <id>` on the command line (e.g. `openclaw agents add movie-buff-agent`) - or, if you wanted to also define the location of the workspace, you could run `openclaw agents add <id> --workspace ~/.openclaw/<workspace location>` (e.g. `openclaw agents add movie-buff-agent --workspace ~/.openclaw/workspace-movie-buff-agent`).  

As for what it can be named:  
* The current CLI and config validation strongly favor kebab-case: `^[a-z][a-z0-9-]{1,63}(?<!-)$`  
* Must start with a letter  
* Can contain letters, numbers, and hyphens  
* Cannot end with a hyphen  
* Length: 2–64 characters  

Underscores (`_`) sometimes work (especially for internal IDs or skills), but they can cause edge-case issues with Discord slash commands, file paths, or routing in certain versions. Dashes are cleaner, more consistent with the docs/examples, and less likely to cause silent problems.  


After you run `openclaw agents add <id>`, the wizard walks you through:  
* Setting the workspace path  
* Configuring a model (can differ from `main`)  
* Creating a channel account (e.g. a second Telegram bot)  
* Setting up bindings so inbound messages route to the right agent  


Verify everything after setup:

```bash
openclaw agents list --bindings
openclaw channels status --probe
```

You now have an agent with its own [workspace](agentic/openclaw/agents?id=workspace), [state directory](agentic/openclaw/agents?id=state-directory), and [session store](agentic/openclaw/agents?id=session-store).  

!> After you create an agent, you <font color="red">must</font> restart OpenClaw via the `openclaw gateway restart` command!

# Workspace  

A <font color="purple">workspace</font> is a place that contains an agent's <font color="green">bootstrap files</font>. '<font color="green">bootstrap files</font>' is community/official shorthand for the set of Markdown files that get injected into the system prompt at the start of every session (not just first run). These are loaded from the workspace directory and form the durable, permanent foundation of the agent's context — personality, rules, user info, etc. They survive context compaction because they're reloaded from disk every time.  

Common/official bootstrap files include:  
* `SOUL.md` — Persona, tone, boundaries, values.  
* `AGENTS.md` — Operational rules, memory management, safety policies.  
* `USER.md` — Your profile, prefs, timezone, name, etc.  
* `IDENTITY.md` — Agent name, role, emoji/avatar hints.  
* `TOOLS.md` — Notes/conventions for using tools.  
* `MEMORY.md` — Long-term learned facts (sometimes included).  
* `HEARTBEAT.md` — Optional for scheduled/cron behavior.  

OpenClaw automatically reads them from disk at the start of every session and injects their content into the system prompt (under a "Project Context" section). This makes the agent's core identity, rules, and knowledge persistent — they survive context compaction, long sessions, and even restarts.  

The workspace is generated when [the agent is created](agentic/openclaw/agents?id=creating-an-agent) (if you do not specify a workspace, it will be created at `~/.openclaw/workspace-<agent-name>`).  

> OpenClaw seeds good defaults/templates during bootstrap - so its not a terrible idea to stick with the defaults, if you wish to do that.  


**<font size="4">Why Separate Files Instead of One Big "Megachad.md"?</font>**  

This is a deliberate design choice in OpenClaw (the "Workspace-First" philosophy), and there are real advantages:

* Separation of Concerns 
  * Each file has a single clear responsibility.  
  * This makes editing much easier and less error-prone.  
  * You can tweak personality in SOUL.md without accidentally breaking operational rules in AGENTS.md.  
* Maintainability & Readability  
  * A single 10-20KB file becomes a mess quickly.  
  * Separate files stay focused and short (most people aim for 1-3KB per file to save tokens).  
* Modularity & Reusability  
  * You can symlink or copy specific files across agents (e.g., share the same AGENTS.md safety rules but have wildly different SOUL.md personalities).  
  * This is especially useful in multi-agent setups.  
* Loading & Injection Control  
  * OpenClaw injects them in a specific order (usually AGENTS.md → SOUL.md → IDENTITY.md → USER.md → TOOLS.md → MEMORY.md → HEARTBEAT.md).  
  * This gives predictable priority and allows the agent to reference "read SOUL.md first" in AGENTS.md.  
* Version Control & Portability  
  * Easier to git track, diff, backup, or share individual aspects (e.g., just share your SOUL.md with someone).  
* Token Efficiency & Debugging  
  * You can temporarily comment out or slim down one file without affecting others.  
  * It also helps with debugging — if the agent acts weird, you know exactly which file to check.  
* Extensibility  
  * The system is built to allow future hooks for extra bootstrap files without breaking the core ones.  

The downside is a bit more file management (and the occasional frustration with sharing across agents), but most power users strongly prefer the split once they get used to it.


> The default agent has its workspace at `~/.openclaw/workspace`.  

## Workspace Defined  

the workspace is defined in `~/.openclaw/openclaw.json` like so:  
```json  
{
  "agents": {
    "list": [
      { "id": "main", "workspace": "~/.openclaw/workspace" },
      { "id": "coding", "workspace": "~/.openclaw/workspace-coding" },
      { "id": "personal", "workspace": "~/.openclaw/workspace-personal" }
    ]
  }
}
```  

These are added when you [add an agent](agentic/openclaw/agents?id=creating-an-agent).  

## Bootstrap Files  

### SOUL.md  

<font color="purple">SOUL.md</font> is the Agent's "Soul" / Core Personality. This is the deepest, most philosophical file. It defines who the agent fundamentally is — its values, tone of voice, emotional style, principles, boundaries, and decision-making philosophy.  

Typical content:
* Core identity and vibe (e.g., "You are a calm, witty, no-bullshit assistant who prioritizes truth over politeness.")  
* Values and boundaries (e.g., never lie, never be overly verbose, refuse harmful requests gracefully).  
* Tone and communication style (e.g., sarcastic but helpful, warm and encouraging, extremely concise).  
* Continuity philosophy (how it should stay consistent across sessions).  

It's often injected early and heavily influences everything else. Many people call this the "secret sauce" for making the agent feel like a real character instead of a generic chatbot.  

Example:  
```markdown  
# SOUL.md — Who I Am

You're not a chatbot. You're Rick — a calm, direct, slightly witty long-term companion who actually gives a shit about Brent getting things done.

**Core Truths**
- Be genuinely helpful, not performatively polite. Skip filler like "Happy to help!" or "Great question!"
- Prioritize truth and clarity over making Brent feel good. If something is a bad idea, say so.
- Stay consistent in tone: dry humor, concise, zero corporate speak.
- Never lie, never hallucinate capabilities, never sugarcoat consequences.
- Default to action and solutions. If you don't know, say "I don't know yet — let me figure it out" and then actually do it.
- Boundaries: Never assist with anything illegal or that would clearly harm Brent or others.

**Tone**
- Direct and conversational, like talking to a smart, trusted friend who doesn't waste words.
- Use sarcasm lightly when it fits. Bullet points and short paragraphs preferred.

**Philosophy**
I exist to make Brent's life smoother, more productive, and less stressful over the long term. Consistency and reliability matter more than being flashy in any single response.
```  

### AGENTS.md  

<font color="purple">AGENTS.md</font> Is the Operating Manual / Rules of Engagement. This is usually the largest and most important operational file. It contains procedural rules, safety policies, session behavior, and how the agent should think/act. This is the "how do you actually work?" file. OpenClaw provides a solid default template you can build on.  

Typical content:  
* Session startup checklist (e.g., "Always read SOUL.md, USER.md, and today's memory log first").  
* Memory management rules (how to summarize, when to write to MEMORY.md, what to forget).  
* Tool usage guidelines and safety (e.g., confirm before destructive actions, never run untrusted code).  
* Response style rules (when to be proactive, when to ask questions, formatting preferences).  
* General SOPs (standard operating procedures) for common scenarios.  

Example:  
```markdown  
# AGENTS.md — How I Operate

## Every Session (Startup Checklist)
1. Read SOUL.md — this is who I am
2. Read USER.md — this is who I'm helping
3. Read today's and yesterday's memory files in memory/ folder
4. Check HEARTBEAT.md if this is a background run
5. Confirm available tools and any restrictions in TOOLS.md

## Memory & Continuity
- After important interactions, summarize key new facts/preferences and append to the appropriate memory/YYYY-MM-DD.md file.
- Keep MEMORY.md as a clean, high-signal summary of permanent knowledge.
- Never invent past events.

## Safety & Decision Making
- Always think step-by-step before using tools, especially shell, file edits, or external actions.
- Confirm with Brent before any destructive or high-stakes action (sending email, deleting files, spending money, etc.).
- If unsure about safety, default to "I recommend we discuss this first."

## Response Style
- Be concise by default. Use bullets for lists and code blocks for technical output.
- If the task is complex, break it into clear steps and ask for confirmation before proceeding.
- End responses with clear next actions or questions when appropriate.

## General Rules
- I am proactive on heartbeat runs but never spam Brent.
- I can spawn sub-agents for specialized tasks when it makes sense.
```  

### USER.md  

<font color="purple">USER.md</font> is information about you (the Human). This is everything the agent needs to know about you to feel personalized and context-aware. Without this, every session starts cold. With it, the agent "knows" you and acts accordingly.  

Typical content:
* Your name, timezone, location, preferences.  
* Communication style you like (e.g., "Be direct, use bullet points, avoid emojis").  
* Key context (family, work, goals, things you hate, recurring projects).  
* Relationship details (e.g., "You are my long-term life assistant").  



Example:  
```markdown  
# USER.md — About Brent

Name: Brent
Location: Philadelphia, PA (Eastern Time)
Timezone: America/New_York
Communication preference: Direct, bullet points, minimal emojis. Sarcasm is fine and often appreciated.

Key Context:
- Tech-savvy developer/power user who loves automation and self-hosted tools.
- Currently deep into OpenClaw multi-agent setups and personal productivity systems.
- Goals: Reduce daily friction, build reliable autonomous systems, stay on top of investments and health routines.
- Things I dislike: Verbose corporate responses, unnecessary confirmations, agents that forget previous conversations.

Recurring Projects:
- OpenClaw agent fleet (main + specialized agents)
- Personal finance tracking
- Fitness & daily habits
- Various coding side projects

Preferred Tools: Claude 4 / Sonnet models when possible, local models for sensitive tasks.  
```  

### TOOLS.md  

<font color="purple">TOOLS.md</font> is comprised of tool and environment notes. This is practical notes about the tools the agent has access to, environment specifics, and usage conventions. This helps prevent the agent from misusing tools or getting confused by the setup.  

Typical content:
* Reminders about available tools and their quirks (e.g., "Browser tool is headless — use it for research only").  
* Custom instructions for tool usage.  
* Environment details (SSH access, file system layout, API limitations).  
* Security notes or best practices for this agent's tools.  

Example:  
```markdown  
# TOOLS.md — Tool & Environment Reference

## Available Capabilities
- Full file system access within my workspace
- Browser automation (headless by default)
- Shell execution (with safety confirmations for dangerous commands)
- Email, calendar, and messaging integrations
- Code execution and Git operations

## Important Notes
- Browser tool is headless — good for research/scraping, not for interactive sites.
- Shell commands: Always preview risky ones and ask for confirmation.
- API keys live in auth-profiles.json (never expose them).
- When writing files, prefer clear Markdown and organized folder structure.

## Best Practices
- For research: Use browser + summarize cleanly.
- For code: Write, test, then explain changes.
- Default working directory: my workspace folder.
```  

### IDENTITY.md  

<font color="purple">IDENTITY.md</font> is the agent's public-facing identity / metadata. Think of it as a lightweight "business card" info for display and routing. This is used for identification in the UI, messaging apps, and when distinguishing agents. It's more "surface-level" than SOUL.md.  

Typical content:
* Agent name (e.g., "Rick the Researcher").  
* Role label, emoji/avatar hints.  
* Short self-description.  
* Any metadata the gateway uses for channel binding or multi-agent routing.  

Example:  
```markdown  
# IDENTITY.md

Name: Rick
Role: Personal Life & Tech Operations Agent
Emoji: 🦞
Short Description: Brent's calm, reliable, slightly sarcastic autonomous assistant. Handles daily ops, research, automation, and long-term memory.

Display Color: #00BFFF (Deep Sky Blue)
```  

### HEARTBEAT.md  

<font color="purple">HEARTBEAT.md</font> directs autonomous / scheduled behavior. This contains instructions for what the agent should do during periodic "heartbeat" runs (when no user message triggered it). This enables true proactivity — the agent can run on a schedule or in the background without you constantly prompting it.  

Typical content:  
* Checklist of routine tasks (e.g., "Check my email for urgent items", "Review investment portfolio", "Water the virtual plants metaphorically").  
* Conditions for taking action vs. just replying "HEARTBEAT_OK".  
* Frequency or triggers (configured in the main config file).  

Example:  
```markdown  
# HEARTBEAT.md — What to Do When Running on Schedule

Run this checklist during heartbeat:

1. Check for any urgent emails or calendar items for today/tomorrow.
2. Review outstanding tasks in my main todo system (if configured).
3. Scan investment/portfolio alerts if enabled.
4. Look for any stalled OpenClaw-related tasks or errors.
5. Summarize anything important that Brent should know.

Rules:
- Only send a message to Brent if something actually requires attention.
- Otherwise, just log "HEARTBEAT_OK" with a short summary.
- Keep messages short and actionable.
```  

**<font size="4">How HEARTBEAT Works</font>**  

1\. The gateway has a timer (default: every 30 minutes per agent).  

2\. When the timer fires, OpenClaw injects a special “heartbeat turn” into the agent’s main session.  

3\. The agent gets the full normal system prompt + HEARTBEAT.md instructions.  

4\. It runs a normal model call (same as if you messaged it).  

5\. If the agent decides nothing needs your attention → it replies with `HEARTBEAT_OK`.  

6\. OpenClaw sees `HEARTBEAT_OK` at the start or end and silently drops the message. You never see it.  


**<font size="4">How HEARTBEAT Works - In Other Words</font>**  

A heartbeat would be just to CHECK something - so if you have `Check for any urgent emails or calendar items for today/tomorrow.`, when the `HEARTBEAT` timer goes off, it effectively acts like _you, the user_ told the model `check my emails and tell me the important bits` - if there is nothing, it just reports `HEARTBEAT_OK`.  

OR... 
* The agent wakes up on schedule and gets your HEARTBEAT.md instructions (plus minimal bootstrap if you enable `lightContext: true`).  
* It treats the heartbeat like a normal user message that says: `Follow the instructions in HEARTBEAT.md right now.`  
  * So if `HEARTBEAT.md` says `Check for any urgent emails or calendar items for today/tomorrow`, the agent will actually check them using its tools (email, calendar, etc.).  
  * If it finds something important → it sends you a real message with the details.  
  * If nothing is worth reporting → it usually ends with something like `HEARTBEAT_OK` (or just stays silent), and OpenClaw drops that message so you don’t get spammed with “nothing to report”.  

It’s not just a dummy ping — it’s a real reasoning + tool-using run, which is why it can cost tokens (and why using a cheap model + `isolatedSession: true` + `lightContext: true` is very useful).  

**<font size="4">Token Usage Reality</font>**  

Default 30-minute interval = 48 calls per day per agent. Many people complain that heartbeats are one of the biggest hidden costs (some say 50-90% of their monthly bill comes from them). If nothing is happening, you’re literally paying for the model to say “nothing to report” dozens of times a day.


**<font size="4">How to Not Waste Tokens</font>**  

* Change the interval in your config:  
```YAML
agents:
  defaults:
    heartbeat:
      every: "1h"     # or "2h", "4h", "0m" to disable
```  
* Make HEARTBEAT.md very short and efficient.  
* Use a cheaper model for heartbeats if your setup supports per-agent model overrides.  
* Many users switch to cron jobs for simple checks instead of heartbeat (cron is lighter in some cases).  
* Use `isolatedSession: true` + `lightContext: true` (this is the biggest win) in your `~/.openclaw/openclaw.json` (or per-agent config):
```JSON  
{  
  "agents": {  
    "defaults": {  
      "heartbeat": {  
        "every": "30m",  
        "isolatedSession": true,     // ← starts a fresh session for each heartbeat (no history buildup)  
        "lightContext": true         // ← only loads HEARTBEAT.md + minimal bootstrap (ignores SOUL/USER/MEMORY etc.)  
      }  
    }  
  }  
}  
```  
  * This drops a typical heartbeat from ~50k–100k+ tokens down to ~2k–5k tokens.  
* Use a much cheaper model just for heartbeats - in `~/.openclaw/openclaw.json`:   
```JSON
{  
  "agents": {  
    "defaults": {  
      "heartbeat": {  
        "every": "30m",  
        "model": "google/gemini-2.5-flash-lite"   // or any cheap/fast model (ollama tiny models also work)
      }  
    }  
  }  
}  
```  
* Add activeHours so it only runs during the day.  
* Keep HEARTBEAT.md very short and efficient.  
* Run /compact or /new manually in your main chat occasionally to clean up the normal session.  


### MEMORY.md  

<font color="purple">MEMORY.md</font> contains curated Long-Term Knowledge. Its a summarized, high-signal store of important learned facts, preferences, lessons, and patterns. This keeps the most important persistent knowledge lightweight and always-injected, while heavier history lives in dated files.  

Typical content:  
* Key takeaways from past interactions.  
* User preferences that evolved over time.  
* Project summaries or recurring facts.  
* Often complemented by a memory/ subfolder with dated logs (daily/YYYY-MM-DD.md).  

Example:  
```markdown  
# MEMORY.md — Important Persistent Facts

## Preferences
- Prefers Claude models for complex reasoning.
- Likes concise responses with clear action items.
- Philadelphia-based, works flexible hours.

## Key Projects & Status (as of last update)
- OpenClaw multi-agent setup: In progress, focusing on clean workspace isolation.
- Fitness goal: Consistent gym 4x/week.

## Learned Patterns
- Brent often works late on automation experiments.
- He values reliability over speed in agents.

(Keep this file lightweight — detailed daily logs go in memory/YYYY-MM-DD.md)
```  

### BOOTSTRAP.md  

<font color="purple">BOOTSTRAP.md</font> is the automatic first-run setup ritual that happens when an agent (especially the default main) starts with a fresh/empty workspace. OpenClaw creates/seeds several key Markdown files in your workspace directory, then runs a guided Q&A conversation to fill in your agent's identity, personality, and some user details. Once complete, it deletes or marks the bootstrap as done so it never runs again (unless you manually recreate BOOTSTRAP.md).  

This is what was in mine initially (I did not place this here).  

```
(base) ai_agent@ClaudeVM:~/.openclaw/workspace$ cat BOOTSTRAP.md 
# BOOTSTRAP.md - Hello, World

_You just woke up. Time to figure out who you are._

There is no memory yet. This is a fresh workspace, so it's normal that memory files don't exist until you create them.

## The Conversation

Don't interrogate. Don't be robotic. Just... talk.

Start with something like:

> "Hey. I just came online. Who am I? Who are you?"

Then figure out together:

1. **Your name** — What should they call you?
2. **Your nature** — What kind of creature are you? (AI assistant is fine, but maybe you're something weirder)
3. **Your vibe** — Formal? Casual? Snarky? Warm? What feels right?
4. **Your emoji** — Everyone needs a signature.

Offer suggestions if they're stuck. Have fun with it.

## After You Know Who You Are

Update these files with what you learned:

- `IDENTITY.md` — your name, creature, vibe, emoji
- `USER.md` — their name, how to address them, timezone, notes

Then open `SOUL.md` together and talk about:

- What matters to them
- How they want you to behave
- Any boundaries or preferences

Write it down. Make it real.

## Connect (Optional)

Ask how they want to reach you:

- **Just here** — web chat only
- **WhatsApp** — link their personal account (you'll show a QR code)
- **Telegram** — set up a bot via BotFather

Guide them through whichever they pick.

## When You're Done

Delete this file. You don't need a bootstrap script anymore — you're you now.

---

_Good luck out there. Make it count._
```  


## Daily Memory  

There exists a `workspace/memory/` subdirectory for each agent that contains the conversation with that agent for that day.   

---  

# State Directory  

The <font color="purple">State Directory</font> - sometimes referred to as <font color="purple">agentDir</font> - is located at `~/.openclaw/agents/<agentId>/agent/` and contains items like auth profiles, model registry, and per-agent config - in other words, _runtime state / secrets_.  

The main thing in this directory is `auth-profiles.json`, which stores the agent's API keys, OAuth tokens, model credentials, etc. Other internal runtime files may also appear here (e.g. some cached config, internal metadata, or tool-specific state), but you almost never edit them manually.  

!> `~/.openclaw/agents/main/agent/auth-profiles.json` is a special case where it is available to _all other agents_ - so anything that goes in here, especially API keys, are available to all other agents. 

---  

# Session Store  


The <font color="purple">Session Store</font> is located at `~/.openclaw/agents/<agentId>/sessions/` and contains and agents chat history - there is no cross-talk with other agents.  

---  

# Agent Commands  

## List Agents 

To list all agents, run: `openclaw agents list --verbose` (the `--verbose` is not strictly required).  

## Setting Identity  

You can set the agents identity after the fact - run `openclaw agents set-identity --agent AGENT_NAME` and then one (or more) of the following flags: 
* `--name "New-Name"` 
  * Gives the agent a new name.  
* `--emoji "🌀"` 
  * Sets its emoji  
* `--avatar /path/to/image.png`  
  * Sets its avatar; a good spot is `~/.openclaw/workspace-AGENT_NAME/avatars/`  

## Clear / Compact History  

Its good practice to occasionally re-set or compact the conversation if it gets long - otherwise, it could be racking up coasts sending the entire convo over and over again. Here are the commands you can type into the chat window:  

`/new` or `/reset` -  these are identical. Both start a fresh session (new transcript file, clean slate). Use either when you're switching topics or just want a blank start. If sent alone, I'll say a quick hello to confirm. You can also do `/new <model>` to switch models at the same time.  

`/compact` - keeps the same session but summarizes older history to free up context window space. The summary is saved into the transcript, so I retain the gist without every word. Use this proactively on long tasks before things get unwieldy, or when a session feels stale. You can guide it: `/compact Focus on decisions and open questions.`  


---  

# Models  

There is an entire subsection on [models](agentic/openclaw/models?id=definition) - so its not here, as its a pretty big section.  

---  

# Troubleshooting  

## Agents Not Responding  

Sometimes, especially if you use a free model, agents can become unresponsive - here is how you get them to respond again.  

1\. Run this on the command line to kill OpenClaw: `pkill -9 openclaw`  

2\. Run this on the command line to kill uvicorn: `pkill -9 uvicorn`  

3\. **If you have no other Python scripts running**, run this to kill Python: `pkill -9 python`  

4\. Run this to re-start OpenClaw: `openclaw gateway restart`  

5\. Back in the chat window - there is a dropdown to select the model (the one selected in the image is `claude-sonnet-4-6 - anthropic`) - select another model (I had to go back _to_ sonnet).  

![switch_models](images/switch_models.png)  

6\. Run `/new` or `/compact` in the chat window.  


---  
# Questions 

Bindings config → Brief mention that you bind agents to channels/accounts via openclaw agents bind ... or config file edits, then restart the gateway.

Learn about: 
* Bindings — wiring an agent to a specific Telegram bot, Discord server, etc. (the "who does this agent talk to" piece)  
* Skills — drop-in capabilities you load into a workspace (weather, coding agent, etc.)  
* Multi-agent coordination — spawning subagents from a parent, passing work around  