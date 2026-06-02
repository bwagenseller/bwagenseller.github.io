# Models  

# Definition  

A <font color="purple">model</font> in the context of AI/ML is a computational system that has been trained on large amounts of data to learn statistical patterns, and can then apply those learned patterns to transform new inputs into useful outputs.  

In our case, we are concerned with <font color="green">LLMs</font> (<font color="green">Large Language Models</font>), which are trained on text to predict the next token; inputs are token sequences, outputs are probability distributions over the vocabulary. The "learned function" captures grammar, facts, reasoning, style, and more.  

In practice, you will use many different <font color="purple">models</font> in OpenClaw - and not _just_ LLMs, either. That said, the meat and potatoes of OpenClaw are, in fact, LLMs.  


# Model Name Structure  

When referencing models, the official structure is `provider/model:key-identifier`, where:  
* `provider` is the company who is hosting the model.  
* `model` is what the provider is calling the model.  
* `key-identifier`, sometimes called the `profile ID`, is the tag you used to identify your personal API Key in the global `~/.openclaw/agents/main/agent/auth-profiles.json` _or_ an agent-specific `~/.openclaw/agents/<agentID>/agent/auth-profiles.json`   
  * It's expected you have at least one named `default` per provider - you are allowed to omit `key-identifier` if a `default` exists and you want to use it.  
  * You can be a bit more creative with additional keys.  

An example: `anthropic/claude-sonnet-4-6:default` (this is equal to `anthropic/claude-sonnet-4-6`).  

---  

# Providers  

## Adding Provider - API Key     

If you have an API Key, use `openclaw models auth paste-token --provider PROVIDER` to add the provider (where the provider name is [listed in the list](agentic/openclaw/models?id=list-all-models)); this is for providers that have a plugin (like anthropic, xai, google, opencode, etc.).  

Some points:  
* This _will_ prompt you to paste your API Key - do so.  
* `default` is the key identifier - usually `default` unless you have multiple keys for the same provider 
* You _could_ add a `--agent AGENT_ID` (where `AGENT_ID` is an actual agent name) to the command if you wanted to add this for a _specific_ agent and not all of them.  

> For providers that don't have a plugin but use standard OpenAI-compatible APIs, you'd need to add them manually to `openclaw.json` or the `models.json` — the CLI seems oriented around known providers.  

## Adding Provider - OAuth   

> Most providers do not allow OAuth, so using this method is uncommon.  

_IF_ the provider allows for Oauth login / login from a webpage, you can use `openclaw models auth login --provider PROVIDER_NAME_HERE` (where the provider name is [listed in the list](agentic/openclaw/models?id=list-all-models)) to add a provider; this is for providers that have a plugin (like anthropic, xai, google, opencode, etc.). That runs an interactive auth flow.  

> For providers that don't have a plugin but use standard OpenAI-compatible APIs, you'd need to add them manually to `openclaw.json` or the `models.json` — the CLI seems oriented around known providers.  

---  

# Basic Model Commands  

!> Most commands update OpenClaw live - however, if you edit the JSON file directly, you would need to run `openclaw gateway restart` afterwards.  

## List All Models  

To list all models that OpenClaw knows about, type this on the command line: `openclaw models list --all`  

The list returns all models in `provider/model` [format](agentic/openclaw/models?id=model-name-structure).  

> Its good practice to use this listing when setting various models; it may be possible to use something different than what is returned by this list, but <font color="red">be careful</font> if you choose to do that.  

## See Current Model  

To see the current model:  
* Type `/model` in any [chat interface](agentic/openclaw/agents?id=chatting-with-your-agent)  
* Type `openclaw models` on the command line  

## See Model Status  

To see the model status (your current primary + fallbacks + auth info), run this on the CLI: `openclaw models status`  

## Set Temp Model  

To temporarily set a different model, type `/model PROVIDER/MODEL:KEY-IDENTIFER` (i.e. `/model anthropic/claude-sonnet-4-6:default` or simply `/model anthropic/claude-sonnet-4-6`) in any [chat interface](agentic/openclaw/agents?id=chatting-with-your-agent).  

## Set Primary Model  

To set the primary master default model, run: `openclaw models set PROVIDER/MODEL` (where `PROVIDER/MODEL` is a provider/model from [the list](agentic/openclaw/models?id=list-all-models)).  

If you wanted to set a specific agent's default model - say, the agent called `main` (the default), you would run: `openclaw models --agent main set PROVIDER/MODEL` (<font color="red">please note</font> I am told this works, but I cannot currently get it to save on the individual agent level). 

## Manipulating Fallbacks  

> A <font color="purple">Fallback Model</font> is a model that will be used instead if the primary model is not reachable.  

How fallbacks work:  
* OpenClaw tries the primary first.  
* If it fails (rate limit 429, token limit, auth error, timeout, etc.), it automatically moves to the next model in the fallbacks list (in order).  
* It can also failover inside the same provider if you have multiple auth profiles.  


To **list** your current fallback models: `openclaw models fallbacks list`  

To **add** a fallback model (to the bottom of the list), run: `openclaw models fallbacks add PROVIDER/MODEL` (where `PROVIDER/MODEL` is a provider/model from [the list](agentic/openclaw/models?id=list-all-models)).  

To **remove** a fallback model, run: `openclaw models fallbacks remove PROVIDER/MODEL`  

To **clear** the fallback list entirely, run: `openclaw models fallbacks clear`  

!> It is claimed that if you add `--agent AGENT_NAME_HERE` after `openclaw models` it will set whatever you are setting for a _specific agent_ - but I have found it didn't save anywhere, so I do not know if this actually works.  

---  

# Recommended Models  

## Which Model?  

That really is the big question, and its hard to answer. For me, I am a fan of using both Anthropic (Claude) and xAI (Grok), but others love Google Gemini and OpenAI (ChatGPT). Some of the others (OpenRouter, OpenCode) have some interesting free tiers, too.  

## Anthropic  

> I consulted Claude for this section.  

| Model | Input | Ctx  | Comment |  
| --- | --- | ---  | --- |  
| anthropic/claude-haiku-4-5 | text+image | 195k  |  |  
| anthropic/claude-opus-4-5 | text+image | 195k  |  |  
| anthropic/claude-opus-4-6 | text+image | 977k  |  |  
| anthropic/claude-sonnet-4-5 | text+image | 195k  |  |  
| anthropic/claude-sonnet-4-6 | text+image | 977k  |  |  


* There are dated models too - several (i.e. `anthropic/claude-sonnet-4-5-20250929`) 
  * I didnt list them, but these are sometimes used to keep consistency for production servers.  
  * You can look these up if you have a need.  
* `Opus` > `Sonnet` > `Haiku`  
  * The pricing reflects this  
  * `Opus` - $5 / $25 per MTok  
    * When you need deep reasoning, massive context, or multi-agent coordination, Opus 4.6 is the pick   
  * `Sonnet`  
    * $3 / $15 per MTok  
    * For daily use, Sonnet 4.6 is the standout  
  * `Haiku` - $1 / $5 per MTok  
    * For high-volume tasks where cost matters above all, Haiku 4.5 achieves 73.3% on SWE-bench (just 4 points below Sonnet 4.5)  

## Google Gemini    

> I consulted Gemini for this section.  

| Model | Input | Ctx  | Comment |  
| --- | --- | ---  | --- |  
| google/gemini-2.0-flash | text+image | 1024k  | Significantly cheaper than other options ($0.10–$0.30 per 1M input) but have a much smaller output limit (8K tokens). If OpenClaw tries to refactor a large file, these models will truncate the code mid-sentence. |  
| google/gemini-2.0-flash-lite | text+image | 1024k  |  |  
| google/gemini-2.5-flash | text+image | 1024k  | Significantly cheaper than other options ($0.10–$0.30 per 1M input) but have a much smaller output limit (8K tokens). If OpenClaw tries to refactor a large file, these models will truncate the code mid-sentence. |  
| google/gemini-2.5-flash-lite | text+image | 1024k  |  |  
| google/gemini-2.5-pro | text+image | 1024k  | Great at "reading" (analysis), but the 8K output limit makes it frustrating for "doing" (generation). |  
| google/gemini-3-flash-preview | text+image | 1024k  | **The Best Value**. It oddly outperforms Gemini 3 Pro in some coding tasks. High 66K output limit. |  
| google/gemini-3-pro-preview | text+image | 977k  |  |  
| google/gemini-3.1-flash-lite-preview | text+image | 1024k  |  |  
| google/gemini-3.1-pro-preview | text+image | 1024k  | **Top Tier**. Massive reasoning jump; 80.6% on SWE-bench. Best for multi-step debugging and complex logic. |  
| google/gemini-3.1-pro-preview-customtools | text+image | 1024k  | **Optimized for OpenClaw**. Specifically tuned to prefer user-defined tools over generic bash commands. |  
| google/gemini-flash-latest | text+image | 1024k  |  |  
| google/gemini-flash-lite-latest | text+image | 1024k  |  |  
| google/gemini-live-2.5-flash | text+image | 125k  | This is optimized for the Live API (voice/video). Using it for a text-based agent like OpenClaw is an inefficient use of its sub-second latency tuning. |  


The different names:  
* `Pro`  
  * Highest reasoning, best coding, and best at following complex "agentic" instructions without getting confused. 
  * Use for debugging, complex planning, or high-stakes code refactoring.  
* `Flash`  
  * Optimized for speed and "price-performance." It’s smart enough for most tasks but might miss subtle logic errors that Pro catches.  
  * Great for general web browsing, summarizing long docs, or basic script writing.  
* `Lite`  
  * Extremely fast and cheap. It is stripped down to handle high-volume, simple tasks (like classifying 1,000 emails or translating text).  
  * Use for "pre-processing" data before sending the hard stuff to Pro.  
* `Flash-Lite`  
  * A specific tier (introduced in the 2.0/3.0 era) that sits between Flash and Nano. It's built for "extremely low latency" (sub-second responses).  
  * Useful if you need OpenClaw to react instantly to live events or chat messages.  
* `Preview`  
  * The Beta. These are "bleeding edge" versions. They contain the newest features (like better tool-use) but might be slightly less stable than "Stable" versions.  
  * Recommended for OpenClaw because the "Preview" models usually have the best support for the latest agent tools.  


**<font size="4">Notes on the free tier</font>**  

* Pro Models  
  * Very restrictive.  
  * You might only get 2 to 5 requests per minute and 50 to 100 requests per day.  
* Flash Models  
  * Much more generous.  
  * You often get 15 requests per minute and up to 1,500 requests per day.  
  * Note: If OpenClaw is "looping" (trying a task, failing, and trying again), it can burn through a Free Pro quota in 10 minutes.  
* Privacy  
  * On the Free Tier, Google can use your inputs/outputs to train their models.  
  * If you are using OpenClaw on sensitive company data or private code, you should switch to a Paid Tier (where your data is kept private).  
* Token Cutoffs (Context)  
  * While both tiers usually allow the massive 1 Million token context, the Free Tier has a "Tokens Per Minute" (TPM) limit.  
  * If you paste a massive codebase into OpenClaw, the Free Tier might tell you to "Slow down" because you've sent too many tokens at once.  
* For "Free" experimentation:  
  * Start with `gemini-2.5-flash`  
    * It’s the most stable "balanced" model with high free limits.  
    * If it fails a task: Switch to `gemini-3.1-pro-preview`.  
      * It is much smarter, but watch your daily request count!  

## huggingface  

> [Huggingface](https://huggingface.co/) is probably the largest model repository that I know of. Apparently it runs some models, but I didnt look too far into it.  

| Model | Input | Ctx  | Comment |  
| --- | --- | ---  | --- |  
| huggingface/deepseek-ai/DeepSeek-R1-0528 | text+image | 160k  |  |  
| huggingface/deepseek-ai/DeepSeek-V3.2 | text+image | 160k  |  |  
| huggingface/MiniMaxAI/MiniMax-M2.1 | text+image | 200k  |  |  
| huggingface/MiniMaxAI/MiniMax-M2.5 | text+image | 200k  |  |  
| huggingface/moonshotai/Kimi-K2-Instruct | text+image | 128k  |  |  
| huggingface/moonshotai/Kimi-K2-Instruct... | text+image | 256k  |  |  
| huggingface/moonshotai/Kimi-K2-Thinking | text+image | 256k  |  |  
| huggingface/moonshotai/Kimi-K2.5 | text+image | 256k  |  |  
| huggingface/Qwen/Qwen3-235B-A22B-Thinki... | text+image | 256k  |  |  
| huggingface/Qwen/Qwen3-Coder-480B-A35B-... | text+image | 256k  |  |  
| huggingface/Qwen/Qwen3-Coder-Next | text+image | 256k  |  |  
| huggingface/Qwen/Qwen3-Next-80B-A3B-Ins... | text+image | 256k  |  |  
| huggingface/Qwen/Qwen3-Next-80B-A3B-Thi... | text+image | 256k  |  |  
| huggingface/Qwen/Qwen3.5-397B-A17B | text+image | 256k  |  |  
| huggingface/XiaomiMiMo/MiMo-V2-Flash | text+image | 256k  |  |  
| huggingface/zai-org/GLM-4.7 | text+image | 200k  |  |  
| huggingface/zai-org/GLM-4.7-Flash | text+image | 195k  |  |  
| huggingface/zai-org/GLM-5 | text+image | 198k  |  |  


## OpenAI (ChatGPT)  

> I consulted ChatGPT for this section.  

| Model | Input | Ctx  | Comment |  
| --- | --- | ---  | --- |  
| openai/codex-mini-latest | text+image | 195k  |  |  
| openai/gpt-4 | text | 8k  |  |  
| openai/gpt-4-turbo | text+image | 125k  |  |  
| openai/gpt-4.1 | text+image | 1023k  |  |  
| openai/gpt-4.1-mini | text+image | 1023k  |  |  
| openai/gpt-4.1-nano | text+image | 1023k  |  |  
| openai/gpt-5 | text+image | 391k  |  |  
| openai/gpt-5-chat-latest | text+image | 125k  |  |  
| openai/gpt-5-codex | text+image | 391k  |  |  
| openai/gpt-5-mini | text+image | 391k  |  |  
| openai/gpt-5-nano | text+image | 391k  |  |  
| openai/gpt-5-pro | text+image | 391k  |  |  
| openai/gpt-5.1 | text+image | 391k  |  |  
| openai/gpt-5.1-chat-latest | text+image | 125k  |  |  
| openai/gpt-5.1-codex | text+image | 391k  |  |  
| openai/gpt-5.1-codex-max | text+image | 391k  |  |  
| openai/gpt-5.1-codex-mini | text+image | 391k  |  |  
| openai/gpt-5.2 | text+image | 391k  |  |  
| openai/gpt-5.2-chat-latest | text+image | 125k  |  |  
| openai/gpt-5.2-codex | text+image | 391k  |  |  
| openai/gpt-5.2-pro | text+image | 391k  |  |  
| openai/gpt-5.3-codex | text+image | 391k  |  |  
| openai/gpt-5.4 | text+image | 266k  |  |  
| openai/gpt-5.4-pro | text+image | 1025k  |  |  
| openai/o1 | text+image | 195k  |  |  
| openai/o1-pro | text+image | 195k  |  |  
| openai/o3 | text+image | 195k  |  |  
| openai/o3-deep-research | text+image | 195k  |  |  
| openai/o3-mini | text+image | 195k  |  |  
| openai/o3-pro | text+image | 195k  |  |  
| openai/o4-mini | text+image | 195k  |  |  
| openai/o4-mini-deep-research  | text+image | 195k  |  |  
| openai-codex/gpt-5.1 | text+image | 266k  |  |  
| openai-codex/gpt-5.1-codex-max | text+image | 266k  |  |  
| openai-codex/gpt-5.1-codex-mini | text+image | 266k  |  |  
| openai-codex/gpt-5.2 | text+image | 266k  |  |  
| openai-codex/gpt-5.2-codex | text+image | 266k  |  |  
| openai-codex/gpt-5.3-codex | text+image | 266k  |  |  
| openai-codex/gpt-5.3-codex-spark | text+image | 125k  |  |  
| openai-codex/gpt-5.4 | text+image | 266k  |  |  


There are really 4 families of models here:  
* <font color="green">GPT</font>- (general-purpose models)  
  * "Balanced intelligence + usability"    
  * These are your default models  
  * Examples: `gpt-4.1`, `gpt-5`, `gpt-5.4`  
  * Best for: chat, coding (most cases), writing, multimodal tasks  
* <font color="green">Mini / Nano variants</font> (cheap + fast)  
  * "cheap workers"  
  * Examples: `gpt-4.1-mini`, `gpt-5-mini`, `gpt-5-nano`  
  * Best for: high-volume tasks, simple transformations, autocomplete / agents
  * Weak at: deep reasoning, complex debugging
* <font color="green">Codex variants</font> (code-specialized)  
  * “coding specialists”  
  * Examples: `gpt-5-codex`, `gpt-5.1-codex-max`, `codex-mini-latest`, `openai-codex/*`  
  * Best for: writing code from scratch, large refactors, repo-aware reasoning  
  * Weak at: general conversation, nuanced reasoning outside code
* <font color="green">O-series</font> (reasoning-first models)  
  * “deep thinkers”
  * Examples: `o1`, `o3`, `o3-pro`, `o4-mini`  
  * Best for: hard reasoning, math / logic, complex planning  
  * Tradeoffs: slower, more expensive per request, sometimes overthinks simple tasks  


**<font size="4">Scenarios</font>**    

* Best Reasoning  
  * o3-pro > o3 > o1
  * gpt-5.4 is close but more balanced
  * Use when: debugging something nasty, designing systems, tricky logic
  * Avoid for: simple CRUD code, formatting tasks (waste of money)
* Best General Model (sweet spot)
  * `gpt-5.4` > `gpt-5` / `gpt-5.1` > `gpt-4o` (still very good + cheaper)
  * These are your default picks
* Best Cost Efficiency
  * `gpt-5-mini` > `gpt-4.1-mini`  
  * Use when: heartbeats, batch jobs, agents, repetitive prompts
  * Weak at: architecture decisions, multi-step reasoning
* Best for Coding
  * `gpt-5-codex` / `gpt-5.1-codex-max`: highest raw coding power
  * `gpt-5.4`: best balanced coding + reasoning
  * Codex models shine on large codebases
  * GPT models are better for mixed tasks (code + explanation)
* Context Window (important)
  * Huge (1M tokens): `gpt-4.1`, `gpt-5.4-pro`  
  * Large (~400k): most `gpt-5`
  * Bigger context 
    * Does not mean better reasoning  
    * can load big repos  
    * can hold long conversations  

**<font size="4">openai vs openai-codex</font>**    

* `openai`  
  * Standard models  
  * General-purpose  
  * Includes codex variants too  
* `openai-codex`  
  * Tuned specifically for coding workflows  
  * Often more aggressive edits  
  * Often better repo navigation  
  * Often slightly different behavior  

**<font size="4">Task Use</font>**  

  * Everyday dev / chat - `gpt-5.4`  
  * Cheaper default - `gpt-4o`  
  * Heavy reasoning - `o3 or o3-pro`  
  * Large codebase edits - `gpt-5-codex`  
  * Fast + cheap pipelines - `gpt-5-mini`  
  * Massive context (docs/repos) - `gpt-4.1` or `gpt-5.4-pro`  

> If you're building a coding agent, try `openai-codex` - otherwise, stick to `openai`  

**<font size="4">OpenClaw recommendations (from ChatGPT / OpenAI itself)</font>**  

* Default: gpt-5.4  
* Cheap: gpt-5-mini  
* Reasoning: o3  
* Coding-heavy: gpt-5-codex  
* **For OpenClaw agent workflows, prioritize Codex-class models (`openai-codex/` amd `-codex`)** for repository and CLI-heavy coding tasks, but keep `gpt-5.4` as a top-tier fallback or even default for mixed coding + reasoning workloads.  
  * In OpenClaw-style agent workflows, prefer Codex-oriented models for code editing, refactors, tests, repo exploration, and CLI/tool-heavy tasks.  

## OpenCode  

> I consulted Grok for this section.  

| Model | Input | Ctx  | Comment |  
| --- | --- | ---  | --- |  
| opencode/big-pickle | text+image | 195k  | Solid general + coding performance (widely believed to be a rebranded GLM-4.6 or similar strong MoE). Good reasoning and tool use. **Popular favorite**. |  
| opencode/mimo-v2-omni-free | text+image | 256k  | Xiaomi's MiMo V2 Omni — good all-rounder with solid agentic capabilities and vision support. |  
| opencode/mimo-v2-pro-free | text+image | 1024k  | Standout — Xiaomi MiMo V2 Pro. Excellent tool calling, multi-step reasoning, and agent workflows. **One of the best free long-context models available** anywhere right now. Strong on coding + general tasks. |  
| opencode/minimax-m2.5-free | text+image | 200k  | Very strong coding & productivity model. Often praised as near Opus-level in real-world agent use. Excellent instruction following and tool use. Can be verbose. |  


* OpenCode is a popular open-source coding agent (CLI + desktop, like a free alternative to Cursor/Claude Code).  
* They generally have generous rate limits for free use.  
* Strong focus on tool calling, agentic coding loops, and long context.  

## OpenRouter  

> I consulted Grok for this section.  

| Model | Input | Ctx  | Comment |  
| --- | --- | ---  | --- |  
| openrouter/arcee-ai/trinity-mini:free | text+image | 128k  | **Solid for heartbeats** + normal lightweight agents. One of the better free options for tool use. |  
| openrouter/minimax/minimax-m2.5:free | text+image | 192k  | Strong real-world productivity, good coding/office tasks, decent agentic capabilities. One of the more “SOTA-like” free models. **Strong contender** for general agent work. |  
| openrouter/openai/gpt-oss-120b:free | text+image | 128k  | Surprisingly capable for a free model. Good reasoning, tool use, and agentic behavior (117B MoE, 5.1B active). One of the best free general-purpose options. Good fallback. |  
| openrouter/free | text+image | 195k  | Auto-routes to whatever free model is best/available for your request (smart filtering for tools/vision). |  
| openrouter/qwen/qwen3-coder:free | text+image | 256k  | **Currently one of the strongest free coding models** on OpenRouter. Excellent at code generation, debugging, and agentic coding loops. |  
| openrouter/stepfun/step-3.5-flash:free | text+image | 250k  | Free tier. Fast, efficient MoE, solid general reasoning + tool calling. Excellent all-rounder for heartbeats and normal agent tasks. **One of the top free picks**. |  
| openrouter/z-ai/glm-4.5-air:free | text+image | 128k  | Free tier. Decent safe choice for everyday lightweight use. |  

* Zero cost — but heavy rate limits: Typically ~20 requests per minute and 50–200 requests per day (higher if you buy even $10 of credits).  
* Tool calling / function calling works on most, but not always as reliably as paid services.  
* Great for hybrid setups: Use free models for 70–80% of lightweight stuff, escalate to your paid Grok/Claude when intelligence matters.  

## xAI (Grok)  

> I consulted Grok for this section.  

| Model | Input | Ctx  | Comment |  
| --- | --- | ---  | --- |  
| xai/grok-4 | text+image | 250k  | Best reasoning; most expensive.<br>Pick when task is critical and high-stakes, where accuracy > speed/cost.|  
| xai/grok-4-1-fast | text+image | 1953k  | Excellent balance. Very cheap ($0.20 / $0.50). Good for normal agent work that needs some thinking. |  
| xai/grok-4-1-fast-non-reasoning | text+image | 1953k | Best for heartbeat checks, as its cheap and uses the least amount of tokens on reasoning. |  
| xai/grok-4-fast | text+image | 1953k | Cheaper, good for normal use that does not require complex thinking. |  
| xai/grok-4-fast-non-reasoning | text+image | 1953k | Cheaper, good for normal use that does not require complex thinking. |  
| xai/grok-4.20-beta-latest-non-reasoning | text+image | 1953k |  |  
| xai/grok-4.20-beta-latest-reasoning | text+image | 1953k | Strong reasoning at $2 / $6. Noticeably better than Fast models on complex planning, but 10× more expensive. |  
| xai/grok-4.20-multi-agent-beta-latest | text+image | 1953k | Great, but very expensive to use and not fully supported in OpenClaw of today. |  
| xai/grok-code-fast-1 | text+image | 250k | Great for coding; cheaper for input, a bit more expensive for output.<br>Most OpenClaw users who do heavy coding end up routing routine/fast coding to grok-code-fast-1 and hard planning/debugging to a Grok-4 variant. |  

* `fast` models are usually more lightweight and thus cheaper.  
* `non-reasoning` means the model gives direct, fast answers without doing any extended internal chain-of-thought (CoT) or "thinking step by step" before replying.  
* `multi-agent` is xAI's specialized multi-agent variant of the Grok 4.20 Beta model (released around February/March 2026). It's a built-in team of 4 AI agents that collaborate in real time on every complex query.  
  * The four agents are usually:  
    * Grok (Captain/Coordinator) — Breaks down the task, assigns work, resolves conflicts, and synthesizes the final response.  
    * Harper (Research/Facts) — Handles real-time web + X (Twitter) searches, gathers fresh data, and does primary fact-checking.  
    * Benjamin (Logic/Math/Code) — Does rigorous step-by-step reasoning, verifies logic, handles math/coding, and stress-tests ideas.  
    * Lucas (Creative/Contrarian) — Challenges assumptions, spots weaknesses, adds creative angles, and plays devil's advocate.  
  * Dramatically lower hallucination rates (users report ~65% reduction in some tests).  
  * Expensive — $2 per million input tokens / $6 per million output tokens (roughly 10x more than the cheap grok-4-1-fast-non-reasoning at $0.20/$0.50).
  * Uses way more tokens internally because of all the agent debate → even higher real-world cost.
  * Slower latency (the collaboration takes time).
  * In OpenClaw - Some variants require a different API endpoint (not the standard /chat/completions), so not all setups support it out of the box. 

---  

# Models in the JSON   

## auth-profiles.json  

!> This file is very sensitive - protect it. In addition, you should _really_ use the [model](agentic/openclaw/models?id=basic-model-commands) or [providers](agentic/openclaw/models?id=providers) CLI commands before editing a JSON file directly - but, if you want to do it this way you can too, just run `openclaw gateway restart` afterwards.  

`auth-profiles.json` is where your API keys reside. This file is on a per-agent basis - if it does not have one it will _not_ re-use another agent's file. The main agent's file is located at `~/.openclaw/agents/main/agent/auth-profiles.json`, but every other specific agent outside of `main` would use their own at `~/.openclaw/agents/AGENT_NAME_HERE/agent/auth-profiles.json`.  

`auth-profiles.json` looks as follows:  
```JSON  
{
  "version": 1,
  "profiles": {
    "anthropic:default": {
      "type": "api_key",
      "provider": "anthropic",
      "key": "sk-your-actual-key-here"
    },
    "anthropic:backup": {
      "type": "api_key",
      "provider": "anthropic",
      "key": "sk-ant-2nd-key-here"
    }, 
    "xai:default": {
      "type": "api_key",
      "provider": "xai",
      "key": "xai-your-actual-key-here"
    },
    "google:default": {
      "type": "api_key",
      "provider": "google",
      "key": "AIzaSy-your-gemini-key-here"
    }
  }
}
```  
* The format is `provider:key-identifier`, which was [previously mentioned](agentic/openclaw/models?id=model-name-structure).  
  * The `provider` is [pre-defined](agentic/openclaw/models?id=list-all-models), but you make the `key-identifier` (although the convention is `default` for most cases).  
  * Don't be cute - use `:default` _at least once per provider_; if you have a second key, you can get creative.  
* Replace the placeholder keys with your real ones.  
* You can name them something else if you want (e.g. `xai:main` or `google:free`), but `:default` is the standard.  
* After editing the file, always run `openclaw gateway restart`.  

## openclaw.json  

!> You should _really_ use the [model](agentic/openclaw/models?id=basic-model-commands) or [providers](agentic/openclaw/models?id=providers) CLI commands before editing a JSON file directly - but, if you want to do it this way you can too, just run `openclaw gateway restart` afterwards.  

The file `~/.openclaw/openclaw.json` holds a great deal of information about your OpenClaw setup. The `agents` section is one of the larger sections of this file, and a good chunk of it is about the models used. There is an example JSON below. Note that this is not the full file - just the `auth` and `agents` sections.  

Lets delve into the `auth` section of `~/.openclaw/openclaw.json` first.  

```JSON
...
  "auth": {
    "profiles": {
      "anthropic:default": {
        "provider": "anthropic",
        "mode": "api_key"
      },
      "anthropic:backup": {
        "provider": "anthropic",
        "mode": "api_key"
      },
      "openrouter:default": {
        "provider": "openrouter",
        "mode": "api_key"
      },
      "opencode:default": {
        "provider": "opencode",
        "mode": "api_key"
      },
      "opencode-go:default": {
        "provider": "opencode-go",
        "mode": "api_key"
      },
      "xai:default": {
        "provider": "xai",
        "mode": "api_key"
      },
      "google:default": {
        "provider": "google",
        "mode": "api_key"
      }
    }
  },
...
```  

!> These <font color="red">must</font> have a match in [auth-profiles.json](agentic/openclaw/models?id=auth-profilesjson)!

The format for the model [was mentioned previously](agentic/openclaw/models?id=model-name-structure). The `key identifier` / `profile_id` is used when you have multiple keys (as in the example above - there are two 'anthropic' entries, one `:default` and one `:backup`).  You may notice that there are no API keys here - thats on a per-agent (and is set in [auth-profiles.json](agentic/openclaw/models?id=auth-profilesjson)).  

> After any config change run `openclaw gateway restart` on the CLI.  

```JSON  
...
  "agents": {
    "defaults": {
      "model": {
        "primary": "opencode/mimo-v2-pro-free", 
        "fallbacks": [
          "anthropic/claude-sonnet-4-6:backup",
          "xai/grok-4-1-fast-non-reasoning", 
          "anthropic/claude-sonnet-4-6" 
        ]
      },
      "models": {
        "opencode/mimo-v2-pro-free": { 
          "alias": "mimo"
        },
        "anthropic/claude-sonnet-4-6": {
          "alias": "sonnet",
          "params": {
            "cacheRetention": "short"
          }
        },
        "anthropic/claude-opus-4-6": {
          "alias": "opus"
        }, 
        "google/gemini-2.5-flash-lite": {
          "alias": "gemini-flash"
        }, 
        "xai/grok-4-1-fast-non-reasoning": {}
      },
      "workspace": "/home/ai_agent/.openclaw/workspace",
      "contextPruning": {
        "mode": "cache-ttl",
        "ttl": "1h"
      },
      "compaction": {
        "mode": "safeguard"
      },
      "heartbeat": {
        "every": "30m"
      }
    },
    "list": [
      {
        "id": "main",
        "identity": {
          "name": "Rick",
          "emoji": "\U0001f300"
        }, 
        "model": {
          "primary": "xai/grok-4-1-fast-non-reasoning",
          "fallbacks": [
            "xai/grok-4-1-fast-non-reasoning",
            "anthropic/claude-sonnet-4-6",
            "opencode/mimo-v2-pro-free",
          ]
        }
      }, 
      {
        "id": "coding",
        "model": {
          "primary": "google/gemini-2.5-flash",
          "fallbacks": ["deepseek/deepseek-reasoner"]
        }
      }
    ]
  },
...  

```  
* The primary and [fallback](gentic/openclaw/models?id=manipulating-fallbacks) models are set in `agents.defaults.model`  
  * <font color="red">Please note</font> that any provider you use _must_ be in `agents.defaults` _somewhere_, either in the primary or fallbacks - if you only put it in, say, an agent-specific list it will not work!  
* `agents.defaults.models` holds all providers/models available  
  * Notice the `alias` in some of them - you can set an alias so you do not have to type the entire thing when setting.  
* Notice the `:backup` on the one `anthropic` - this is instructing OpenClaw to use the key for anthropic associated with the specific key identifier `:backup` in `~/.openclaw/agents/main/agent/auth-profiles.json`.  
  * We do not have to list `:default` on the others as OpenClaw expects `:default` to be, well, the default - _make sure to use that key identifier unless there is a good reason to use a different key identifier_, mostly you already used `:default` and you have a secondary key for a specific provider.  


**<font size="4">The Allowlist Gotcha</font>**  

If `agents.defaults.models` is set in your config, it becomes a **whitelist**. Any model *not* in that list will be rejected with:

```
Model "provider/model" is not allowed. Use /model to list available models.
```  

That said, you will have to also update the key in [auth-profiles.json](agentic/openclaw/models?id=auth-profilesjson).  

---  

# Rule-Based Model Routing  

<font color="purple">Tiered / Rule-Based Model Routing</font> means using a different model if the right conditions are hit: usually, this revolves around security (is this request handing sensitive information) or complexity. Practically - you want to use cheap / free models for mindless tasks / low-brow tasks; since the free stuff usually spies on your prompts / uses your prompts for training data _and_ they arent usually terribly intelligent, you will long for a mechanism to automatically switch - and this is it. In short, you will probably want a different model for <font color="green">sensitive tasks</font> or <font color="green">high-stakes tasks</font>. OpenClaw does this through settings in `~/.openclaw/openclaw.json`.    

There are two ways to do this: <font color="green">OpenClaw's built-in rules + fallback chain</font> and <font color="green">using a small local router model</font>.  

## OpenClaw's Routing Rules  

You can rely on simple built-in fallbacks and keyword rules. This approach is 'OK' and can work. Edit `~/.openclaw/openclaw.json`:  
```JSON
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "opencode/mimo-v2-pro-free",
        "fallbacks": ["xai/grok-4-1-fast"],
        "routing": {
          "enabled": true,
          "rules": {
            "sensitive": {
              "patterns": ["sensitive", "confidential", "private", "secret", "password", "api_key", "nda", "ssn", "credit card", "financial", "medical", "legal", "contract"],
              "minTokens": 100,
              "model": "xai/grok-4-1-fast"
            },
            "complex": {
              "minTokens": 180,
              "model": "xai/grok-4-1-fast"
            }
          }
        }
      }
    }
  }
}
```  

In this example, `opencode/mimo-v2-pro-free` is a free model, but we do not want sensitive or complex requests going thorugh it: Normal heartbeats, simple tasks, and routine coding can stay on the free/cheap primary model., but anything else must go to `xai/grok-4-1-fast`. If the incoming message contains sensitive keywords, looks like it contains PII, or is long/complex → OpenClaw automatically escalates that specific request (and often the whole turn) to the paid Grok model.  

It should be noted that `patterns` are simple keyword / substring matches — they are NOT smart semantic categories. OpenClaw does NOT use another LLM to "figure out" if something is sensitive. It literally does a case-insensitive text search for any of the words/phrases you list.  

## LLM Rule Router  

> This option is better for accuracy.  

Many users run a tiny local router (Ollama qwen2.5-7b or similar) that classifies every request. Add this to `~/.openclaw/openclaw.json`:  
```JSON  
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "opencode/mimo-v2-pro-free",        // ← Your cheap/free default model
        "fallbacks": ["xai/grok-4-1-fast"],

        "routing": {
          "enabled": true,

          "router": {
            "baseUrl": "http://192.168.1.XXX:4001/v1",   // ← IP of your router host
            "model": "router",                           // This can be anything, but "router" is fine
            "apiKey": "dummy-key",                       // Doesn't matter much
            "api": "openai-completions"                  // Important - keep this
          }
        }
      }
    }
  }
}
```  
* `openai-completions` (or `openai`) is important - your script implements the standard OpenAI `/v1/chat/completions` endpoint.  
* `"model"` nay not be important if you are using the simple `llama-cpp-python[server]` script (below), but if you are running your own Python script (second option below) it _can_ be important, if you program it to be important.  

You will need to run llama.cpp for this - there are two ways to do it. The first way is to simply use the Python library `llama-cpp-python[server]` - here is how you install it:  
```bash
pip install llama-cpp-python[server]
```

Once installed, just run on whatever local host like so:
```
# Run the server (replace with your model path)
python -m llama_cpp.server \
  --model /path/to/your/router-model.gguf \
  --host 0.0.0.0 \
  --port 4001 \
  --n-gpu-layers -1 \
```

The second way is to write a `llama_cpp` script in Python yourself:  
```Python  
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
import uvicorn
from llama_cpp import Llama

app = FastAPI(title="OpenClaw Smart Router")

# ====================== CONFIG ======================
HOST = "0.0.0.0"
PORT = 4001

# === CHANGE THESE TO YOUR ACTUAL MODELS ===
MODELS = {
    "router": {                                 # Fast text-only router
        "path": "/path/to/your/text-model.gguf",           # e.g. Qwen2.5-7B or Phi-3.5
        "n_gpu_layers": -1,
        "context": 8192,
        "chat_format": "chatml"                            # Qwen / most modern models
    },
    "vision-router": {                          # Vision model
        "path": "/path/to/qwen2.5-vl-7b-instruct-q5_k_m.gguf",
        "n_gpu_layers": -1,
        "context": 8192,
        "chat_format": "chatml"                            # Qwen2.5-VL uses chatml
    }
}

loaded_models = {}

def get_llm(model_name: str):
    if model_name not in MODELS:
        raise HTTPException(status_code=400, detail=f"Unknown model: {model_name}")
    
    if model_name not in loaded_models:
        cfg = MODELS[model_name]
        print(f"Loading {model_name} → {cfg['path']}")
        
        loaded_models[model_name] = Llama(
            model_path=cfg["path"],
            n_gpu_layers=cfg["n_gpu_layers"],
            n_ctx=cfg["context"],
            verbose=False,
            chat_format=cfg["chat_format"]          # Now dynamic per model
        )
    return loaded_models[model_name]

SYSTEM_PROMPT = """You are a routing and safety classifier for OpenClaw.

Analyze the latest user message (and image if present).

Respond with EXACTLY one word:

- PRIMARY   → routine, simple, heartbeat, normal coding, non-sensitive
- ESCALATE  → sensitive/private information OR explicit NSFW content

Sensitive triggers:
- Passwords, API keys, credentials
- SSN, credit card, passport, ID, medical, financial, legal documents
- Clear explicit/sexual content in images

Only escalate when clearly warranted. Otherwise use PRIMARY.

Output ONLY the word PRIMARY or ESCALATE. No explanation."""

class ChatRequest(BaseModel):
    model: str
    messages: list
    temperature: float = 0.0
    max_tokens: int = 30

@app.post("/v1/chat/completions")
async def completions(request: ChatRequest):
    has_image = any(
        any(x in str(msg).lower() for x in ["data:image", "image_url", "base64", "[image]", "vision"])
        for msg in request.messages
    )

    router_model = "vision-router" if has_image else "router"
    print(f"→ Using {router_model} {'(image+text)' if has_image else ''}")

    llm = get_llm(router_model)

    messages = [
        {"role": "system", "content": SYSTEM_PROMPT},
        request.messages[-1]
    ]

    output = llm.create_chat_completion(
        messages=messages,
        temperature=0.0,
        max_tokens=30,
        stop=["\n", "Explanation", "Reasoning"]
    )

    raw = output["choices"][0]["message"]["content"].strip().upper()
    decision = "ESCALATE" if "ESCALATE" in raw else "PRIMARY"

    print(f"Router decision: {decision}")
    return {
        "id": f"router-{router_model}",
        "object": "chat.completion",
        "model": router_model,
        "choices": [{
            "index": 0,
            "message": {"role": "assistant", "content": decision},
            "finish_reason": "stop"
        }]
    }


if __name__ == "__main__":
    uvicorn.run(app, host=HOST, port=PORT)
```
* Change `API_KEY`  
* Change the path of both models to something real  
* `chat_format="chatml"` is hardcoded for both models.  
  * ChatML works for Qwen, but if someone swaps in a Llama-based GGUF, they'll get garbled output and wonder why the router keeps saying ESCALATE on everything. 
  * This assumes a ChatML-compatible model (Qwen2.5 etc.) — change chat_format if you swap models.  
* Currently, the best local vision model is `Qwen2.5-VL-7B GGUF`  

**<font size="4">How the Flow Actually Happens</font>**  

1\. A new message arrives in OpenClaw.  

2\. Because you enabled routing, OpenClaw first sends the message to your local router (the Python script running on port 4001).  
* It sends ONLY the latest user message (plus your system prompt that you defined in the router script).  
* OpenClaw does NOT send the full chat history, bootstrap messages, IDENTITY.md, MEMORY.md, tool results, or any previous conversation turns to the router.  
* The router call is deliberately lightweight and fast: it's designed to be a quick classification step before the real work begins.  

3\. Your router replies with only one word:  
  * `PRIMARY` - means “use the cheap/free model”  
  * `ESCALATE` - means “use the more powerful/expensive model”  

4\. OpenClaw reads that decision and automatically switches the model for that turn (and usually the next few turns in the same conversation thread).  
