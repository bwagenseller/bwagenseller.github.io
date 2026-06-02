# API Keys, Tokens, and Identifiers  

Agentic architecture relies on _several_ API Keys, tokens, and other identifiers. This section is intended to showcase how to get several of these tokens, as well as any pertinent notes on them.  

# LLM Keys  

LLM API Keys are the meat and potatoes of agentic AI - in almost every case, an agent requires an LLM to run. You will need many API keys, but this is the most important one. Popular choices are Claude, Grok, and Gemini (Google) - Gemini apparently does has a free tier.  

## Anthropic (Claude)  

Claude is Anthropic's flagship LLM family (Claude 3.x, Claude Sonnet, Claude Haiku, etc.) and is widely considered one of the top choices for agentic AI — strong reasoning, long context, and excellent instruction-following.

1\. Go to [https://console.anthropic.com](https://console.anthropic.com) and sign up or log in.

2\. Add credits  
  * Go to **Billing** in the left sidebar and add a payment method  
  * Purchase credits — there is no free tier; you pay for what you use  
  * Unlike some providers, Anthropic does not auto-charge; you buy a block of credits upfront and they drain as you make API calls

3\. Create an API Key  
  * Go to **API Keys** in the left sidebar  
  * Click **+ Create Key**, give it a name, and copy it — it starts with `sk-ant-...`  
  * Store it somewhere safe; Anthropic will not show it again after creation  

> As of 2026-03-22: Claude Sonnet is the best balance of capability and cost. Claude Haiku is fast and cheap for lightweight tasks. Claude Opus is the most capable but significantly more expensive — use it sparingly unless you need it. Pricing is per-token (input + output), so verbose prompts cost more. Keep an eye on your usage in the console.

> **Tip:** Set a spending limit under **Billing → Usage Limits** to avoid surprise charges if something goes into an unexpected loop.


## Grok (xAI)

Grok is xAI's LLM family and a solid Claude alternative — particularly strong at real-time information (it has live access to X/Twitter data). Good reasoning, competitive pricing, and worth having as a secondary model.

1\. Go to [https://console.x.ai](https://console.x.ai) and sign up or log in (an X/Twitter account is not required).

2\. Add credits  
  * Go to **Billing** in the left sidebar and add a payment method  
  * Purchase credits — xAI previously offered $150 in free monthly credits as part of a data-sharing program, but **discontinued that in May 2025**. It is now pay-as-you-go like Anthropic  
  * New users may receive promotional credits automatically on signup — check your balance before buying

3\. Create an API Key  
  * Go to **API Keys** in the left sidebar  
  * Click **+ Create API Key**, give it a name, and copy it — it starts with `xai-...`  
  * Store it somewhere safe; it will not be shown again after creation

> As of 2026-03-22: Grok 4.1 Fast is very cost-effective (~$0.20/$0.50 per million input/output tokens) and good for everyday tasks. Grok 4 is the full reasoning model and costs more. Check [x.ai/api](https://x.ai/api) for current model pricing as xAI updates this frequently.

> **Tip:** Set a spending/usage limit in the console to avoid runaway charges. Same advice as every other API key on this page.


## Google Gemini  

To get a Google Gemini API key:  

1\. Go to [https://aistudio.google.com/app/apikey](https://aistudio.google.com/app/apikey) (you must log in first).  

2\. _If_ this is your first time here, you will need to create a 'Project' first - click `Projects` on the left hand sidebar, and then click `+ Create a new project` (around the top right-ish of the page).  
  * Name it something appropriate and then click `Create Project`.  

3\. Now, click `API Keys` on the left hand sidebar, and then click `Create API Key` (around the top right-ish of the page).  
  * Name it something appropriate, pick the project you just created, and then click `Create key`.  

4\. You now have a key - click it to get the _full_ key (once you click it, a popup will come up and show you under `API Key`)  

> You will notice it says `Set up billing / Free tier` under `Quota Tier. Apparently, the free tier is generous for light use (60–1,500 requests/day depending on model, resets daily). As of 2026-03-22, no billing is required for the free Gemini API key (unlike Google Places API, which forces billing). Currently, the free tier gives you Gemini 1.5 Flash for free (fast, cheap, good for lightweight tasks), and 1.5 Pro at a lower free quota. If you are just starting out and do not want to pay for an LLM, this is probably the best deal you will get.  

## OpenRouter  

OpenRouter is basically the "Netflix of AI models" — one single API key that gives you access to hundreds of models from Anthropic, Google, OpenAI, Grok, Mistral, Meta, Qwen, DeepSeek, etc. Instead of signing up for 10 different providers, you just talk to OpenRouter and it routes your request to whichever model you want (or the cheapest/fastest one that matches your needs).  

To get an OpenRouter API Key:  
1\. Go to [https://openrouter.ai/keys](https://openrouter.ai/keys)  

2\. Sign up / log in (Google, GitHub, or email works)  

3\. Click "Create Key"  

4\. Give it a name (e.g. "OpenClaw")  

5\. Copy the key immediately (you won't see it again)  

No credit card required to start — you can use the free models right away.  

## OpenCode Zen  

OpenCode Zen is a curated AI model gateway run by the OpenCode team.. Its a popular open-source AI coding agent (terminal-first, with desktop app support) that lets you use almost any LLM.  

Zen is their add-on service:
* A hand-picked list of models that the OpenCode team has tested and optimized specifically for coding agents (tool calling, long context, agentic workflows, etc.).  
* Acts as a convenient gateway — instead of managing 10 different API keys and figuring out which model works best, you connect once to Zen and get access to a reliable, pre-vetted collection.  
* Some models in Zen are completely free during their beta periods (like `mimo-v2-pro-free`, `minimax-m2.5-free`, `big-pickle`, etc.).  
* Others are paid but often cheaper or more stable than going direct to the original provider.  

!> OpenCode Zen is not technically free - as of March 2026, you need to put 20 bucks against your account. That said, you get a generous token count upfront. If you _make sure to cap your spending_ to something small (like 5 bucks a month), this is a cheap model source.  

To sign up:  

1\. Go to the official site: [https://opencode.ai/zen](https://opencode.ai/zen)  

2\. Click Sign Up (top right) - Use your Google or GitHub login  
  * I didnt see an option for a direct login with email + password  

3\. Go to `Billing, then click 'Enable Billing` - follow the prompts to set up your account.  
  * Add the minimum balance ($20 + ~$1.23 fee)  
  * Add exactly $20 (this is currently the minimum required to activate the account) using a credit/debit card.  

4\. **Immediately set strict limits** once your balance is added:  
  * Go to Billing (or Settings -> Billing).  
  * **Set Monthly Usage Limit** to a low amount, e.g. $5 or $10.  
    * This is a hard cap; OpenCode will stop using paid models once you hit it for the month.  
  * **Disable Auto-Reload / Auto-Top-Up** completely.
    * By default, Auto reload is disabled - but **double-check this**; when your balance drops below $5, it may automatically add another $20.  

5\. Generate your API key  
  * You almost assuredly have one generated already - check it by going to the `API Keys` section.  
    * If not, create one.  

!> Usage of the 'free' - or close to free models can use your prompts in its own training, and they may end up selling data to a 3rd party (I am not sure, it was unclear to me). If privacy is a concern, be careful!  

# Deepseek  

You can get an API Key [from Deepseek](https://platform.deepseek.com/usage), but it will cost some money.  

## Deep Seek Via Claude Code  

!> Using this method, you must pick either Deepseek or Claude - I do not think you can use both on the same box.  

You can use Deepseek in Claude's harness - install Claude Code, but _do not_ log into Claude - instead, add these environment variables:  
```bash
export ANTHROPIC_BASE_URL=https://api.deepseek.com/anthropic
export ANTHROPIC_AUTH_TOKEN=YOUR_DEEPSEEK_API_KEY
export ANTHROPIC_MODEL=deepseek-v4-pro[1m]
export ANTHROPIC_DEFAULT_OPUS_MODEL=deepseek-v4-pro[1m]
export ANTHROPIC_DEFAULT_SONNET_MODEL=deepseek-v4-pro[1m]
export ANTHROPIC_DEFAULT_HAIKU_MODEL=deepseek-v4-flash
export CLAUDE_CODE_SUBAGENT_MODEL=deepseek-v4-flash
export CLAUDE_CODE_EFFORT_LEVEL=max
```  
* Obviously replace `YOUR_DEEPSEEK_API_KEY`  

It _should_ be working at this point. If claude asks you to log in even after setting the variables, it usually means the app hasn't marked its initial setup as "complete." You can fix this by creating or editing the file ~/.claude.json (in your user home directory) and making sure it includes these lines. The primaryApiKey can be any non-empty placeholder:  
```json
{
  "hasCompletedOnboarding": true,
  "primaryApiKey": "sk-placeholder"
}
```  

---  

# Web Searches  

## Brave  

Sometimes, an agent needs to "search the web" when it needs real-time info (news, prices, flights, whatever research you need). Brave is a _very_ popular selection for this. To get a key, go to [Brave's api page](https://brave.com/search/api/) (you may need to sign up).  

Unfortunately, you are forced to pick a plan first - to do so:  

1\. Go to the `Available Plans` section in the left hand sidebar.  
  * Currently, there is a plan that costs $5 for 1,000 requests - and you get a free $5 every month (as of 2026-03-22 - this may change after that date), but _you must enter a credit card_.  

2\. After you pick a plan, go to `Usage Limits` on the left hand sidebar, then `+ Add Usage Limit`  
  * From what I understand, you can put `$5` as the limit and it will not go over the free credits you have been given, but **I strongly advise setting a limit**.  
  * You _probably_ wont go above $5 if you are just testing, but...better safe than sorry.  

3\. Once you set the usage limit, click `API Keys` on the left hand sidebar  
  * Click `+ Add API Key` to generate a key.  
  * Once done, you will get an API key that starts with `BSAd`.  


---  


# Chat App Accounts  

## Slack  

### Slack Bot Token  

Slack has an **official** Anthropic-maintained MCP server — the most reliable option on this page.

1\. Create a Slack App
* Go to [api.slack.com/apps](https://api.slack.com/apps) and click **Create New App → From Scratch**  
* Give it a name (e.g. "Claude Code") and pick your workspace  

2\. Set Bot Permissions  

In your new app, go to **OAuth & Permissions → Bot Token Scopes** and add:

| Scope | Direct<br>Mode? | Purpose |
|---|---| --- |  
| `chat:write` | Yes | Send messages to channels the bot is in |
| `chat:write.public` |  Yes | Send to any channel without joining |
| `channels:read` |  Yes | List channels |
| `users:read` |  Yes | Look up users by name |
| `channels:history` |  Yes | Read message history |  
| `app_mentions:read` | No | View messages that directly mention the bot (using the `@` symbol) in conversations that the app is in | 
| `channels:join` | No | Join public channels in a workspace. |  
| `chat:write.customize` | No | Send messages as @Slack Rick with a customized username and avatar. **Not truly required for <font color="purple">direct mode</font>**, but I appreciate it. |  
| `chat:write.public` | No | Send messages to channels the bot isn't a member of. **Not truly required for <font color="purple">direct mode</font>**, but I appreciate it. |  
| `im:history` | No | View messages and other content in direct messages that the bot has been added to. |  
| `im:write` | No | Start direct messages with people. |  
| `users:read` | No | View people in a workspace. |  
* There is <font color="purple">direct mode</font> (where its one way communication (LLM to you)) - or <font color="purple">Channels</font> can be used (you can speak to the LLM, and the LLM can speak back to you).  
  * Everything above is needed for a <font color="purple">Channel</font> - but not everything is needed for <font color="purple">direct mode</font> so therefore its denoted.  

3\. Install the App  

In **OAuth & Permissions**, click **Install to Workspace**. After approving, copy the **Bot User OAuth Token** — it starts with `xoxb-...`.

4\. Get Your Team ID  

Go to [api.slack.com/apps](https://api.slack.com/apps) and click into your app. The URL will look like:

```
https://app.slack.com/app-settings/TXXXXXXXXXX/AXXXXXXXXXX/oauth
```

The `T...` value right after `/app-settings/` is your Team ID.

5\. Enable Socket Mode  

> This is absolutely needed for [OpenClaw](http://192.168.1.230:9566/#/agentic/openclaw/) - and is more likely for things that use <font color="purple">channel</font> - but maybe not others. This is not necessary for <font color="purple">direct mode</font>.  

If you are using a <font color="purple">channel</font>, there needs to be a way for Slack to send your Agent any messages it receives - many things use a websocket connection for this (especially [OpenClaw](http://192.168.1.230:9566/#/agentic/openclaw/)).  

To enable this, click **Socket Mode** under **Settings**, and then toggle `Enable Socket Mode` to 'On'.  

6\. Enable Events  

> This is absolutely needed for [OpenClaw](http://192.168.1.230:9566/#/agentic/openclaw/) - and is more likely for things that use <font color="purple">channel</font> - but maybe not others. This is not necessary for <font color="purple">direct mode</font>.  

If you are using a <font color="purple">channel</font>, there needs to be a way for Slack to send your Agent any messages it receives. This can be done via a websocket _or_ HTTP (and we enabled the websocket in the last step). This will actually enable the events to happen.  

To enable this, click **Event Subscriptions** under **Features**, and then toggle `Enable Events` to 'On'.  

Now, click on the _Subscribe to bot events_ section and there will be a dropdown - add the following:  
* `app_mention` - Subscribes to only the message events that mention your app or bot.  
* `message.im` - A message was posted in a direct message channel.  
* `message.channels` - A message was posted to a channel.  

Now click `Save Changes`.  <font color="red">Please note</font> that If `Save Changes` is grayed out, it means you did not switch to socket mode - without switching to socket mode it will expect HTTP mode, and for that you would need a `Request URL`.  

!> As of March 2026, OpenClaw is struggling with events - if you select `app_mention` or `message.channels`, OpenClaw can duplicate responses (i.e. you send a DM or mention, it will respond twice). For now, I removed these two and simply left `message.im` - so I can only DM my bot. Hopefully OpenClaw fixes this in the future.  

7\. Install the App (again)  

> This is only needed if you enabled socket mode and / or enabled events above - if you did not, its not necessary to do this again. Matter of fact, you may not have had to save this previously, but I mention this now for posterity. After certain changes in Slack's API you _will_ have to 'Install to Workspace', so...its good practice to do this / know how to do this anyway.  

In **OAuth & Permissions**, click **Install to Workspace**.  

8\. Refresh Slack - In your Slack app, press `Ctrl` + `r`.  

9\. Register the bot with your agentic API of choice. This will be different for various platforms:  
* [Claude Code (direct mode)](agentic/claude_code/channels?id=slack-direct-mode)  

10\. Invite the Bot to a Channel

In Slack, go to any channel and type `/invite @BOT_NAME_HERE`. If you added `chat:write.public` you can skip this — the bot can post anywhere without being invited.

What You Can Do Now  
* "Post a message to #general saying the deployment is done"  
* "List all channels in the workspace"  
* "What were the last 10 messages in #dev?"  

### Slack App Token  

> This is not needed for Claude Code, but you need the `xapp-` token if you want to use Socket Mode (which is the default and recommended way OpenClaw connects to Slack). Your `xoxb-` token lets the bot send messages and act in your workspace.
The `xapp-` token is required to open a WebSocket connection to Slack so OpenClaw can receive messages (DMs, mentions in #general, etc.) in real time. Without the xapp- token, Socket Mode won’t work: your bot won’t see incoming messages.  

This is how you get the App-Level Token.  

1\. Go to [https://api.slack.com/apps](https://api.slack.com/apps) and log in  

2\. In the left sidebar, click Basic Information.  

3\. Scroll down to the section App-Level Tokens.  

4\. Click Generate Token and Scopes (or Add Token if you see the button).  

5\. Give it a name  

6\. Click Add Scope and add this one scope: `connections:write`  

7\. Click Generate.  

8\. Copy the token (it starts with xapp-...). Save it securely — you won’t see it again.  

### Slack Channel ID  

Often, you cannot use the channel name when setting up a bot to respond to messages in a channel - you _must_ use the channel ID. To get this, go to the Slack app and right-click the channel; at the bottom of the `About` section it will show the `Channel ID`.  

### Slack Bot Flair  

You can add some 'flair' to your bot in Slack (description, etc) - go to [https://api.slack.com/apps](https://api.slack.com/apps) and log in, and then click `Basic Information`. Scroll down a bit and you can set an icon, the name, etc etc.  

## Discord

**<font size="4">Do You Have a Discord Server?</font>**  

Discord bots cannot DM users directly — they must be invited to a server first, and all interactions flow through that server. If you don't have one, creating a private personal server takes about 30 seconds and is a common pattern for personal bot use.

**<font size="4">Creating a Personal Discord Server</font>**  

1\. In Discord, click the **+** button in the left sidebar  

2\. Select **Create My Own**  

3\. Choose **For me and my friends** (or just skip)  

4\. Give it a name (e.g. "Claude Code") and click **Create**  

That's it — you now have a private server you can invite the bot to. Nobody else will be in it unless you invite them.  

**<font size="4">Creating Your Bot</font>**  

1\. Create a Discord Application  
  * Go to [discord.com/developers/applications](https://discord.com/developers/applications) and click **New Application**  
  * Give it a name and click **Create**  

2\. Create a Bot  
  * In your application, go to **Bot** in the left sidebar  
  * Click **Add Bot** → **Yes, do it!**  
  * Under **Token**, click **Reset Token** and copy the token — this is your `DISCORD_TOKEN`  
  * Under **Privileged Gateway Intents**, enable:  
    * **Server Members Intent**  
    * **Message Content Intent**  

3\. Set Bot Permissions & Invite to Server  
  * Go to **OAuth2 → URL Generator** in the left sidebar  
  * Under **Scopes**, check `bot`  
  * Under **Bot Permissions**, check: `Send Messages`, `Send Messages in Threads`, `Attach Files`, `Read Message History`, `Add Reactions`, `View Channels`  
  * Copy the generated URL, open it in a browser, and invite the bot to your server  

4\. Get Your Server (Guild) ID  
  * In Discord, go to **Settings → Advanced** and enable **Developer Mode**. Then right-click your server name and select **Copy Server ID** — this is your `DISCORD_GUILD_ID`.

!> Note that there are also _channel_ and _user_ IDs - and for some agents you will _need these two instead of the server ID_. Each user _and_ channel has its own ID - once Developer Mode is on, just right click a user or channel for its ID! 

5\. Register the MCP server. This will be different for various platforms:  
* [Claude Code](agentic/claude_code/channels?id=discord-direct-mode)  


**<font size="4">What You Can Do Now</font>**  

* "Send a message to #general saying the build passed"  
* "List all channels on the server"  
* "What were the last 10 messages in #dev?"  

**<font size="4">Note: Channel IDs</font>**  


Discord's send tool requires a **channel ID**, not a channel name. Some agents (like Claude Code) will automatically look up the channel ID for you when you refer to a channel by name — but if it ever needs help, just ask it to fetch your server info first: `"Get my Discord server info and show me the channel IDs"`  

## Telegram

> Telegram has an official bot API making setup straightforward. As with Discord, use a community MCP server and verify it is still maintained.

1\. Create a Telegram Bot  
  * Open Telegram and search for **@BotFather**  
  * Send `/newbot` and follow the prompts to name your bot  
  * BotFather will give you a bot token in the format `1234567890:ABCdef...` — this is your `TELEGRAM_BOT_TOKEN`  

2\. Get Your Chat ID  

Send a message to your new bot, then visit:

```
https://api.telegram.org/bot<YOUR_ENTIRE_TOKEN_HERE>/getUpdates
```

Look for `"chat":{"id":...}` in the response — that number is your `TELEGRAM_CHAT_ID`.

3\. Register the MCP server. This will be different for various platforms:  
* [Claude Code (direct mode)](agentic/claude_code/mcp?id=telegram)  


**<font size="4">What You Can Do Now</font>**  

* "Send me a Telegram message saying the deployment finished"  
* "What are my last 5 Telegram messages?"  


## Signal

!> <font color="red">Warning</font>: Signal has no official API. Integration requires `signal-cli`, a Java-based command-line tool that registers as a Signal client on your phone number. Unlike Slack, Discord, and Telegram, **no Signal MCP server currently exists on npm**. The ecosystem hasn't caught up with Signal's complexity and unofficial-client restrictions. **Use a spare phone number, not your primary one** — see the risk explanation below.


**<font size="4">The Risk of Using Your Primary Number</font>**  

`signal-cli` is a legitimate, well-maintained open-source project (4,300+ GitHub stars, GPLv3 licensed, fully auditable). It stores your credentials locally and only communicates with Signal's official servers — no data exfiltration, no sketchy behavior.

However, Signal has been known to occasionally crack down on unofficial clients, similar to how WhatsApp bans numbers that use unofficial apps. If Signal flags your number:  
* **Most likely**: The `signal-cli` session gets invalidated and you just re-register — no harm done  
* **Worst case**: The phone number itself gets banned from Signal — and if that's your primary number, your real Signal account goes with it  

The risk is low (the community would be vocal if bans were common), but the consequence of losing your primary Signal account is high enough to warrant using a spare number. A cheap prepaid SIM or a VoIP number works fine.  


**<font size="4">Prerequisites: signal-cli</font>**  

You must install `signal-cli`:  
```bash
# Check the latest version at https://github.com/AsamK/signal-cli/releases
# As of March 2026 the latest is v0.14.1 — update the version number below if newer
wget https://github.com/AsamK/signal-cli/releases/download/v0.14.1/signal-cli-0.14.1-Linux-native.tar.gz
tar -xzf signal-cli-0.14.1-Linux-native.tar.gz
sudo mv signal-cli-0.14.1-Linux-native /opt/signal-cli
sudo ln -s /opt/signal-cli/bin/signal-cli /usr/local/bin/signal-cli
```

> Note: The filename format changed between versions — it no longer requires Java and now ships as a native binary (`-Linux-native`). Always verify the exact filename on the releases page before downloading.


**<font size="4">Link a Phone Number</font>**  

Signal requires a captcha to register new numbers.  

1\. Go to [signalcaptchas.org/registration/generate.html](https://signalcaptchas.org/registration/generate.html) in your browser  

2\. Solve the captcha  

3\. Right-click the **"Open Signal"** link and copy the link — it will look like `signalcaptcha://signal-recaptcha-v2.XXXXXXX...`  

4\. Run the register command with the captcha token:  

```bash
signal-cli -a +1XXXXXXXXXX register --captcha signalcaptcha://signal-recaptcha-v2.XXXXXXX...
```

5\. You'll receive an SMS with a verification code — verify with:

```bash
signal-cli -a +1XXXXXXXXXX verify <SMS_CODE>
```


**<font size="4">First Send: Rate Limit Captcha</font>**  

Signal may rate-limit your first send attempt and require an additional captcha. If you see `CAPTCHA proof required`, do the following:  

1\. Go to [signalcaptchas.org/challenge/generate.html](https://signalcaptchas.org/challenge/generate.html)  

2\. Solve the captcha  

3\. Right-click **"Open Signal"** and copy the link  

4\. Submit it along with the challenge token from the error output:  

```bash
signal-cli submitRateLimitChallenge --challenge <CHALLENGE_TOKEN> --captcha signalcaptcha://...
```

Then retry your send command — it should go through.  


**<font size="4">Use Bash Directly</font>**  

Since signal-cli is a command-line tool, an agentic AI can send Signal messages via its Bash tool without needing MCP at all. Just ask:

> "Send a Signal message to +1XXXXXXXXXX saying the backup completed"

The agent will run:

```bash
signal-cli -a +1XXXXXXXXXX send -m "the backup completed" +1YYYYYYYYYY
```

This works fine for simple send use cases. If you need something more structured or reusable, writing a thin Python MCP wrapper around signal-cli is the path forward — but that's a project in itself.  

---  

# Email  

## Gmail (also: Calendar)  

**<font size="4">Prerequisites: Google Cloud Project</font>**  

Unlike the chat platform integrations, Gmail requires setting up your own Google Cloud OAuth app. This is a one-time setup, and the credentials are reusable across any MCP client (Claude Code, OpenClaw, Aider, etc.).  

1\. Create a Google Cloud Project  
  * Go to [console.cloud.google.com](https://console.cloud.google.com) and sign in with your Gmail account  
  * Click **Select a project** → **New Project**  
  * Name it (e.g. "Agentic AI Gmail") and click **Create**  
  * Make sure your new project is selected in the top dropdown  

2\. Enable the Gmail API  
  * In the search bar, search for **Gmail API** and click it  
  * Click **Enable**  

3\. Enable Google Calendar API (Optional)  
  * If you also wish to use the Google Calendar API, search for **Google Calendar API** and click **Enable**  

4\. Configure OAuth Consent Screen  
  * Go to **APIs & Services** → **OAuth consent screen**  
  * Choose **External** and click **Create**  
  * Fill in:  
    * **App name**: anything (e.g. "Claude Code")  
    * **User support email**: your Gmail address  
    * **Developer contact email**: your Gmail address  
  * Click **Save and Continue** through the remaining screens  
  * Under **Test users**, add your Gmail address  

5\. Create OAuth Credentials  
  * Go to **APIs & Services** → **Credentials**  
  * Click **+ Create Credentials** → **OAuth client ID**  
  * Select **Desktop app** as the application type  
  * Name it and click **Create**  
  * Copy the **Client ID** and **Client Secret** — store these somewhere safe  

6\. Register the MCP server. This will be different for various platforms:  
* [Claude Code](agentic/claude_code/mcp?id=gmail)  
  * or [here](agentic/claude_code/mcp?id=calendar-google) if you are doing this for the calendar.  


**<font size="4">What You Can Do Now</font>**  

* Gmail  
  * "Show me my last 5 unread emails"  
  * "Search my inbox for emails from Amazon in the last 30 days"  
  * "Read the email with subject 'Meeting tomorrow'"  
  * "Search for any emails about my Plex subscription"  
* Calendar  
  * "What's on my calendar this week?"  
  * "Do I have anything on Monday?"  
  * "Create an event for a dentist appointment next Tuesday at 2 PM"  
  * "What calendars do I have access to?"  