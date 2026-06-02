# Channels  

Starting with Claude Code v2.1.80, you can use the agentic concept of a <font color="purple">channel</font>: basically, Claude Code connects to a chat application (currently Telegram and Discord as of March 2026, but hopefully Slack and WhatsApp soon) and you can interact with your Claude Code implementation this way. In this context, Claude Code (or anything that acts via a chat application) is considered a <font color="green">bot</font>.  

!> In order for the bot to be available in your chat application, Claude Code _must_ be running; this means you will have to leave it running...  

# Install  

!> Do not install as root! Install as a normal user.  

You will need to make sure your Claude Code version is at least v2.1.80; if Claude Code is not installed, [you can install it](agentic/claude_code/claude_code_basics?id=installation); if you simply need to upgrade, simply run `claude update`.  

Now you need to install <font color="purple">Bun</font>; to install <font color="purple">Bun</font>: `curl -fsSL https://bun.sh/install | bash`  

> Remember to source `.bashrc` with `source ~/.bashrc` after the <font color="purple">Bun</font> install!  

To check to see if <font color="purple">Bun</font> installed properly, check the version with `bun --version`  


---  

# Setup - General Flow  

> Once Claude Code (and Bun) is installed, you can begin hooking up channels to a running instance of Claude Code.  

There seems to be a _general_ flow of how to setup a channel - Discord and Telegram in particular. I will lay out the general process.  

1\. Start Claude Code with the channels flag: `claude --channels plugin:CHANNEL_HERE@claude-plugins-official`  
* Replace `CHANNEL_HERE` with the channel (`discord`, `telegram`, etc)  
* You _must_ use the `--channels` flag _every_ time you want to connect to Telegram from Claude!  
* You CAN run the same bot across multiple channels - for example, to have the same agent monitor / respond to Telegram _and_ Discord, just list both channels: `claude --channels plugin:telegram@claude-plugins-official plugin:discord@claude-plugins-official`  

2\. Once inside Claude Code, install the Telegram plugin:  
* Run (in Claude terminal): `/plugin marketplace add anthropics/claude-plugins-official`  
  * <font color="red">Please note</font> I think this has to be run only once per install.  
* Run (in Claude terminal): `/plugin install CHANNEL_HERE@claude-plugins-official`  
  * Replace `CHANNEL_HERE` with the channel (`discord`, `telegram`, etc)  
* Run (in Claude terminal): `/reload-plugins`  

3\. Exit Claude Code, and then re-enter:  
* Run (in Claude terminal): `/exit`  
* Run (in regular terminal): `claude --channels plugin:CHANNEL_HERE@claude-plugins-official`  
  * Replace `CHANNEL_HERE` with the channel (`discord`, `telegram`, etc)  


4\. Link your bot token ([Telegram](agentic/general/keys_tokens?id=telegram) / [discord](agentic/general/keys_tokens?id=discord)) by putting it in this command and then running it in the Claude Code CLI: `/CHANNEL_HERE:configure 0123456789:AAAAABBBBBCCCCCCDDDDD`  
* Replace `CHANNEL_HERE` with the channel (`discord`, `telegram`, etc)  
* Replace `0123456789:AAAAABBBBBCCCCCCDDDDD` with your real bot ID  

5\. Exit Claude Code, and then re-enter:  
* Run (in Claude terminal): `/exit`  
* Run (in regular terminal): `claude --channels plugin:CHANNEL_HERE@claude-plugins-official`  
  * Replace `CHANNEL_HERE` with the channel (`discord`, `telegram`, etc)  
* <font color="purple">Yes</font>, you must do this again.  

6\. Send a DM / PM to your bot in (Telegram|Discord)  
* You will get a message back (similar to this) - take note of the ID it gives you:  

```  
Pairing required — run in Claude Code:

/telegram:access pair 12345e7
```  

7\. Run that line in the Claude Code terminal (taking note of your unique pairing ID): `/CHANNEL_HERE:access pair 12345e7`  
* Replace `CHANNEL_HERE` with the channel (`discord`, `telegram`, etc)  

8\. Lock it down (so no one else can use your bot): `/CHANNEL_HERE:access policy allowlist`  
* Replace `CHANNEL_HERE` with the channel (`discord`, `telegram`, etc)  

# Extra Setup (Telegram)  

Take these steps once you have [set up Telegram](agentic/claude_code/channels?id=setup-general-flow).  

## Allowing Users (Telegram)  

After you set up Telegram, and if you ran `/telegram:access policy allowlist`, no one else can access your bot. However, if you did want someone to use your Telegram bot, here is how you would do that:  

1\. They get their Telegram User ID  
* They can get this by messaging `@userinfobot` in Telegram - it will give back an ID  

2\. In the Claude Code terminal, type: `/telegram:access allow THEIR_TELEGRAM_USER_ID`  
* Obviously replace `THEIR_TELEGRAM_USER_ID`  
* If you ever want to revoke their access: `/telegram:access deny THEIR_TELEGRAM_USER_ID`  

> You may have to `/exit` Claude Code and then re-start with `claude --channels plugin:telegram@claude-plugins-official`  

# Extra Setup (Discord)  

Take these steps once you have [set up Discord](agentic/claude_code/channels?id=setup-general-flow).  

## Allowing Users (Discord)  

After you set up Discord, and if you ran `/discord:access policy allowlist`, no one else can access your bot. However, if you did want someone to be able to DM your Discord bot, here is how you would do that:  

1\. Get their User ID  
* Once you enable [developer mode](agentic/general/keys_tokens?id=discord), you can right-click a profile and get their User ID.  

2\. In the Claude Code terminal, type: `/discord:access allow THEIR_USER_ID`  
* Obviously replace `THEIR_USER_ID`  
* If you ever want to revoke their access: `/discord:access deny THEIR_USER_ID`  

> You may have to `/exit` Claude Code and then re-start with `claude --channels plugin:discord@claude-plugins-official`  


## Allowing Mentions (Discord)  

You can also allow people to mention your bot in a channel and have it respond :  

1\. Get the ChannelID  
* Once you enable [developer mode](agentic/general/keys_tokens?id=discord), you can right-click a _channel_ (not _server_, a channel) and get the channel's ID.  

2\. In the Claude Code terminal, type: `/discord:access group add  CHANNEL_ID`  
* Obviously replace `CHANNEL_ID`  

> You may have to `/exit` Claude Code and then re-start with `claude --channels plugin:discord@claude-plugins-official`  

---  

# JSON Configurations  

!> Its highly suggested that you make use of the in-app commands ([adding users](agentic/claude_code/channels?id=allowing-users), [adding channels](agentic/claude_code/channels?id=setup-general-flow), etc) and not edit the JSON directly. That said, here is what the JSON looks like.  


## access.json  

The <font color="purple">access.json</font> file (i.e. `~/.claude/channels/discord/access.json`, `~/.claude/channels/telegram/access.json`, etc) describes which users are allowed to interact with your bot, which groups (meeting rooms, group chats, etc). Here is the basic layout:  

```JSON
{
  "dmPolicy": "pairing",
  "allowFrom": [
    "0123456789012345678"
  ],
  "groups": {
    "9876543210987654321": {
      "requireMention": true,
      "allowFrom": []
    },
    "1231231231231231231": {
      "requireMention": true,
      "allowFrom": []
    }
  },
  "pending": {}
}
```  
* `dmPolicy` - what the policy is on recieving DMs (in this case, the user must be paired first).  
* `allowFrom` - lists the userIDs allowed to DM the bot.  
* `groups` - lists the various IDs of 'groups' (meeting rooms, group chats, etc) that the bot is allowed to respond in.  
* `requireMention` - is the bot required to be mentioned specifically?  
  * Its a smart bet to leave this `true` unless you want it to respond to everything said.  
* `pending` - the userIDs that have requested DM access.  

## .env  

The <font color="purple">.env</font> file (i.e. `~/.claude/channels/discord/.env`, `~/.claude/channels/telegram/.env`, etc) houses that specific chat app's API Key.  It typically has one line in it:  
```
CHAT_APP_BOT_TOKEN=XYZXYZXYZ  
```  

Where `CHAT_APP` is a chat app name (Discord, Telegram, etc - all caps).  

## settings.json  

The <font color="purple">settings.json</font> file is specific to your project directory and is located in `YOUR_PROJECT_DIRECTORY/.claude/settings.json`. It lists your enabled plugins, amongst other things:
```
{
  "enabledPlugins": {
    "telegram@claude-plugins-official": true,
    "discord@claude-plugins-official": true
  }
}
```  

## settings.local.json  

The <font color="purple">settings.local.json</font> file is specific to your project directory and is located in `YOUR_PROJECT_DIRECTORY/.claude/settings.local.json`. It lists your allowed permissions. An example:  
```
{
  "permissions": {
    "allow": [
      "Bash(mkdir -p ~/.claude/channels/telegram)",
      "Read(//home/user/.claude/channels/**)",
      "Bash(chmod 600 ~/.claude/channels/telegram/.env)",
      "mcp__plugin_telegram_telegram__reply",
      "Bash(chmod:*)",
      "Bash(mkdir -p ~/.claude/channels/discord/approved)",
      "mcp__plugin_discord_discord__reply"
    ]
  }
}
```  


---  

# Daily Operations  

## Running Claude Code  

In order for the bot to be available it must be running constantly. One way you could do this is run this:
```bash  
tmux new -s claude
claude --channels plugin:CHANNEL_HERE@claude-plugins-official
```  
* Remember, you CAN run the same bot across multiple channels - for example, to have the same agent monitor / respond to Telegram _and_ Discord, just list both channels: `claude --channels plugin:telegram@claude-plugins-official plugin:discord@claude-plugins-official`  

Then detach with `Ctrl+B` and _then_ `D`.  


## Script 

You can write a bash function that would automatically launch Claude in channel mode:  

```bash
function claude.bot {
    tmux new-session -d -s some_bot "cd /SOME/DIR/HERE && claude --channels plugin:telegram@claude-plugins-official plugin:discord@claude-plugins-official";
    echo "Claude Bot started - re-attach with: tmux attach -t some_bot ... to detach again: Ctrl+B, THEN D (by itself).";
}
export -f claude.bot
```  

## Allowing Users  

Occasionally, you will have to allow other users to use your bots. The basic way to do this is to run `/CHANNEL_HERE:access allow USER_ID` (replace `CHANNEL_HERE` with the channel (`discord`, `telegram`, etc) and `USER_ID` with their chat app specific ID). This is how you get that ID for [Discord](agentic/claude_code/channels?id=allowing-users-discord), and this is how you get it for [Telegram](agentic/claude_code/channels?id=allowing-users-telegram).  

> You may have to `/exit` Claude Code and then re-start with the `--channels` flags of your choice.  

## Removing Users  

If you need to revoke someone's access to your bot, the process is straightforward.
```
/CHANNEL_HERE:access deny USER_ID
```  
* Replace `CHANNEL_HERE` with the channel (`discord`, `telegram`, etc)  
* Obviously replace `USER_ID` with the real User ID  

Their ID is the same one you used when you originally added them. If you have forgotten it, you can find it in [access.json](gentic/claude_code/channels?id=accessjson) - it will be listed in the allowFrom array.  

!> You may have to /exit Claude Code and then re-start with the --channels flags of your choice for changes to take effect.  

## Removing Groups  

If you want to remove a group (Discord channel, Telegram group, etc) so the bot stops responding to @mentions:  
```
/CHANNEL_HERE:access group rm CHANNEL_ID
```  
* Replace `CHANNEL_HERE` with the channel (`discord`, `telegram`, etc)  

The `CHANNEL_ID` is the same one you used when you originally added the group. If you have forgotten it, you can find it in [access.json](gentic/claude_code/channels?id=accessjson).  

!> You may have to /exit Claude Code and then re-start with the --channels flags of your choice for changes to take effect.  

## Managing Context  

Claude Code keeps a running history of your conversation - this is called the <font color="purple">context window</font>. The longer your bot runs, the more context it accumulates; this can slow down responses and eventually hit limits. There are two ways to manage this:  
* `/clear` - Wipes the conversation history entirely and starts fresh. The bot stays running and your Telegram/Discord connection is unaffected - only the conversation history is wiped. This is the recommended option for a long-running bot.  
* `/compact` - Instead of wiping history, this summarizes the conversation down to a smaller footprint. Useful if you want Claude to retain some memory of what has been discussed but need to free up space.  

Claude Code has automatic context compaction built in - when the context window gets close to its limit, it will compact automatically rather than crash. That said, it is good practice to run /clear or /compact manually if you notice responses slowing down.

!> `/clear` and `/compact` must be run from inside the Claude Code terminal (i.e. inside your tmux session). Run `tmux attach -t YOUR_SESSION_NAME` to reattach first.  

---  

# Direct Mode  

> Now that <font color="green">channels</font> are a thing, <font color="purple">Direct Mode</font> is less relevant. That said, there may be a niche need for them - OR, in some cases, the target chat application is not yet supported by Claude Code - so they are mentioned here.  

<font color="purple">Direct Mode</font> - as opposed to a <font color="green">channel</font> - means the Claude Code agent can remotely contact you, _but_ you cannot contact Claude Code; there are <font color="purple">MCP</font> servers you can use to facilitate this.  

> All examples in this section require Node.js (`npx`). See [MCP Servers](/agentic/claude_code/mcp?id=configuration) for installation instructions if needed.  

## Slack (Direct Mode)  

Get a [Slack bot token](agentic/general/keys_tokens?id=slack); then, Register the MCP Server:  
```bash
claude mcp add slack --scope user \
  -e SLACK_BOT_TOKEN=xoxb-0000000000000-00000000000000-xxxxxxxxxxxxxxxxxxxxxxxx \
  -e SLACK_TEAM_ID=T0000000000 \
  -- npx -y @modelcontextprotocol/server-slack
```

Verify it connected:

```bash
claude mcp list
```

You should see `slack: npx -y @modelcontextprotocol/server-slack - ✓ Connected`.  

## Discord (Direct Mode)  

> No official Anthropic-maintained Discord MCP server exists. The community server below is widely used — verify it is still maintained before relying on it.  

Get a [Discord bot token](agentic/general/keys_tokens?id=discord); then, Register the MCP Server:  

```bash
claude mcp add discord --scope user \
  -e DISCORD_TOKEN=your-bot-token-here \
  -e DISCORD_GUILD_ID=your-server-id-here \
  -- npx -y mcp-discord
```


## Telegram (Direct Mode)  

Get a [Telegram bot token](agentic/general/keys_tokens?id=telegram); then, Register the MCP Server:  

```bash
claude mcp add telegram --scope user \
  -e TELEGRAM_BOT_TOKEN=0000000000:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx \
  -e TELEGRAM_CHAT_ID=0000000000 \
  -- npx -y @iqai/mcp-telegram
```  

## Signal (Direct Mode)  

!> Signal does _not_ technically have a MCP - it needs to use `signal-cli`; see [here](agentic/general/keys_tokens?id=signal) on how to install that.  
    
    
Setup [Signal](agentic/general/keys_tokens?id=signal); then, Claude Code can use Bash Directly. Since signal-cli is a command-line tool, an agentic AI can send Signal messages via its Bash tool without needing MCP at all. Just ask `"Send a Signal message to +1XXXXXXXXXX saying the backup completed"`  

The agent will run:

```bash
signal-cli -a +1XXXXXXXXXX send -m "the backup completed" +1YYYYYYYYYY
```

This works fine for simple send use cases. If you need something more structured or reusable, writing a thin Python MCP wrapper around signal-cli is the path forward — but that's a project in itself.  

---  