# MCP   

I cover <font color="purple">MCP</font> [here](agentic/general/mcp) if you want to read more. Anthropic actually developed <font color="purple">MCP</font>, but its now a universal standard.  

That said, there are some Claude Code-specific commands you will need to run for Claude Code.  

# Adding MCP Servers  

To add a MCP server - either one you or someone else previously wrote - the basic command to run, _outside of Claude / on the command line_, is:  
```bash  
claude mcp add [options] <name> <commandOrUrl> [args...]
```  
* `<name>` - your local alias (becomes the mcp__<name>__<tool> prefix).  
* `<commandOrUrl>` - either a command to spawn (for stdio) or a URL to connect to (for http).  
* `[args...]` - extra arguments passed to the spawned command (stdio only).  

Key options:  

| Flag | Values | Purpose | Default |  
| --- | --- | --- | --- |  
| -s, --scope | local, project, user | Where to store the entry | local |  
| -t, --transport | stdio, http,<br>sse (do not use) | Transport type | stdio |  
| -e | KEY=value | Inject env var into the spawned process | - |  
| -H, --header | "Header: value" | Add HTTP header (http transport only)<br>(common for Auth is `--header "Authorization: Bearer $TOKEN"`) | - |  


**<font size="4">Notes</font>**  

* scope  
  * `local`  
    * This means 'just you, just this project'  
    * Stored in `~/.claude.json` under the project directory (as a key in the JSON) -> `mcpServers`; for example, using the example MCP server:  
```
      "/media/agentic_projects/learning": {
        "allowedTools": [],
        "mcpContextUris": [],
        "mcpServers": {
            "random-color": {
              "type": "http",
              "url": "http://192.168.1.100:8765/mcp"
            }
        },
      ...
      }
```
  * `project`  
    * This means 'anyone who clones this repo, including you'  
    * Stored in the [project directory](agentic/claude_code/claude_code_basics?id=launching) in a file called `.mcp.json`; for example, using the [example MCP server](agentic/general/mcp?id=fastmcp-python-example):  
```
{
  "mcpServers": {
    "random-color": {
      "type": "http",
      "url": "http://192.168.1.100:8765/mcp"
    }
  }
}
```  
  * `user`  
    * This means 'just you, every project on this machine'  
    * Stored in `~/.claude.json` at the top level under `mcpServers` for example, Telegram:  
```
  "mcpServers": {
    ...
    "telegram": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "-y",
        "@iqai/mcp-telegram"
      ],
      "env": {
        "TELEGRAM_BOT_TOKEN": "XXXXXYYYYZZZZZ",
        "TELEGRAM_CHAT_ID": "AAABBBBCCC"
      }
    }, 
    ...
  }
```  
* transport  
  * `stdio`  
    * If Claude will run this command as a sub-process, directly getting back information from stdout  
    * careful - this technically runs `npm` directly, so it _is_ getting info back from stdout in that case  
      * `npm` can - and usually does - make remote calls internally, but to Claude, its an internal call.  
  * `http`  
    * Claude directly contacts a remote MCP server  
  * `sse` - deprecated - do not use this  

## MCP Add Examples  

**<font size="4">HTTP server</font>**  
(Based off of [the example MCP server](agentic/general/mcp?id=fastmcp-python-example) - remember to swap out the IP and port for your own)  

`claude mcp add --transport http random-color http://192.168.1.100:8765/mcp`  

**<font size="4">HTTP server, all-projects scope</font>**  
(Based off of [the example MCP server](agentic/general/mcp?id=fastmcp-python-example) - remember to swap out the IP and port for your own)  

`claude mcp add --transport http --scope user random-color http://192.168.1.100:8765/mcp`  

**<font size="4">Stdio server via npx (Gmail/Calendar style)</font>**  

`claude mcp add gmail --scope user -- npx -y @gongrzhe/server-gmail-autoauth-mcp`  

**<font size="4">Stdio server with secrets</font>**  

```
  claude mcp add telegram --scope user \
    -e TELEGRAM_BOT_TOKEN=abc \
    -e TELEGRAM_CHAT_ID=123 \
    -- npx -y @iqai/mcp-telegram
```  

**<font size="4">Stdio server pointing at a local Python script</font>**  

`claude mcp add my-tools --scope local -- python /path/to/python_script.py`  

**<font size="4">Stdio server pointing at a local Python script (Example 2)</font>**  

`claude mcp add maven-builder /home/ai_agent/miniforge3/envs/mcp/bin/python /home/ai_agent/scripts/mcp/mcp_maven.py --scope user`  

**<font size="4">HTTP server requiring auth header</font>**  

`claude mcp add corridor --transport http -H "Authorization: Bearer $TOKEN" https://app.corridor.dev/api/mcp`  

**<font size="4">HTTP server requiring auth header (local MCP)</font>**  

`mcp add --transport http auth-demo http://192.168.1.100:8767/mcp --scope project --header "Authorization: Bearer SOME_TOKEN_HERE"`  


## A Note on the Separator  

The `--` separator. This is shell convention: "stop interpreting flags, everything after is positional." You need it when the command you're spawning has its own flags that would otherwise be eaten by claude mcp add.  

As an example, when adding the [Google Calendar](gentic/claude_code/mcp?id=calendar-google) via `claude mcp add google-calendar --scope user -- npx -y @gongrzhe/server-calendar-autoauth-mcp`:  
* Without `--`, the `-y` would be parsed as a flag for claude mcp add (which doesn't have one) and error out.  
* With `--`, everything after it is the command line for the spawned process: `npx -y @gongrzhe/server-calendar-autoauth-mcp`  

You don't need `--` when there are no flag-looking arguments, but using it consistently is safer.  

---  

# Managing MCP Servers  

`claude mcp` has more than just `add`. The ones you'll actually reach for, in roughly the order you'll need them:  

## claude mcp list  

The first thing to run when an MCP server isn't working. Lists every configured server (across all scopes) with a health check on each one - for HTTP servers it pings the URL; for stdio servers it actually spawns the subprocess and confirms it speaks MCP. The output gives you each server's name, its transport/URL/command, and a Connected / Failed status:  

```bash
claude mcp list
```  

When something's wrong, the status column is your first signal - "Failed to connect" usually means the host is unreachable, the wrong port is configured, or the server simply isn't running.  

> <font color="red">Security note:</font> `claude mcp list` _spawns stdio subprocesses_ to health-check them. If you're in a directory whose `.mcp.json` you don't trust, don't run this - an untrusted `.mcp.json` can specify a command that gets executed the moment you health-check it, which is arbitrary code execution. Same warning applies to `claude mcp get`.  

## claude mcp get <name>  

Returns the full config of a single server, plus the same health check as `list`. Useful when you want to see exactly what's registered (URL or command, args, env vars, scope) for one server without sifting through a list:  

```bash
claude mcp get google-calendar
```  

## claude mcp remove <name>  

Uninstalls a server. By default it removes from whatever scope the server is registered in. If the same server name exists in multiple scopes (rare but possible), pass `--scope local|project|user` to disambiguate:  

```bash
claude mcp remove google-calendar
claude mcp remove google-calendar --scope user
```  

## claude mcp add-json <name> '<json>'  

The paste-the-JSON form. Vendors and docs often publish MCP server configs as a JSON blob ready to drop into `.mcp.json` or `~/.claude.json`. Instead of hand-editing those files (and risking a stray comma that silently breaks Claude Code on next launch), feed the blob in directly:  

```bash
claude mcp add-json telegram --scope user '{
  "type": "stdio",
  "command": "npx",
  "args": ["-y", "@iqai/mcp-telegram"],
  "env": {
    "TELEGRAM_BOT_TOKEN": "...",
    "TELEGRAM_CHAT_ID": "..."
  }
}'
```  

This is the cleanest way to install anything more complex than the basic `claude mcp add` form handles - especially configs with env vars or nested structure. Same `--scope` flag rules as `claude mcp add`.  

## Other Subcommands  

Three you'll rarely need:  

| Command | Purpose |
| --- | --- |
| `claude mcp add-from-claude-desktop` | Imports MCP server configs from Claude Desktop. <font color="red">Mac and WSL only</font> - won't work on plain Linux. |
| `claude mcp reset-project-choices` | Clears your "approve" / "reject" state for project-scoped servers in `.mcp.json` - useful if you accidentally rejected a server and want to be re-prompted on next launch. |
| `claude mcp serve` | Starts Claude Code _itself_ as an MCP server, exposing its tools (Read, Edit, Bash, etc.) to another MCP client. Niche - you'll know if you need this. |

---  

# Debugging MCP Servers  

When an MCP server isn't working, the failure mode is often "Claude just doesn't see the tools" with no obvious error message in the chat. Here's the toolbelt for figuring out why, in roughly the order to apply it.  

## Step 1: claude mcp list  

Already covered above — your fast triage. If a server shows "Failed to connect", that tells you where to look next:  

* <font color="purple">HTTP server, "Failed to connect"</font> — the URL is unreachable. Check the host/port, that the server is actually running, and that nothing is sitting between Claude and the server (firewall, VPN, container network).  
* <font color="purple">stdio server, "Failed to connect"</font> — the spawned subprocess died on launch. Could be a missing executable (`npx`, `python`, `node`), a syntax error in the script, or a permission issue on the file.  

## Step 2: Per-Server Logs  

Claude Code writes per-server JSONL logs to disk. The path:  

```
~/.cache/claude-cli-nodejs/<slugified-project-path>/mcp-logs-<server-name>/<ISO-timestamp>.jsonl
```  

The slugification rule is straightforward: replace `/` with `-` in the project path. For a project at `/media/agentic_projects/learning`, the directory is `~/.cache/claude-cli-nodejs/-media-agentic-projects-learning/`. Each server gets its own subdirectory; each Claude session that touched that server creates a new timestamped log file.  

Each log line is a JSON object — example from a real wizard server startup:  

```json
{"debug":"Initializing HTTP transport to http://192.168.1.100:8765/mcp","timestamp":"2026-05-07T19:05:05.142Z","sessionId":"db00ab08-...","cwd":"/media/agentic_projects/learning"}
```  

Useful one-liners:  

```bash
# Most recent log file for a given server
ls -t ~/.cache/claude-cli-nodejs/-media-agentic-projects-learning/mcp-logs-random-color/ | head -1

# Tail the most recent log, pretty-printed
LOG_DIR=~/.cache/claude-cli-nodejs/-media-agentic-projects-learning/mcp-logs-random-color
tail -f "$LOG_DIR/$(ls -t $LOG_DIR | head -1)" | jq .
```  

These logs capture transport setup, the JSON-RPC handshake, `tools/list` and `resources/list` responses, and any errors raised by the spawned subprocess or HTTP endpoint. <font color="purple">When something's broken, start here.</font>  

## Step 3: --debug Flag  

For real-time visibility while running Claude Code, launch with `--debug` (or `-d`):  

```bash
claude --debug
```  

This streams debug output to stderr as you work. The flag accepts an optional category filter:  

```bash
claude -d mcp              # only MCP-related debug output
claude -d "api,hooks"      # multiple categories, comma-separated
claude -d "!1p,!file"      # exclude categories with !
```  

Or capture the output to a file for after-the-fact review (implicitly enables debug mode):  

```bash
claude --debug-file /tmp/claude-debug.log
```  

> <font color="purple">Note:</font> there's an older `--mcp-debug` flag from earlier Claude Code versions, but it's deprecated. Use `--debug` (or `--debug mcp` for filtered output) on current versions.  

## Common Failure Modes  

| Symptom | Likely cause | Where to look |
| --- | --- | --- |
| "Failed to connect" on an HTTP server | Server not running / wrong port / firewall | Run the server manually; `curl` the `/mcp` endpoint to confirm it responds |
| "Failed to connect" on a stdio server | Wrong command path, missing executable, or args wrong | Run the same `command + args` manually in a shell — same error reproduces |
| Server connects but `tools/list` is empty | Server crashed during init or registered no tools | Per-server log will show the traceback or empty advertisement |
| Env vars missing in the spawned process | Typo in `-e KEY=value` flag, or value contained an unescaped shell character | `claude mcp get <name>` to see exactly what's registered |
| Tools advertised but the model never uses them | Bad or missing description on the tool | Improve the function docstring or `description=` parameter — the model picks based on these |
| Changes to the server code aren't reflected | stdio: subprocess cached for the session. http: server process never restarted. | Restart Claude (for stdio) or restart the server process (for http) |
| Tool schema looks weird (missing args, wrong types) | Bare `dict` or untyped param | Add type annotations — see [Tool Arguments](agentic/general/mcp?id=fastmcp-tool-arguments) |

## Isolating a Problem Server  

If you can't tell whether a problem is with one server or with how multiple servers interact, use `--strict-mcp-config` to load _only_ the server you're testing, ignoring everything in `.mcp.json` and `~/.claude.json`:  

```bash
claude --strict-mcp-config --mcp-config '{"mcpServers":{"random-color":{"type":"http","url":"http://192.168.1.100:8765/mcp"}}}'
```  

This isolates the test so you can confirm whether the issue is the server itself or some interaction between servers. Useful when adding a new server breaks something that was working before.  

---  

# Your Own MCP Server  

This section will detail how you can connect Claude to an MCP server you wrote / run on a machine available to you.  

## MCP Server Setup  

First thing's first: you will have to get your MCP server up and running. I wrote about how to [install fastMCP in Python](agentic/general/mcp?id=your-own-mcp-fastmcp-installation) and then [write a simple MCP server](agentic/general/mcp?id=fastmcp-python-example). I will use this simple script as an example of running a home-grown MCP server, accessible by Claude.  

> Note the hostname/IP and port you set in the script - you will need those in the Claude setup!  

## Connecting: .mcp.json  

!> This is a manual way to add the mcp to your project - really, if you can, you should use [claude mcp add](agentic/claude_code/mcp?id=adding-mcp-servers) (i.e. `claude mcp add --transport http --scope project random-color http://192.168.1.100:8765/mcp`) instead!  

The file `.mcp.json` exists in your [project directory](agentic/claude_code/claude_code_basics?id=launching). It can look something like this:  
```json  
{
  "mcpServers": {
    "random-animal": {
      "type": "stdio",  
      "command": "python3",
      "args": ["/path/to/script/mcp_random_animal.py"]
    }, 
    "random-color": {
      "type": "http",
      "url": "http://192.168.1.100:8765/mcp"
    }
  }
}
```  

Some things:  
* The names under `mcpServers` (i.e. `random-animal`, `random-color`) define the overall service name in Claude.  
* The first entry: local MCP  
  * A local MCP is simply a script that does _not_ run as a server - its spun up by Claude and only lasts until Claude shuts down.  
    * Usually, if I were in this situation, I would just tell Claude to 'run this python script if you need to' and simply have it scrape whatever it prints to stdout - and not use MCP.  
  * Useful if you want to use a tool to perform work and not have Claude burn tokens doing it some other way  
  * If you use [Conda](learn_to_code/python/conda), you will have to put the absolute path to the python command (or, launch Claude in [the right environment](learn_to_code/python/conda?id=activate-conda-env)).
  * Specifically, this is simply a python script that returns a random animal (you can [modify this script](agentic/general/mcp?id=fastmcp-python-example) to replicate this - name the function `random-animal`)  
* The second entry: remote MCP  
  * `"type": "http"` specifies to go to a remote host  
  * `url` contains the host / port your MCP [is running on](agentic/general/mcp?id=fastmcp-python-example).  
* `"random-color"` is what it is named above, and `"random-color"` is the name of the overall server in [the example MCP server](agentic/general/mcp?id=fastmcp-python-example), but they do _not_ have to match  
  * The _should_ match, but they do not have to - whatever is in `.mcp.json` wins.  

---  

# Specific MCPs  

## Chat Applications  

Chat applications are discussed [here](agentic/claude_code/channels?id=slack-direct-mode).  

---  

## Email  

### Gmail  

> `~/.gmail-mcp/` is not a universal MCP standard - it's the specific directory that the `@gongrzhe/server-gmail-autoauth-mcp` package looks for credentials in. Similarly, `~/.calendar-mcp/` is hardcoded in the calendar package. There is no universal convention for where MCP servers store credentials; each package picks its own location.  

Get your [Gmail OAuth Credentials](agentic/general/keys_tokens?id=gmail-also-calendar); then, Register the MCP Server:  

1\. Create the OAuth Keys File  

Create `~/.gmail-mcp/gcp-oauth.keys.json` with your credentials:

```json
{
  "installed": {
    "client_id": "YOUR_CLIENT_ID.apps.googleusercontent.com",
    "client_secret": "YOUR_CLIENT_SECRET",
    "redirect_uris": ["urn:ietf:wg:oauth:2.0:oob", "http://localhost"],
    "auth_uri": "https://accounts.google.com/o/oauth2/auth",
    "token_uri": "https://oauth2.googleapis.com/token"
  }
}
```

2\. Authenticate  

```bash
npx -y @gongrzhe/server-gmail-autoauth-mcp auth
```

This opens a browser OAuth flow. Sign in with your Gmail account and grant access. The token is saved to `~/.gmail-mcp/credentials.json` and reused automatically on future sessions.

3\. Register the MCP Server  

```bash
claude mcp add gmail --scope user -- npx -y @gongrzhe/server-gmail-autoauth-mcp
```

!> The `@gongrzhe/server-gmail-autoauth-mcp` package requests `gmail.modify` scope (read + write), not strictly read-only. In practice, Claude Code's human-in-the-loop approval model means no action happens without your explicit approval - but be aware the permission technically allows writes.  

---  

## Calendar  

### Calendar (Google)  

Get your [Gmail OAuth Credentials](agentic/general/keys_tokens?id=gmail-also-calendar) (if you did this for Gmail, you may already have this); then, Register the MCP Server:  

1\. Copy OAuth Keys  

If you already registered gmail, the same `gcp-oauth.keys.json` can be used. Copy it to the calendar MCP directory:

```bash
mkdir -p ~/.calendar-mcp
cp ~/.gmail-mcp/gcp-oauth.keys.json ~/.calendar-mcp/gcp-oauth.keys.json
```  

If not, [make the gcp-oauth.keys.json file](agentic/claude_code/mcp?id=gmail) and then save to `~/.calendar-mcp/gcp-oauth.keys.json`  

2\. Authenticate  

```bash
npx -y @gongrzhe/server-calendar-autoauth-mcp auth
```  

This opens a browser OAuth flow. Sign in with your Google account and grant access. The token is saved to `~/.calendar-mcp/credentials.json`.  

3\. Register the MCP Server  

```bash
claude mcp add google-calendar --scope user -- npx -y @gongrzhe/server-calendar-autoauth-mcp
```  

> Claude Code also ships with a built-in `claude.ai Google Calendar` MCP server. It can be authenticated with `claude mcp auth "claude.ai Google Calendar"` and requires no Google Cloud setup. However it is Claude Code-specific and cannot be reused by OpenClaw or other MCP clients - use the approach above if you want portability.  

---  


