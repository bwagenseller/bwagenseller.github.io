# OpenClaw Install  

# Helpful Links  

* Health check help  
  * [Health](https://docs.openclaw.ai/gateway/health)  
  * [Troubleshooting](https://docs.openclaw.ai/gateway/troubleshooting)  
* [Control UI](https://docs.openclaw.ai/web/control-ui)  
* [Workspace backup](https://docs.openclaw.ai/concepts/agent-workspace)  
* [Security](https://docs.openclaw.ai/security)  
* [Remote Usage](https://docs.openclaw.ai/gateway/remote)  
* [Web search](https://docs.openclaw.ai/tools/web)  
* [Showcase](https://openclaw.ai/showcase)  


# Requirements  

* Minimum viable: 1 vCPU / 1–2 GB RAM / ~500 MB–1 GB disk free  
  * Comfortable / recommended for daily use: 2–4 cores, 4–8 GB RAM, SSD with 20–40 GB free

You can also run it on:  
* Raspberry Pi 5 (4–8 GB models)
* Old laptops
* Basic VMs (even shared-hosting style)

Node.js itself idles super low (~100–500 MB RAM when quiet), spikes during tasks like browsing/email/scheduling (maybe 1–3 GB peak if doing heavy multi-tool stuff).  


# Installation

> This should <font color="red">not be run as root</font>.  

To start the installation process, simply run: `curl -fsSL https://openclaw.ai/install.sh | bash`  

After this, run: `openclaw onboard --install-daemon`  

## Completing the Install  

**<font size="4">Affirmation</font>**  

You will be asked `I understand this is personal-by-default and shared/multi-user use requires lock-down. Continue?` - Say `yes`.  
  
**<font size="4">Onboarding Mode</font>**  

For the `Onboarding mode` pick `QuickStart`  

**<font size="4">Model/auth provider</font>**  

You must select the Model/auth provider - this will be the LLM that will power OpenClaw. You can add additional models later, but for now, pick the one you like. 

Note that for most selections you will be required to have an <font color="purple">API Key</font>; you will find several things in this install need an <font color="purple">API Key</font>. <font color="purple">API Keys</font> typically cost money (although not always) - usually, you get an account with, say, Grok or Claude or Gemini, then you purchase 'credits' and you apply those credits towards an <font color="purple">API Key</font>. For Claude and Grok, you buy the credits, get the <font color="purple">API Key</font>, and then when your credits hit 0 your <font color="purple">API Key</font> stops working until you put more money on it. Others, like Brave's <font color="purple">API Key</font>, require you to enter a 'cap' for spending in any given month, and when that spending limit is hit you get cut off for the month. <font color="purple">API Keys</font> are one of the more annoying aspects of the current AI landscape.  

In any event, get your Claude / Grok / Gemini / whatever <font color="purple">API Key</font>, put some money towards it, copy the key, and then paste it here when prompted.  


**<font size="4">Channel Selection</font>**  

A <font color="purple">channel</font> is how you will communicate with the LLM, outside of the OpenClaw webpage itself. Popular choices are Slack, WhatsApp, Discord, and Telegram, with Telegram seemingly being the favorite. If you have a favorite select it, but most will pick <font color="green">Telegram</font>. Whatever you pick, you will almost certainly have to get either an <font color="green">API Key</font> or a token of some sort.  

I will assume you want <font color="green">Telegram</font> and already have an account. To obtain a <font color="green">Telegram</font> bot token, [go here](agentic/general/keys_tokens?id=telegram).  

> [See the docs](https://docs.openclaw.ai/telegram) for more information.  


**<font size="4">Web Search</font>**  


OpenClaw uses this to "search the web" when it needs real-time info (news, prices, flights, whatever research you need). The options are solid, but Brave Search is usually selected for this (its officially recommended); [here](agentic/general/keys_tokens?id=brave) are my instructions on how to get a Brave API Key. Once you get this key, enter it where prompted.  


> See [here](https://docs.openclaw.ai/tools/web) for more information.  


**<font size="4">Install missing Skill Dependencies</font>**  

This is OpenClaw asking if you want to auto-install npm/Python/whatever packages needed for the optional extra skills (plugins/tools) it can offer. These are not required for core functionality — the agent will run perfectly fine if you skip everything here (and most people do).  

For now, select `Skip for now (Continue without installing dependencies)`; once it's alive and responding in Telegram/whatever, add skills one-by-one only as needed.  

Examples:  
* Want GitHub? `openclaw skill install github`  
* Want summaries? `openclaw skill install summarize`  


This way:  
* No bloat on install  
* No surprise failures mid-onboarding  
* You control what gets deps installed  

**<font size="4">Set GOOGLE_PLACES_API_KEY for goplaces?</font>**  

This enables the `goplaces` skill (located [on GitHub](https://github.com/steipete/goplaces)). This is an optional skill that lets your agent query Google Places API for stuff like:
* Text-based place search  
* Place details (hours, ratings, photos, phone)  
* Reviews, nearby spots, resolve addresses  
* Outputs human-readable summaries or JSON for scripts  

It's handy for location-aware tasks e.g., `find parking near the stadium`, but its not a core OpenClaw skill. Most users skip it unless they need heavy geo stuff.  

That said, if you want to enable this, you must get a `GOOGLE_PLACES_API_KEY`. Google Places API requires a real API key from Google Cloud. 

To do so: Google Cloud project → enable Places API → add billing (yes, billing account required even for free tier) → create restricted API key (the free tier is $200/month credit (covers ~40k–100k+ requests depending on type), but billing must be enabled).  

Again, most do not select this, so feel free to skip.  


**<font size="4">Set GEMINI_API_KEY for nano-banana-pro?</font>**  

`nano-banana-pro` is an OpenClaw skill that wraps Google's Gemini models (Gemini 1.5 Flash/Pro/Exp etc.) as an extra LLM provider or for specific tasks like:  
* Multimodal reasoning (image + text analysis)  
* Long-context handling  
* Code generation / math / creative writing with Gemini's style  
* Sometimes used as a fallback or parallel model when your main LLM (e.g., Claude or Grok) is rate-limited or too expensive for certain queries.  

OpenClaw supports multiple LLM backends and `nano-banana-pro` is just its cute internal name for a lightweight/fast model slot — it's trying to wire up Gemini for that role.  

It needs a GEMINI_API_KEY from Google AI Studio / Google Cloud; my instructions on how to get a free account is [here](agentic/general/keys_tokens?id=google-gemini). _So long as its still free_ you might as well get one.  

**<font size="4">Set NOTION_API_KEY for notion?</font>**  

`notion` is a knowledge base / docs backend; it lets the agent read/write Notion pages, databases, etc. Since I use [docsify](learn_to_code/docsify/) - and I allow the agents to edit my markdown files - I skipped this, but if you do not have this you may want to look into it.  


**<font size="4">Set OPENAI_API_KEY for openai-image-gen?</font>**  

This is for DALL-E / OpenAI image generation. I didn't look into this as I have access to [Stable Diffusion](ai_media/stable_diffusion/), but you may want to check this out.  


**<font size="4">Set OPENAI_API_KEY for openai-whisper-api?</font>**  

I opted no for this - honestly, if you have a half-decent GPU, you can literally [run Whisper yourself](learn_to_code/python/machine_learning/whisperx), and Whisper is not _that_ taxing on a GPU.  

Even without a GPU, this is only necessary if you wish to voice your commands to OpenClaw - which is cool, but not necessary.  


**<font size="4">Set ELEVENLABS_API_KEY for sag?</font>**  

`sag` is the other side of Whisper: `sag` takes an LLM's output and converts it to speech. You can also do this locally with [F5-TTS](learn_to_code/python/machine_learning/f5-tts),[Kokoro](learn_to_code/python/machine_learning/kokoro-tts), or any other local TTS. Its nice, but not needed.  


**<font size="4">Hooks</font>**  

> See [the docs](https://docs.openclaw.ai/automation/hooks) for more information.  

Hooks let you automate actions when agent commands are issued. Example: Save session context to memory when you issue `/new` or `/reset`.  

The ones I selected:  
These are worth actually thinking about. Here's the breakdown:
* `boot-md` — Runs a markdown file on agent startup, like a CLAUDE.md-style context loader.  
  * Good if you want to prime the agent with your project context on boot.  
* `bootstrap-extra-files` — Similar, loads extra config/skill files at startup. More flexible version of boot-md.  
* `command-logger` — Logs every command issued.  
  * Good for debugging and auditing what the agent did.  
* `session-memory` — The one they literally use as the example: saves session context when you `/new` or `/reset` so the agent remembers across sessions.  

You can manage hooks later with:  
* `openclaw hooks list`  
* `openclaw hooks enable <name>`  
* `openclaw hooks disable <name>`  

Setup is complete! You can now log into the web interface _locally_ (on the same computer) - navigate to `http://127.0.0.1:18789/#token=YOUR_TOKEN_HERE`. `YOUR_TOKEN_HERE` will be given to you as the setup finishes - its important to grab this token.  

!> You may see `Health check failed: gateway closed (1006 abnormal closure (no close frame)): no close reason` or someting similar. As of 2026-03-22, this apparently happens; for me, I waited a few minutes and ran `systemctl --user status openclaw-gateway.service` and it was actually running. I dont know what the deal was, this may have been an oddball error that wasnt real.  

> At this point, whatever chat app you selected during the **Channel Selection** step is probably active; give the bot a chat message and see if it replies!  

## More On The Token  

The token is a simple bearer-style authentication token for the OpenClaw Gateway's web dashboard.  Its main purpose is it lets you (and only someone with that exact URL) log in to the browser-based control panel without setting up extra usernames/passwords during initial setup. It's tied to your local Gateway instance (loopback-bound, not exposed publicly).  

With it, you can view live logs, see running agents, test chat messages, tweak config, monitor tool usage (including Brave search burn), enable/disable hooks, etc. It's not a permanent password — many setups regenerate or rotate it on restart, or you can disable token auth and switch to basic auth/password later (via config or docs). Since the Gateway binds only to 127.0.0.1 (localhost), it's safe as long as your host isn't port-forwarded publicly.  

The first time you see your token is _just_ after the install is complete; look for something like `http://127.0.0.1:18789/#token=YOUR_TOKEN_HERE` just as the install wraps up.  

If the token is lost, just rerun `openclaw gateway status` or check `~/.openclaw/openclaw.json`; the token might be stored there or regenerated.  

> If a password _and_ a token is active, you can use either-or. The token is nice, because you can just embed it in a bookmark i.e. `https://IP_OF_MACHINE_RUNNING_OPENCLAW:18789/#token=YOUR_TOKEN_HERE`  

---  

# Basic Commands  

Here are some basic commands you may need to run on the command line.  

## Checking The Service  

To check to see if the OpenClaw service is running: `systemctl --user status openclaw-gateway.service`  

Conversely, you can try: `openclaw gateway status`  

## Re-Starting The Service  

If you need to re-start the OpenClaw service, for any reason: `systemctl --user restart openclaw-gateway.service`  

## Tail The Log  

If you need to watch the OpenClaw logs in real time: `journalctl --user -u openclaw-gateway.service -f`  

---  

# Setting a Password  

Its a good idea to set a password. To do so, you can run these two commands (which edits `~/.openclaw/openclaw.json`):  
```bash  
openclaw config set gateway.auth.mode password
openclaw config set gateway.auth.password "YOUR_STRONG_PASSWORD_HERE"  
systemctl --user restart openclaw-gateway.service
```  
* Obviously change `YOUR_STRONG_PASSWORD_HERE`  
* Don't forget to restart!  

If you really wish, you can set the password in the environment variable `OPENCLAW_GATEWAY_PASSWORD` on the command line like so:  
```bash  
openclaw config set gateway.auth.mode password  
export OPENCLAW_GATEWAY_PASSWORD="YOUR_STRONG_PASSWORD_HERE"  
systemctl --user restart openclaw-gateway.service
```  
* You only have to run `openclaw config set gateway.auth.mode password` once.  


For reference, this modifies `~/.openclaw/openclaw.json`:  
```json  
...
  "gateway": {
...
    "auth": {
      "mode": "password",
      "token": "YOUR_TOKEN_HERE",
      "password": "YOUR_PASSWORD_HERE"
    },
...
  }
```  

!> Its recommended that you run `openclaw security audit --fix` after you update the password to check for obvious security issues.  

> If a password _and_ a token is active, you can use either-or. The token is nice, because you can just embed it in a bookmark i.e. `https://IP_OF_MACHINE_RUNNING_OPENCLAW:18789/#token=YOUR_TOKEN_HERE`  

---  

# Make WebUI Reachable  

You have two options to make the webUI reachable on any machine on your local network.  

## WebUI via SSH  

You can use SSH tunneling in order to make the WebUI reachable on any local machine. To do so, run this command in a terminal on a computer that is not running OpenClaw on your network:  
```bash  
ssh -N -L 18789:127.0.0.1:18789 USERNAME@IP_OF_MACHINE_RUNNING_OPENCLAW
```  
* The `USERNAME` is the username of the Linux account running OpenClaw.  
* You will also have to replace `IP_OF_MACHINE_RUNNING_OPENCLAW` with the real IP.  

Now, you can log into the webUI of OpenClaw on your current machine - navigate to this web address: `http://localhost:18789/#token=YOUR_TOKEN_HERE`  
* `YOUR_TOKEN_HERE` is the token [discussed here](agentic/openclaw/install?id=more-on-the-token).  

## WebUI Direct Access  

> This is my preferred method.  

You must set `gateway.bind lan` if you wish to simply navigate to the local website without a tunnel. To do this, run: the following:  
```bash  
openclaw config set gateway.bind lan` 
openclaw config set gateway.controlUi.allowedOrigins '["https://IP_OF_MACHINE_RUNNING_OPENCLAW:18789"]'
systemctl --user restart openclaw-gateway.service
```  
* This modifies `~/.openclaw/openclaw.json`  
* Note, it _is_ `https`  
* You will also have to replace `IP_OF_MACHINE_RUNNING_OPENCLAW` with the real IP.  
  * If you do not do this, you may see the error `origin not allowed` and you will be denied the login  
  * And yes, you will have to run it twice - first for `http` and then for `https`  

If you would rather set it in `~/.openclaw/openclaw.json` you _can_ do that as well (but its recommended to just use `openclaw config set gateway.bind lan`). If you really want to edit this file, its:  
```json  
...
  "gateway": {
    "port": 18789,
    "mode": "local",
    "bind": "lan",
    "controlUi": {
      "allowedOrigins": [
        "https://IP_OF_MACHINE_RUNNING_OPENCLAW:18789"
      ]
    },
...
  }
```  
* You will also have to replace `IP_OF_MACHINE_RUNNING_OPENCLAW` with the real IP.  

...and then restart with `systemctl --user restart openclaw-gateway.service` . Really, though - just use `openclaw config set gateway.bind lan`.  

> At this point, you can access the webUI via navigating to `http://IP_OF_MACHINE_RUNNING_OPENCLAW:18789/#token=YOUR_TOKEN_HERE`, where `IP_OF_MACHINE_RUNNING_OPENCLAW` and `YOUR_TOKEN_HERE` are real values.  

## Add HTTPS Cert  

If connecting from another machine on the lan, you _will_ have to us HTTPS with a self-signed cert - this will almost assuredly be a requirement from your browser; if you get the message `control ui requires device identity (use HTTPS or localhost secure context)` this is almost certainly what this means.  

Too add the cert, run this on the machine / Linux account running OpenClaw:  
```bash  
mkdir -p ~/.openclaw/certs;
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 \
  -keyout ~/.openclaw/certs/openclaw.key \
  -out ~/.openclaw/certs/openclaw.crt \
  -subj "/CN=IP_OF_MACHINE_RUNNING_OPENCLAW" -addext "subjectAltName=IP:IP_OF_MACHINE_RUNNING_OPENCLAW"
```  
* You will also have to replace `IP_OF_MACHINE_RUNNING_OPENCLAW` with the real IP.  

Then, in `~/.openclaw/openclaw.json`, add this under the `gateway` section:
```json
  "gateway": {
...
    "tls": {
      "enabled": true,
      "cert": "/home/ai_agent/.openclaw/certs/openclaw.crt",
      "key": "/home/ai_agent/.openclaw/certs/openclaw.key"
    }
...
  }
```  

Then restart: `systemctl --user restart openclaw-gateway.service`  

Back on the machine that you are using to access the webUI, Accept browser cert warning → password → connect.  

## Add Device  

You now may get the error `pairing required` - this means your current device accessing the webUI is not authorized.  

To show the current deice list, type `openclaw devices list` ; this may show a list like so:
```bash  
Pending devices:
- ID: abc123 (from IP 192.168.1.xxx, browser: Chrome on Windows/Mac, requested at [time])
```  

To approve the pending device: `openclaw devices approve <REQUEST-ID-from-list>` (i.e. 'openclaw devices approve abc123', and note: its _not_ DeviceID).  

> To my knowledge, there is no blanket approval - you need the request ID, and really, that is probably for the best.  


---  

# Pair Telegram  

If you try to say something to your Telegram bot and you get something back like this:
```
OpenClaw: access not configured.

Your Telegram user id: 1234567890

Pairing code: AAAABBBB

Ask the bot owner to approve with:
openclaw pairing approve telegram AAAABBBB
```  

It means you need to approve the pairing; do exactly what it says, run: `openclaw pairing approve telegram AAAABBBB`  

