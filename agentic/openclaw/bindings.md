# Bindings  

# Definition  

In OpenClaw, bindings (also called channel bindings) are the configuration rules that decide which agent handles incoming messages from a specific messaging channel, account, group, or peer. They act like a smart routing table or switchboard: when a message arrives (from WhatsApp, Telegram, Slack, Discord, etc.), OpenClaw looks at the bindings to figure out which agent should process it. This is crucial for multi-agent setups, where you might run several specialized agents on the same gateway (e.g., one for personal tasks, one for work/support, one cheap/fast model agent, etc.).


# Why Bindings Exist  

* OpenClaw's Gateway receives normalized messages from all connected channels.  
  * Without bindings, everything might default to a single "main" agent.  
  * With bindings, you get fine-grained control: route a specific Telegram group to a "support" agent, your personal WhatsApp DMs to your main agent, a Slack workspace to another, etc.  
* Routing is mostly deterministic (not chosen by the LLM) — it happens before the agent even sees the message.  
* Replies automatically go back to the original channel (no need for the model to decide "where" to send).  

---  

# Binding: Slack  

This details how you can bind slack to your main agent.  

1\. Get / set up a [Slack bot token](agentic/general/keys_tokens?id=slack-bot-token), and then get / set up a [Slack app token](agentic/general/keys_tokens?id=slack-app-token).  

2\. Add the channel via the CLI: `openclaw channels add slack --token "xoxb-your-bot-token-here" --app-token "xapp-your-app-token-here"`  

3\. Actually bind the main agent to the default slack entry: `openclaw agents bind --agent main --bind slack:default`  

4\. Ensure everyone in Slack can send messages to the bot: `openclaw config set channels.slack.allowFrom '["*"]'`  

5\. (<font color="purple">Optional</font>) Add permissions for the bot to respond in a certain channel (once you [get the channel ID](agentic/general/keys_tokens?id=slack-channel-id)): `openclaw config set channels.slack.channels.'C01234567N9' '{"allow": true, "requireMention": true}'`  
* This assumes `C01234567N9` is the channel ID in Slack - obviously it will be different.  

6\. Re-load the config: `openclaw gateway config-reload`  

7\. Verify: `openclaw channels status`  

# CLI Commands  

## Add Channel  

> This is the first command you will issue when adding a new channel.  

To add a channel: `openclaw channels add PLATFORM_HERE --token "BOT_TOKEN_HERE" --app-token "APP_TOKEN_HERE`  
* `PLATFORM_HERE` will be the chat application platform (`slack`, `discord`, `telegram`, `signal`, etc).  
* `BOT_TOKEN_HERE` is your bot token.  
* `APP_TOKEN_HERE` is your app token (note - Slack needs this, Telegram doesn't - so this flag isnt always required).  


These channel commands configure and manage Slack integration for OpenClaw agents. Run from a terminal on your host machine.  

## Bind an Agent

To bind an agent: `openclaw agents bind --agent AGENT_NAME --bind PLATFORM_HERE:ACCOUNT_ID`  
* `AGENT_NAME` is the agent's name.  
* `PLATFORM_HERE` will be the chat application platform (`slack`, `discord`, `telegram`, `signal`, etc).  
* `ACCOUNT_ID` is the accountID (usually `default`)  

Two agents cannot be bound to the same source. Routing picks one agent per inbound message, using a priority chain (peer -> guild -> team -> account -> channel -> default). The first match wins. You can't have two agents both "listening" to the same channel in normal routing.

The exception is broadcast groups, but that is intentional.  

## Unbind an Agent  

To unbind an agent: `openclaw agents unbind --agent AGENT_NAME --bind PLATFORM_HERE:ACCOUNT_ID`
* `PLATFORM_HERE` will be the chat application platform (`slack`, `discord`, `telegram`, `signal`, etc).  
* `ACCOUNT_ID` is the accountID (usually `default`)  

You can bind multiple channels in one command: `openclaw agents bind --agent AGENT_NAME --bind PLATFORM_1_HERE:ACCOUNT_1_ID --bind PLATFORM_2_HERE:ACCOUNT_2_ID`  

## Set Parameters  

The command `openclaw config set ...` will set the relevant JSON section from `openclaw.json`. It basically follows the JSON structure in dot notation.  

Some examples:  
* `openclaw config set channels.slack.allowFrom '["*"]'` - Allow messages from *all* users/workspaces in a platform (bypasses user restrictions).  
  * Setting a specific user: `openclaw config set channels.discord.allowFrom '["123456789"]'`  
* `openclaw config set channels.slack.channels.'C01234567N9' '{"allow": true, "requireMention": true}'` - Allows people to mention the bot - and get a response - in a Slack channel.  


## Get Bindings  

To see the binding you _manually ran_: `openclaw config get bindings`  

Or, in machine-readable format: `openclaw agents bindings --json`  

For a specific agent: `openclaw agents bindings --agent main`  
* `AGENT_NAME` is the agent's name  

!> This will not show certain bindings - for example, if it routes to `main` by default it will not show here.  

## Get Parameters  

The command `openclaw config get ...` will return the relevant JSON section from `openclaw.json`. It basically follows the JSON structure in dot notation - for example, to see all channels you would type: `openclaw config get channels` will give you the `channels` object in the `openclaw.json` JSON file; `openclaw config get channels.slack` will give you just the slack portion, etc etc.  

The relevant two for bindings:  
* `openclaw config get bindings`  
* `openclaw config get channels`  

And you can traverse the JSON structure from there.


```
openclaw pairing list slack
openclaw channels status
openclaw logs --follow
openclaw channels status --probe

# Schema


BRENT THIS IS FROM YOU OBFUSCATED  
```
  "channels": {
    "telegram": {
      "enabled": true,
      "dmPolicy": "pairing",
      "botToken": "1234567890:abcdefghijklmnopqrstu-vwxyzab-cdefg",
      "groupPolicy": "allowlist",
      "streaming": "partial"
    },
    "slack": {
      "mode": "socket",
      "enabled": true,
      "groupPolicy": "allowlist",
      "dmPolicy": "open",
      "allowFrom": ["*"],
      "streaming": "partial",
      "nativeStreaming": true,
      "accounts": {
        "default": {
          "botToken": "xoxb-1111111111111-22222222222222-abcdefghijklmnopqrstuvwx",
          "appToken": "xapp-1-aaaaaaaaaaa-bbbbbbbbbbbbbb-cccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccc",
          "userTokenReadOnly": true,
          "channels": {
            "C01234567N9": { "allow": true, "requireMention": true },
            "C012345ABCD": { "allow": true, "requireMention": true }
          }
        }
      }
    }
  },
```  

BUT you CAN have multiple entries




```json
...
  "channels": {
    "telegram": {
      "enabled": true,
      "dmPolicy": "pairing",
      "botToken": "1234567890:AAAAAABBBBBBCCCCCCDDDDDD",
      "groupPolicy": "allowlist",
      "streaming": "partial"
    }
  },
...
```
* This is the channel config   

```JSON
...
  "bindings": [
    { 
      "agentId": "home", 
      "match": { 
        "channel": "telegram", 
        "accountId": "default" 
      } 
    },
    {
      "type": "acp",
      "agentId": "codex",
      "match": {
        "channel": "telegram",
        "peer": { 
          "kind": "group", 
          "id": "-1001234567890:topic:42" 
        }
      }
    }
  ]
...
```
* This is the bindings    

# Types

## ACP Bindings (`type: "acp"`)

Route to an ACP (Agent Coding Platform) harness session:

```json
  "bindings": [
    {
      "type": "acp",
      "agentId": "codex",  // ACP agent (codex, claude-code, pi)
      "match": {
        "channel": "telegram",
        "peer": {
          "kind": "group",
          "id": "-1001234567890:topic:3"  // code review topic
        }
      }
    }
  ]
```

## Agent Bindings (`type: "agent"`)

Route to a persistent agent:

```json
  "bindings": [
    {
      "type": "agent",
      "agentId": "maintenance",
      "match": {
        "channel": "telegram",
        "peer": {
          "kind": "direct",
          "id": "6687590589"  // Brent's Telegram ID
        }
      }
    }
  ]
```

# Matching Precedence

1. **Exact peer match** (direct, group, topic) — highest priority
2. **Account match** 
3. **Channel match** (fallback)
4. **Default agent** (no binding match)

# Examples

## Telegram Forum Topic → Codex

```json
  "bindings": [
    {
      "type": "acp",
      "agentId": "codex",
      "match": {
        "channel": "telegram",
        "peer": {
          "kind": "group",
          "id": "-1001234567890:topic:42"
        }
      }
    }
  ]
```

## Brent's DMs → Main Agent

```json
  "bindings": [
    {
      "type": "agent",
      "agentId": "main",
      "match": {
        "channel": "telegram",
        "accountId": "default",
        "peer": {
          "kind": "direct",
          "id": "6687590589"
        }
      }
    }
  ]
```

## Discord Thread → Claude Code

```json
  "bindings": [
    {
      "type": "acp",
      "agentId": "claude-code",
      "match": {
        "channel": "discord",
        "peer": {
          "kind": "thread",
          "id": "1234567890"
        }
      }
    }
  ]
```

# Debugging

```bash
openclaw bindings list      # list active bindings
openclaw bindings test      # test against a message
openclaw logs --follow      # watch routing decisions
```

# Common Patterns

- **Topic per agent**: One Telegram forum topic per coding agent
- **User-specific routing**: Route Brent's DMs to "main", son's to "kid-friendly"
- **Group → specialist**: Route Plex group to "plex-agent"

Bindings make OpenClaw multi-agent routing deterministic and config-driven.