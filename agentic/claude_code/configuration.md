# Configuration

# CLAUDE.md  

> See [here](https://code.claude.com/docs/en/memory) for more information.  

The most important configuration mechanism in Claude Code is the `CLAUDE.md` file. Place one in your project root (or any subdirectory) and Claude Code will automatically load it at the start of every session. Use it to:

- Describe the project and its architecture
- Define conventions (naming, formatting, testing approach)
- Specify behaviors to avoid ("never mock the database")
- Document external systems, credentials locations, or mount points

CLAUDE.md files are hierarchical — a `CLAUDE.md` in a subdirectory adds to (not replaces) the root one. There is also a global `~/.claude/CLAUDE.md` for preferences that apply across all projects.  

The `CLAUDE.md` file in the project root is meant to be shared with everyone using that project - this should be checked in with git (if you are using git). That said, you _can also_ use a `CLAUDE.local.md` file, which you can add to `gitignore`. `CLAUDE.local.md` is useful for your _specific and personal_ notes / settings for this project that should not be shared with your teammates.  

## Frontmatter  

<font color="green">Frontmatter</font> is important for markdown files in Claude - basically, its YAML-esque lines before the markdown itself, resting between `---` lines _at the very top of the document_. Example of a markdown file with <font color="green">Frontmatter</font>:  

```markdown
---
name: User profile — Brent
description: Brent's background and how he learns; technically capable, learns Claude Code by poking at artifacts and challenging assistant claims
type: user
originSessionId: ac997a3c-4b95-4623-8847-3f0309c4db6e
---
# First Header  

Some information goes here.  

```

Everything between the `---` lines is considered _data_ - while everything else is considered as the actual content.  

## Sourcing AGENTS.md  

You may have an `AGENTS.md` file, which is like `CLAUDE.md` but is for Codex CLI (dont worry, I wont judge). You can 'source' in this file into `CLAUDE.md` - you can make your `CLAUDE.md` like so:  
```markdown  
@AGENTS.md
```  

---  

# Slash Commands

<font color="green">Slash commands</font> are built-in shortcuts you can type directly in the Claude Code prompt to trigger specific actions or behaviors - without needing to describe what you want in natural language.

They're invoked by typing `/` followed by a command name, for example:

* `/help` - Show available commands and usage tips
* `/clear` - Clear the current conversation context
* `/compact` - Summarize the conversation to free up context space
* `/exit` - End the Claude Code session

## Skills (Custom Slash Commands)  

<font color="green">Skills</font> - otherwise known as <font color="green">Custom Slash commands</font> - are basically a pre-canned prompt that you have perfect and yet you do not want to type again and again; in effect, it's just a Markdown file whose contents become the prompt that gets sent to Claude when you invoke it.  

You can define your own <font color="green">Custom Slash commands</font> by creating Markdown files inside a `.claude/commands/` directory in your project (or globally in `~/.claude/commands/`). Each `.md` file becomes a reusable command - useful for standardizing repeated workflows like running tests, generating boilerplate, or following a specific code review checklist.  

Example: A file at `.claude/commands/review.md` becomes available as `/project:review` inside Claude Code, which contains:  
```markdown
Please review the selected code for readability, potential bugs, and any obvious performance issues. Suggest specific improvements with examples.
```  

...then typing `/project:review` in Claude Code fires that text off as your prompt. It's a saved, reusable prompt with a shortcut name.  

The one thing that elevates them slightly beyond a plain canned prompt is that they support arguments, so you can pass in context at call time via `$ARGUMENTS`; for example:  
```markdown  
Review the following file for security vulnerabilities, focusing on $ARGUMENTS.  
```  
Then you'd call it as `/project:security-review SQL injection` and it slots your argument in.  

> The docs say you can use `$1`, `$2`, `$3`, `...`, but these did not work for me.  

**<font size="4">argument-hints</font>**  

The frontmatter field is only documentation - it shows up in autocomplete to remind you what args the command expects. It doesn't enforce anything. You can declare description and argument-hint in the frontmatter too. Example:  
```
---  
description: Tells Claude to get DOWN!  
argument-hint: "<action1>" 
---
Claude, please $ARGUMENTS on the dance floor!  
```   


## Custom: warmup  

You can create a custom command `/warmup` to address a specific cost in Claude Code's prompt cache: _mid-session tool loading invalidates a large portion of the cached prefix_, and that cost grows with how long you've been working.  


**<font size="4">Background: deferred tools</font>**  

Claude Code keeps the default system prompt small by <font color="purple">deferring</font> less-frequently-used tools. A deferred tool is announced by name in a system reminder at session start, but its full JSONSchema definition is _not_ in the prompt yet. The first time the tool is needed, Claude calls `ToolSearch`, the schema is fetched, and from that point forward the tool is permanently available for the rest of the conversation.  

Deferral is good: it keeps the baseline prefix lean. The tradeoff is paid only when a deferred tool is actually loaded.  

**<font size="4">The cost of mid-session loading</font>**  

Anthropic's prompt cache is <font color="purple">prefix-based</font>: any change in the cached bytes invalidates the cache from that point onward. When `ToolSearch` loads a new schema, the schema gets injected into the system tools list (which sits before the conversation history in the prompt). Everything after the injection point - _including all accumulated conversation history_ - has to be re-written as fresh `cache_creation` tokens at 1.25x the normal input rate.  

In a measured example from a real session:  

| State | cache_read | cache_creation | hit rate |  
|---|---|---|---|  
| Before WebFetch load | 41,294 | 1,442 | 96% |  
| Loading WebFetch | 15,259 | 28,674 | 34% |  
| After load (next turn) | 43,933 | 616 | 98% |  

One tool load -> ~29k tokens re-billed at 1.25x **The longer the conversation when this happens, the more tokens are in scope to re-write.** Loading WebFetch on turn 2 is cheap; loading it on turn 200 of a long debugging session is painful.  

**<font size="4">What /warmup does</font>**  

`/warmup` issues one bundled `ToolSearch` call that loads several commonly-needed tools at once, *while the conversation cache is still small*. This pays the cost early and once, when there's barely any history to re-cache, instead of paying it later and repeatedly as each tool is reached for organically.  

Default tool set:
* `TaskCreate`, `TaskUpdate`, `TaskList` - multi-step task tracking  
* `Monitor` - for tailing long-running background processes (e.g. `mvn compile`)  
* `WebSearch` - quick lookups of error messages and documentation  
* `WebFetch` - pulling specific URLs (e.g. Akka or Java docs)  

After `/warmup` runs, all of these are loaded for the rest of the session. Subsequent uses incur no cache rewrite \u2014 they read from the now-stable prefix at 10% of the normal input rate.  

**<font size="4">When to use it</font>**  

Run `/warmup` as the **first command** of any session that is likely to:  
* Last more than a handful of turns  
* Reference web documentation  
* Use task lists for multi-step work  
* Run long-running background processes  

For short, single-purpose sessions ("what does this regex do?"), skip it - the load cost of unused tools isn't worth it.  

**<font size="4">When _not_ to use it</font>**  

* A one-off question that doesn't need any deferred tools  
* Sessions where you specifically want the smallest possible prefix  
* If you have an `.mcp.json` with `"alwaysLoad": true` set on every server you use - those load eagerly already and are not subject to deferral  

**<font size="4">The Actual File</font>**  

The command is a Markdown file at `~/.claude/commands/warmup.md`:  
```markdown  
---
description: Preload commonly-used deferred tools in one ToolSearch call to avoid mid-session cache flushes
---

Load these tools in a **single** ToolSearch call: TaskCreate, TaskUpdate, TaskList, Monitor, WebSearch, WebFetch.  

Use exactly one ToolSearch invocation with `select:TaskCreate,TaskUpdate,TaskList,Monitor,WebSearch,WebFetch`. Do not invoke the tools themselves - only load their schemas. After the load completes, reply with a one-line confirmation listing the tools that are now available, and nothing else.  
```  



---  


## Claude Code

Here are some additional instructions for Claude Code - use them wisely.  

```  

--- 

# settings.json

Claude Code's runtime behavior is controlled via `settings.json`. There are two locations:

| File | Scope |
|---|---|
| `~/.claude/settings.json` | Global — applies to all projects |
| `.claude/settings.json` | Project-level — checked into the repo |

## Permissions

The permissions block controls which tools Claude Code can run without asking:

```json
{
  "permissions": {
    "allow": [
      "Bash(git *)",
      "Bash(npm run *)",
      "Read(**)"
    ],
    "deny": [
      "Bash(rm -rf *)"
    ]
  }
}
```

Permissions use glob-style matching against tool calls. `allow` rules auto-approve; `deny` rules block outright.

# Environment Variables

| Variable | Description |
|---|---|
| `ANTHROPIC_API_KEY` | API key (alternative to OAuth login); only set if you [did not use OAuth](agentic/claude_code/claude_code_basics?id=api-key-takes-precedence)! |
| `ANTHROPIC_MODEL` | Override the default model |
| `CLAUDE_CODE_MAX_TOKENS` | Override max output tokens |
