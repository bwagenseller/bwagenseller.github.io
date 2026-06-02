# MCP Servers

<font color="purple">MCP</font> (<font color="purple">Model Context Protocol</font>) has become one of the more impactful open standards in the AI ecosystem since Anthropic open-sourced it back in November 2024. 

It's basically the "USB-C for AI"-a standardized, open protocol that lets large language models (and AI agents/apps built around them) connect securely and consistently to external data sources, tools, APIs, local files, databases, business systems, dev environments, and more. Instead of every AI vendor building custom integrations for every possible data source/tool (which creates massive fragmentation), MCP provides a universal client-server interface.

MCP turns fragmented, one-off tool integrations into a plug-and-play ecosystem: build an MCP server once for your data/tool, and it works across Claude, ChatGPT-compatible clients, Cursor, VS Code Copilot, and many other MCP-supporting frontends. 

# Quick Architecture Breakdown  

<font color="purple">MCP Host/Client</font> - the AI application (e.g. Claude Desktop, an IDE plugin, OpenClaw, a custom agent) that needs external context or actions.  

<font color="purple">MCP Server</font> - the thing you run/host that exposes your specific resources (files, database queries, APIs, calculators, custom workflows, etc.) via a standardized JSON-RPC interface (over HTTP or even local stdin/stdout for CLI tools).  
  * Servers advertise what they can do (tools/list)  
  * Clients call them (tools/call)  
  * Supports two-way communication, secure auth, streaming, etc.  


The protocol handles things like:  
* Reading files/context from repos or local disks  
* Calling functions/tools  
* Injecting prompt templates/workflows  
* Secure permission boundaries (very important for enterprise use)  


# Do You Even Need MCP?

Before reaching for MCP, consider that most autonomous AI systems (Claude Code, etc) can usually run any shell command or script directly via its Bash tool - no MCP required. For example, just ask:

```
Run ~/scripts/analyze.py and tell me what it outputs
```  

Many agents can execute it and read the results. For simple scripting tasks, that's all you need.

MCP is the right choice when you want to:
- **Reuse a tool across multiple AI apps**  
- **Expose structured inputs/outputs** with defined parameters, rather than raw shell output
- **Wrap something not easily shell-invokable** - an authenticated API, a persistent database connection, a long-running service

If you're just running a Python script and reading results, use Bash. If you're building something reusable or structured, use MCP.

# Why MCP?

Out of the box, Claude Code can read/write files, run shell commands, and search the web. MCP extends this to anything: query a database, post to Slack, interact with GitHub's API, control a browser, pull from a vector store - if you can write a server for it, Claude Code can use it.

# Finding MCP Servers

The MCP ecosystem is growing rapidly. Sources include:  
* [modelcontextprotocol.io](https://modelcontextprotocol.io) - official registry
* GitHub: search `mcp-server`
* there are some MCPs for specific agentic solutions:  
  * Anthropic's reference servers (filesystem, GitHub, Slack, Postgres, etc.)

---  

# MCP Primitives  

An MCP server can ship three different kinds of things - collectively called <font color="green">primitives</font>. The split matters because it tells you _who decides when each one fires_:  

| Primitive | What it is | Who decides when to invoke |
| --- | --- | --- |
| <font color="purple">Tool</font> | An action - a function the server runs on request | The **model** (Claude picks based on the conversation) |
| <font color="purple">Resource</font> | Data the client can read by URI (a file, a DB row, an API blob) | The **client/host** (UI attaches it, user picks from a list) |
| <font color="purple">Prompt</font> | A pre-defined prompt template the user can invoke | The **user** (explicitly picks it from a menu/slash command) |

This control-flow axis (model vs. host vs. user) is the cleanest way to keep them straight.  

## Tools

> Tools are, by far, the most common primitive.  

A <font color="purple">tool</font> is a function the server exposes to the model, advertised with a name, a description, and a JSON Schema for its inputs. When the conversation calls for it, the model picks a tool, fills in the arguments, and the server runs it.  

Examples:  
* `send_email(to, subject, body)` - call out to the Gmail API  
* `query_database(sql)` - run a SQL query and return rows  
* `write_file(path, contents)` - write a file to disk  
* `random_color()` - return a random color (the toy example used throughout this doc)  

A few things worth knowing:  

* _Tools are model-invoked_  
  * Claude reads the `tools/list` advertisement at session start and decides on its own when to call a tool, based on the conversation. You don't tell it "use this tool now" - it picks based on the user's request and the descriptions you wrote.  
* Tools can have _side effects_  
  * Unlike resources (which are read-only by design), a tool can send an email, post a message, write a file, charge a credit card. This is why most MCP clients - Claude Code included - prompt for confirmation before running a tool for the first time.  
* Tools advertise both an `inputSchema` (what arguments they accept) and an optional `outputSchema` (the shape of structured results).  
  * Both are derived automatically from your function's type annotations - covered in detail in the FastMCP sections below.  

Where to put a tool in your design: when the model needs to _do_ something - especially something parameterized, or something with side effects - it's a tool. If the client just needs to read static data, use a resource instead.  

## Resources  

A <font color="purple">resource</font> is data the client can read by URI. The server advertises a list of available URIs, and the client (or the user via the host UI) picks which ones to attach as context. Examples:  

* `file:///path/to/README.md` - return the contents of a file  
* `db://users/42` - return a row from a database  
* `github://repo/issues/123` - return an issue from a GitHub API  
* `log://today` - return today's log file  

A few things worth knowing:  

* Resources can be <font color="purple">templated</font> - the URI can have parameters. One resource definition like `file://logs/{date}` covers the whole family `file://logs/2026-05-07`, `file://logs/2026-05-08`, etc.  
* <font color="red">Resources are not model-invoked.</font> Claude doesn't decide to "read this resource" the way it decides to "call this tool." The host application (Claude Desktop, an IDE plugin) surfaces resources as attachable context, and the user or the host decides what to attach. Different control-flow than tools entirely.  
* Resources can support <font color="purple">subscriptions</font> - the client can subscribe to a URI and get notified when the underlying data changes (e.g. a tail of a log file, an inbox).  

When to use a resource vs. a tool: if the client just needs to _read_ something (no side effects, no parameters beyond what's encoded in the URI), it's a resource. If the model needs to _do_ something (with arguments, possibly with side effects), it's a tool.  

## Prompts  

A <font color="purple">prompt</font> is a pre-defined prompt template that the server ships and the user invokes. Yes - at first glance this is just "a saved prompt," and you can be forgiven for being underwhelmed. The protocol-level distinction from "text I copy-paste from a Notion page" is:  

1. <font color="purple">Server-authored, not user-authored</font> - prompts ship _with_ the MCP server. Install the server, you get the prompts. So a Jira MCP server can include `/jira-debug-ticket` that knows how to inflate "review this Jira ticket and the linked PRs..." with the right structure.  
2. <font color="purple">Parameterized</font> - like tools, prompts can take arguments. So `/debug-error` can take an error string and produce a prompt customized to that error.  
3. <font color="purple">Discoverable via the protocol</font> - the client lists them via `prompts/list` and surfaces them as slash commands or menu items. In Claude Code they appear as `/mcp__<server>__<prompt>`.  
4. <font color="purple">User-invoked</font> - distinct from tools (model picks) and resources (host attaches). The user explicitly picks the prompt from a menu.  

**Where prompts actually shine:**  
* Vendor ships workflow templates with their tool - e.g. a GitHub MCP server includes `/github-issue-triage`, `/github-pr-review-checklist`.  
* Org-wide saved prompts: a company writes one internal MCP server with `/postmortem-template`, `/code-review-checklist`; every employee who installs it gets the same standardized prompts that update centrally.  

**Where they're less compelling:**  
* Adoption is thin. Most public MCP servers ship tools and maybe resources; very few ship prompts.  
* For a personal/single-user setup, you can just keep prompts in a text file and copy-paste - the protocol overhead isn't worth it.  
* Client support varies. Claude Code surfaces prompts as slash commands; not every MCP client does.  

> Honest take: document them for completeness and know they exist, but if you never end up writing one of your own, you're in good company.  

---  

# Your Own MCP  

## Your Own MCP: FastMCP Installation  

> I use the [Conda Framework](learn_to_code/python/conda) in Python; you are not required to use this - you may skip all conda commands completely or use Python's virtual environment. That said, Conda is great - try it out!  

FastMCP is a Python package that you can use to get a quick MCP server up and running. FastMCP is _not_ the only way you can get an MCP server up and running - you can also do it in Python, TypeScript, C#, Java, Kotlin, Go, Ruby, Rust, Swift, and PHP. FastMCP is just the high-level Python convenience class; you can also use the lower-level Python SDK without it. Most community servers are written in TypeScript, but FastMCP is best for getting up and running quickly (and in the most popular language).  

1\. [Create](learn_to_code/python/conda?id=create-conda-env) the Conda environment: `conda create --name mcp python=3.13.11 numpy pandas pyarrow fastparquet`  
* Update the Python version to something closer to the current version.  
* You don't absolutely need `numpy pandas pyarrow fastparquet` - I include these as a base, but you do not have to do so.  

2\. [Activate](learn_to_code/python/conda?id=activate-conda-env) the Conda environment: `conda activate mcp`  

3\. Install the mcp package: `pip install mcp`  

Now you are up and running - or, at least, you can now run a MCP server. 

> This mcp install is actually client-agnostic; that is to say, Claude, Cursor, Codex CLI, OpenClaw, etc can use it!  

## FastMCP: Basic Tool Example  

Here is a basic example Python script that uses FastMCP.

```python  
import random

from mcp.server.fastmcp import FastMCP

# set up the server - name it (random-color), bind it to a host and a port  
mcp = FastMCP("random-color", host="192.168.1.100", port=8765)

COLORS = [
    "crimson", "teal", "marigold", "indigo", "chartreuse",
    "burnt sienna", "periwinkle", "ochre", "mauve", "vermilion",
]

WARM_COLORS= ["red", "orange", "yellow"]

COOL_COLORS= ["blue", "purple", "green"]

# @mcp.tool() is a decorator that registers the function with the FastMCP instance's tool registry
@mcp.tool()
def random_color() -> str:
    """Return a random color name from a curated list."""
    return random.choice(COLORS)

# note that you can set _what the agent sees as the function name_ (name), how its displayed to the human (title), and the description (no docstring needed)
@mcp.tool(name="random_warm_color", title="Random Warm Color Picker", description="Return a random warm color name from a curated list.")
def random_warm_color() -> str:
    return random.choice(WARM_COLORS)

@mcp.tool()
def random_cool_color() -> str:
    """Return a random cool color name from a curated list."""
    return random.choice(COOL_COLORS)

if __name__ == "__main__":
    mcp.run(transport="streamable-http")
```  

A few critical notes:  
* <font color="red">Do not forget to supply your own IP and port</font>!  
* `@mcp.tool()` is a decorator that registers the function with the FastMCP instance's tool registry  
  * Multiple functions can be defined and can be called separately (i.e. multiple `@mcp.tool()`)  
* The function name and docstring are critical!  
  * The function name (i.e. `random_color` or `random_warm_color`) along with the docstring in the function (i.e. `"""Return a random color name from a curated list."""`) are read by the agent when the agent comes online.  
  * The agent _will_ use these to determine if the mcp server should be queried - so name them properly and be descriptive in the docstring!  


**<font size="4">mcp.tool parameters</font>**  

> Multiple functions can be defined and can be called separately (i.e. multiple `@mcp.tool()`)  

`@mcp.tool()` is a decorator that registers the function with the FastMCP instance's tool registry. It can take a few parameters (without them, the defaults are taken):  
* `name` - what the agent thinks this function is called; if this is left out, the actual function name is used  
  * You would usually never set this, but there are times when you may want to  
    * Python identifier rules don't match the name you want. Python identifiers can't have hyphens, can't start with a digit, can't contain dots; if you want to use these you need to explicitly use the `name`  
    * Refactoring without breaking the public interface.  
      * Tool names are part of your server's contract - clients (or models, in their training/prompting) may reference them by name; you may want to change the function name but not change its publicly facing name. 
    * Decoupling internal code organization from the tool surface.  
      * Sometimes the Python-side name reads better with extra context that's redundant on the tool side.  
      * Example: in a file full of color helpers, you might name the function `get_random_color_from_curated_list` for clarity in code review, but expose it as just `random_color` to the model  
    * Avoiding collisions with Python builtins or keywords. 
      * If the most natural tool name is `list`, `type`, `filter`, or `print`, you can't use it as a Python function name without shadowing the builtin.   
* `title` - The title of this function as displayed to humans  
  * The default is simply the function name  
  * It's a good idea to make this readable  
* `description` - A description of what this function does.  
  * The default is the docstring - <font color="red">you need this _or_ a docstring</font>!  
* `structured_output` - Auto-detect from the function's return type annotation; should the output be structured in JSON?  
  * Can be `True`, `False`, or `None`  
    * `True` - When auto-detect gives up (return annotation is missing or too vague) but you know the function returns structured data.  
      * Pair it with a proper return-type annotation so FastMCP can build the schema.  
    * `False` - When auto-detect would make the output structured but you want plain-text output instead. 
      * Real-world reasons:  
        * Client compatibility - some MCP clients haven't implemented structured-output support yet and ignore the schema.  
        * Free-form prose responses - if your tool returns paragraphs of generated text where forcing it into `{"result": "..."}` is just noise.  
        * Backward compatibility - you previously shipped an unstructured tool and don't want to break clients that parsed its raw text.  
  * Leaving `None` means auto-detect this from the function's return type annotation (in our case, the `-> str`)  
    * In most cases, its good to leave in the type annotation.  
    * This will struggle with bare `dict`s, as FastMCP can't introspect what keys or value types you intend  
      * i.e. instead of `-> dict` say `-> dict[str, str | int]`  
      * Moral of the story: <font color="red">use type annotations</font>!  


**<font size="4">transport</font>**  

There are 3 options for transport: `stdio`, `streamable-http`, or `sse`. TL;DR we do not use `sse`, but on the other two:  
* `stdio`  
  * The default  
  * Server reads JSON-RPC from stdin and writes to stdout.
  * The client (Claude Code, Codex, OpenClaw, etc) launches the server as a subprocess and pipes to it. 
    * Lifecycle is coupled - when the client starts, the server starts; when the client exits, the server dies.
  * Registered in .mcp.json like: `{ "type": "stdio", "command": "/path/to/python", "args": ["mcp_random_color.py"] }`  
  * Pick this when: the server is a personal helper that only you use from the client, has no external dependencies, and there's no reason for it to outlive the session.  
    * <font color="red">Please note</font> this can be deceptive  
      * `npx` calls to Telegram, Slack, Discord, etc use `stdio`, because, technically, its a _local_ call to npx - where it goes from there is up to npx!  
* `streamable-http`  
  * Server is a long-lived HTTP daemon. Clients POST JSON-RPC and get streamed responses back.  
  * Decoupled lifecycle: server runs independently, multiple clients can connect, you can put it on the network, restart the client without restarting the server.  
  * Registered in .mcp.json like: `{ "type": "http", "url": "http://192.168.1.100:8765/mcp" }`  
  * Pick this when: you want a daemon (multiple clients, LAN access, separate deploy lifecycle, easier debugging since you can curl it).  

> Do not forget: you will have to run your python script with something like `python your_python_script_here.py` and leave it running in a separate window indefinitely (or, as long as you want the server available).  

### Basic Tool Example: Testing MCP  

To test your MCP server, run it, and then run this `curl` command in a different window - _make sure to switch out the IP and port for your own_:  
```bash
curl -s -N \
  -H "Accept: application/json, text/event-stream" \
  -H "Content-Type: application/json" \
  -X POST http://192.168.1.100:8765/mcp \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-06-18","capabilities":{},"clientInfo":{"name":"curl","version":"0"}}}'
```  


Or, better yet - another Python script (courtesy of Grok). This will perform all functions that an agent will perform and then actually test the functions (just _remember to switch out the IP and port as necessary_):  

```python  
import json
import requests

class MCPClient:
    def __init__(self, base_url: str = "http://192.168.1.100:8765", debug: bool = True):
        self.base_url = base_url.rstrip("/")
        self.session = requests.Session()
        self.session.headers.update({
            "Content-Type": "application/json",
            "Accept": "application/json, text/event-stream"
        })
        self.session_id = None
        self.debug = debug

    def send(self, method: str, params: dict = None, id: int = 1):
        payload = {
            "jsonrpc": "2.0",
            "id": id,
            "method": method,
        }
        if params is not None:
            payload["params"] = params

        # === SHOW EXACT JSON BEING SENT ===
        if self.debug:
            print(f"\n→ SENDING to /{method}")
            print(json.dumps(payload, indent=2))

        headers = {}
        if self.session_id:
            headers["Mcp-Session-Id"] = self.session_id

        response = self.session.post(
            f"{self.base_url}/mcp", 
            json=payload,
            headers=headers
        )

        if "mcp-session-id" in response.headers:
            self.session_id = response.headers["mcp-session-id"]
            if self.debug:
                print(f"📌 Session ID: {self.session_id}")

        if response.status_code != 200:
            print(f"❌ HTTP Error {response.status_code}")
            print(response.text)
            return None

        raw_text = response.text.strip()

        # === SHOW RAW SSE RESPONSE ===
        if self.debug:
            print(f"\n← RAW RESPONSE from server:")
            print(raw_text)
            print("-" * 60)

        # Parse the data
        if raw_text.startswith("event:"):
            for line in raw_text.splitlines():
                if line.startswith("data:"):
                    try:
                        return json.loads(line[5:].strip())
                    except json.JSONDecodeError:
                        return None
        else:
            try:
                return response.json()
            except:
                return raw_text

    def initialize(self):
        print("🔄 Initializing session...")
        params = {
            "protocolVersion": "2025-06-18",
            "capabilities": {},
            "clientInfo": {"name": "TestClient", "version": "1.0"}
        }
        return self.send("initialize", params, id=1)

    def list_tools(self):
        print("\n📋 Listing tools...")
        return self.send("tools/list", id=2)

    def call_tool(self, tool_name: str, arguments: dict = None):
        print(f"\n🎯 Calling tool: {tool_name}")
        params = {"name": tool_name}
        if arguments:
            params["arguments"] = arguments
        return self.send("tools/call", params, id=3)


# ====================== Usage ======================
if __name__ == "__main__":
    client = MCPClient(debug=True)   # Set debug=False to clean up output

    init_result = client.initialize()
    tools_result = client.list_tools()

    color_result = client.call_tool("random_color")
    warm_result = client.call_tool("random_warm_color")
```  

### Basic Tool Example: JSON Output



```
🔄 Initializing session...

→ SENDING to /initialize
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "initialize",
  "params": {
    "protocolVersion": "2025-06-18",
    "capabilities": {},
    "clientInfo": {
      "name": "TestClient",
      "version": "1.0"
    }
  }
}
📌 Session ID: 99ee009cae6b4de8ae7589fdd1290723

← RAW RESPONSE from server:
event: message
data: {"jsonrpc":"2.0","id":1,"result":{"protocolVersion":"2025-06-18","capabilities":{"experimental":{},"prompts":{"listChanged":false},"resources":{"subscribe":false,"listChanged":false},"tools":{"listChanged":false}},"serverInfo":{"name":"random-color","version":"1.27.0"}}}
------------------------------------------------------------

📋 Listing tools...

→ SENDING to /tools/list
{
  "jsonrpc": "2.0",
  "id": 2,
  "method": "tools/list"
}
📌 Session ID: 99ee009cae6b4de8ae7589fdd1290723

← RAW RESPONSE from server:
event: message
data: {"jsonrpc":"2.0","id":2,"result":{"tools":[
{"name":"random_color","description":"Return a random color name from a curated list.","inputSchema":{"properties":{},"title":"random_colorArguments","type":"object"},"outputSchema":{"properties":{"result":{"title":"Result","type":"string"}},"required":["result"],"title":"random_colorOutput","type":"object"}},
{"name":"random_warm_color","title":"Random Warn Color Picker","description":"Return a random warm color name from a curated list.","inputSchema":{"properties":{},"title":"random_warm_colorArguments","type":"object"},"outputSchema":{"properties":{"result":{"title":"Result","type":"string"}},"required":["result"],"title":"random_warm_colorOutput","type":"object"}},
{"name":"random_cool_color","description":"Return a random cool color name from a curated list.","inputSchema":{"properties":{},"title":"random_cool_colorArguments","type":"object"},"outputSchema":{"properties":{"result":{"title":"Result","type":"string"}},"required":["result"],"title":"random_cool_colorOutput","type":"object"}}
]}}
------------------------------------------------------------

🎯 Calling tool: random_color

→ SENDING to /tools/call
{
  "jsonrpc": "2.0",
  "id": 3,
  "method": "tools/call",
  "params": {
    "name": "random_color"
  }
}
📌 Session ID: 99ee009cae6b4de8ae7589fdd1290723

← RAW RESPONSE from server:
event: message
data: {"jsonrpc":"2.0","id":3,"result":{"content":[{"type":"text","text":"crimson"}],"structuredContent":{"result":"crimson"},"isError":false}}
------------------------------------------------------------

🎯 Calling tool: random_warm_color

→ SENDING to /tools/call
{
  "jsonrpc": "2.0",
  "id": 3,
  "method": "tools/call",
  "params": {
    "name": "random_warm_color"
  }
}
📌 Session ID: 99ee009cae6b4de8ae7589fdd1290723

← RAW RESPONSE from server:
event: message
data: {"jsonrpc":"2.0","id":3,"result":{"content":[{"type":"text","text":"yellow"}],"structuredContent":{"result":"yellow"},"isError":false}}
------------------------------------------------------------
```  

Some notes:  
* These are, basically, the raw requests/responses  
* The `SENDING to /tools/call` etc etc is a bit of a misnomer - all endpoints use `/mcp` - stuff like `/tools/call` are simply set as the 'method'  
* The `Initialize` and `Listing Tools` are done by the agent when you spin up the agent.  


## Advanced Tool: Arguments  

The example tools above all take zero arguments - but most real tools take inputs. FastMCP turns your function signature into a <font color="purple">JSON Schema</font> (the `inputSchema` you saw in the test output), and the agent uses that schema to decide what to pass.  

The basics:  
* Each parameter becomes a property in `inputSchema`  
* The Python type hint becomes the JSON Schema type (`str` -> `"string"`, `int` -> `"integer"`, etc.)  
* Parameters _without_ a default are added to `required`; parameters _with_ a default are optional  
* The default value flows through to the schema as `"default": ...`  

Simple example:  
```python
@mcp.tool()
def get_weather(city: str, unit: str = "celsius") -> dict[str, str]:
    """Return the current weather for a city."""
    return {"city": city, "temp": "20", "unit": unit}
```  

Generates this `inputSchema`:  
```json
{
  "properties": {
    "city": {"title": "City", "type": "string"},
    "unit": {"default": "celsius", "title": "Unit", "type": "string"}
  },
  "required": ["city"],
  "type": "object"
}
```  

`city` is required; `unit` is optional and defaults to `"celsius"`.  

**<font size="4">Tool Arguments: Constrained Choices With `Literal`</font>**  

If a parameter only accepts a fixed set of values, use `typing.Literal` - FastMCP turns it into a JSON Schema `enum`, which means the agent _knows_ the only valid values:  

```python
from typing import Literal

@mcp.tool()
def random_color_from(palette: Literal["warm", "cool", "all"] = "all") -> str:
    """Return a random color from the chosen palette."""
    ...
```  

Becomes:  
```json
"palette": {
  "default": "all",
  "enum": ["warm", "cool", "all"],
  "type": "string"
}
```  

This is much better than `palette: str` with a "must be 'warm', 'cool', or 'all'" comment in the docstring - the model sees the constraint structurally, can't pass a typo, and most clients render enums as a dropdown.  

**<font size="4">Tool Arguments: Descriptions and Validation</font>**  

For anything beyond a primitive type, use `typing.Annotated` paired with Pydantic's `Field`. This lets you attach a per-parameter description (which the agent sees and uses to decide what to pass) plus validation constraints:  

```python
from typing import Annotated
from pydantic import Field

@mcp.tool()
def search_emails(
    query: Annotated[str, Field(description="The search query (Gmail syntax)")],
    max_results: Annotated[int, Field(description="Cap on results", ge=1, le=100)] = 10,
) -> list[str]:
    """Search Gmail messages."""
    ...
```  

Becomes:  
```json
"query": {
  "description": "The search query (Gmail syntax)",
  "type": "string"
},
"max_results": {
  "default": 10,
  "description": "Cap on results",
  "minimum": 1,
  "maximum": 100,
  "type": "integer"
}
```  

A few useful `Field` constraints:  
* `description="..."` - per-parameter description shown to the agent  
* `ge=`, `le=`, `gt=`, `lt=` - numeric bounds  
* `min_length=`, `max_length=` - string/list length bounds  
* `pattern="..."` - regex constraint for strings  

> The docstring describes the _function as a whole_; `Field(description=...)` describes a _single parameter_. The agent reads both - use the docstring to say _what the tool does_ and `Field` descriptions to say _what each input means_.  

**<font size="4">Tool Arguments: What Types Work</font>**  

FastMCP supports anything Pydantic can introspect:  
* Primitives: `str`, `int`, `float`, `bool`  
* `Literal[...]` -> enum  
* `list[T]`, `dict[K, V]`, `tuple[...]`  
* `Optional[T]` / `T | None` -> nullable  
* Pydantic `BaseModel` subclasses -> nested object schema  
* `TypedDict` and `@dataclass` -> also supported  

> <font color="red">Avoid bare `dict` or `list`</font> as parameter types - same problem as bare `dict` returns: FastMCP can't introspect what's inside, so the schema is unhelpfully vague and the agent has no idea what shape to pass.  

### Advanced Tool Working Example  

Here's an example tool that combines everything above - `Literal` enum, `Annotated` + `Field` descriptions, numeric constraints, defaults, and a structured return type. It drops into a server like the `random-color` example earlier:  

```python
from typing import Annotated, Literal
from pydantic import Field
import random
from mcp.server.fastmcp import FastMCP

# set up the server - name it (random-animal), bind it to a host and a port  
mcp = FastMCP("random-animal", host="192.168.1.100", port=8764)

MAMMAL = ["cow", "cat", "dog", "horse", "bear", "mouse", "elephant", "squirrel"]
REPTILE = ["snake", "lizard", "turtle"]
ALL_ = MAMMAL + REPTILE

@mcp.tool()
def random_animal(
    kingdom: Annotated[
        Literal["mammal", "reptile", "all"],
        Field(description="Which animal kingdom to draw from")
    ] = "all",
    n: Annotated[int, Field(description="How many animals to return", ge=1, le=10)] = 1,
) -> list[str]:
    """Return N random animals from the chosen kingdom."""
    pool = {"mammal": MAMMAL, "reptile": REPTILE, "all": ALL_}[kingdom]
    return random.sample(pool, min(n, len(pool)))

if __name__ == "__main__":
    mcp.run(transport="streamable-http")
```  

FastMCP introspects this and advertises the following `inputSchema` in `tools/list` - this is what the agent reads to decide what to send:  

```json
{
  "properties": {
    "palette": {
      "default": "all",
      "description": "Which palette to draw from",
      "enum": ["warm", "cool", "all"],
      "type": "string"
    },
    "n": {
      "default": 1,
      "description": "How many colors to return",
      "maximum": 10,
      "minimum": 1,
      "type": "integer"
    }
  },
  "type": "object"
}
```  

Notice there's no `"required"` array - both arguments have defaults, so both are optional. Notice `palette` is an `enum` (the agent _knows_ the only valid values), and `n` carries `minimum`/`maximum` enforced by the schema.  

When the user says _"give me 3 warm colors"_, the agent picks the tool and sends this `tools/call` over JSON-RPC:  

```json
{
  "jsonrpc": "2.0",
  "id": 7,
  "method": "tools/call",
  "params": {
    "name": "random_colors",
    "arguments": {
      "palette": "warm",
      "n": 3
    }
  }
}
```  

The `arguments` object is a direct mapping from your Python parameter names to the values the agent chose. The server runs the function and returns:  

```json
{
  "content": [{"type": "text", "text": "['orange', 'red', 'yellow']"}],
  "structuredContent": {"result": ["orange", "red", "yellow"]},
  "isError": false
}
```  

`content` is the human-readable text version. `structuredContent` is the JSON-Schema-validated structured form (because `-> list[str]` is introspectable, the same auto-detect mechanism described under [`structured_output`](#mcptool-parameters)).  

> <font color="purple">Bad input is rejected before your function runs.</font> If the agent sent `{"palette": "lukewarm", "n": 50}`, FastMCP would reject the call: `"lukewarm"` fails the enum check, and `n: 50` violates `maximum: 10`. You don't need to defensively validate inside the function - Pydantic does it for you, and the agent receives a structured error it can read and self-correct from.  

---  

## FastMCP: Resources  

The second primitive - <font color="purple">resources</font> - exposes data by URI. The server defines URIs (or URI templates), and the host fetches them on demand, attaching the contents as context. There are two flavors: <font color="purple">static</font> (a fixed URI) and <font color="purple">templated</font> (a parameterized URI that covers a whole family of related URIs).  

> Reminder: resources are <font color="red">not model-invoked</font>. The model can't decide on its own to read a resource — the host application has to attach it. This is fundamentally different from tools.  

### A Static Resource  

The simplest case: a fixed URI that returns a fixed thing.  

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("color-info", host="192.168.1.100", port=8765)

@mcp.resource("colors://about")
def about_colors() -> str:
    """A description of this server."""
    return "A toy MCP server that returns information about color palettes."
```  

A few things going on here:  
* The URI is `colors://about` - URIs in MCP are arbitrary strings; you pick the scheme. Common conventions are `file://`, `db://`, `github://`, but you can invent your own.  
* `@mcp.resource(...)` takes the URI as its first positional argument.  
* The function name (`about_colors`) becomes the resource's `name`; the docstring becomes its `description`.  
* The return type drives the `mimeType` - returning `str` defaults to `text/plain`. Returning `bytes` would be `application/octet-stream`.  

When the client calls `resources/list`, FastMCP advertises:  

```json
{
  "uri": "colors://about",
  "name": "about_colors",
  "description": "A description of this server.",
  "mimeType": "text/plain"
}
```  

When the host reads the resource, the wire form is:  

```json
{
  "jsonrpc": "2.0",
  "id": 5,
  "method": "resources/read",
  "params": { "uri": "colors://about" }
}
```  

And the server responds:  

```json
{
  "contents": [
    {
      "uri": "colors://about",
      "mimeType": "text/plain",
      "text": "A toy MCP server that returns information about color palettes."
    }
  ]
}
```  

### A Templated Resource  

A static URI works for a fixed thing, but most real resources are <font color="purple">parameterized</font> — "the log file for date X", "the user record for ID Y", "the palette named Z". Templated resources let one definition cover a whole family. As an example, a resource that returns `n` last lines from a log:  

```python  
@mcp.resource("log://tail/{n}")                                                                                                       
def tail_lines(n: str) -> str:                                                                                                        
    """The last N lines of /var/log/app.log."""
    with open("/var/log/app.log") as f:                                                                                               
        return "".join(deque(f, maxlen=int(n))) 
```  

Or, another example - returning colors in a named palette:

```python
@mcp.resource("colors://palette/{name}")
def palette_info(name: str) -> str:
    """Return the colors in a named palette."""
    palettes = {
        "warm": "red, orange, yellow",
        "cool": "blue, purple, green",
        "all":  "red, orange, yellow, blue, purple, green",
    }
    return palettes.get(name, f"unknown palette: {name}")
```  

Two rules to keep in mind:  

1. Each `{...}` in the URI must match a parameter in the function signature - here `{name}` ↔ `name: str`. If they don't match, FastMCP raises at registration time.  
2. <font color="red">Templated resources do _not_ appear in `resources/list`</font> - they appear in a separate `resources/templates/list` call. This is intentional: a template can match infinitely many URIs, so the protocol separates "things the host can browse" (the static list) from "URI shapes the host can construct" (the template list).  

`resources/templates/list` advertises:  

```json
{
  "uriTemplate": "colors://palette/{name}",
  "name": "palette_info",
  "description": "Return the colors in a named palette."
}
```  

The host reads a concrete URI the same way as a static resource - the templating is invisible at read time:  

```json
{
  "jsonrpc": "2.0",
  "id": 6,
  "method": "resources/read",
  "params": { "uri": "colors://palette/warm" }
}
```  

→  

```json
{
  "contents": [
    {
      "uri": "colors://palette/warm",
      "mimeType": "text/plain",
      "text": "red, orange, yellow"
    }
  ]
}
```  

### A Binary Resource (Image)  

Resources aren't limited to text — they can return any bytes (images, PDFs, audio, anything). Multimodal MCP clients like Claude Code will decode the bytes and feed them to the model as a vision/audio input, so a resource is also how you hand a model a real image to look at.  

Here's a complete, working round-trip using a JPEG.  

**The server.** The function reads the file in binary mode and returns the raw `bytes`; the `mime_type=` on the decorator tells MCP what's inside:  

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("wizard-demo", host="192.168.1.100", port=8763)

@mcp.resource("img://wizard", mime_type="image/jpeg")
def wizard() -> bytes:
    return open("/path/to/wizard.jpg", "rb").read()

if __name__ == "__main__":
    mcp.run(transport="streamable-http")
```  

> <font color="red">Don't forget to swap in your own IP, port, and file path</font> before running.  

**The client.** This mirrors the test client in [Basic Tool Example: Testing MCP](#basic-tool-example-testing-mcp) but uses `resources/list` and `resources/read` instead of `tools/call`. It fetches the image, decodes the base64 blob, saves it to disk, and verifies the round-trip with a SHA-256 comparison:  

```python
import base64
import hashlib
import json
import requests


class MCPClient:
    def __init__(self, base_url: str = "http://192.168.1.100:8765"):
        self.base_url = base_url.rstrip("/")
        self.session = requests.Session()
        self.session.headers.update({
            "Content-Type": "application/json",
            "Accept": "application/json, text/event-stream",
        })
        self.session_id = None

    def send(self, method: str, params: dict | None = None, id: int = 1):
        payload = {"jsonrpc": "2.0", "id": id, "method": method}
        if params is not None:
            payload["params"] = params

        headers = {}
        if self.session_id:
            headers["Mcp-Session-Id"] = self.session_id

        response = self.session.post(f"{self.base_url}/mcp", json=payload, headers=headers)
        if "mcp-session-id" in response.headers:
            self.session_id = response.headers["mcp-session-id"]

        # SSE response: pull out the data: line
        raw = response.text.strip()
        if raw.startswith("event:"):
            for line in raw.splitlines():
                if line.startswith("data:"):
                    return json.loads(line[5:].strip())
        return response.json()

    def initialize(self):
        return self.send("initialize", {
            "protocolVersion": "2025-06-18",
            "capabilities": {},
            "clientInfo": {"name": "WizardClient", "version": "1.0"},
        }, id=1)

    def list_resources(self):
        return self.send("resources/list", id=2)

    def read_resource(self, uri: str):
        return self.send("resources/read", {"uri": uri}, id=3)


if __name__ == "__main__":
    client = MCPClient()
    client.initialize()

    # 1. See what's advertised
    listing = client.list_resources()
    print("Resources advertised:")
    print(json.dumps(listing["result"], indent=2))

    # 2. Read the image
    read_result = client.read_resource("img://wizard")
    contents = read_result["result"]["contents"][0]
    print(f"\nReceived: uri={contents['uri']}  mime={contents['mimeType']}")
    print(f"Base64 blob length: {len(contents['blob']):,} chars")

    # 3. Decode the base64 blob back to raw bytes and save it
    image_bytes = base64.b64decode(contents["blob"])
    out_path = "/tmp/wizard_received.jpg"
    with open(out_path, "wb") as f:
        f.write(image_bytes)

    # 4. Prove the round-trip is byte-identical
    original = open("/path/to/wizard.jpg", "rb").read()
    print(f"\nOriginal:  {len(original):,} bytes  sha256={hashlib.sha256(original).hexdigest()[:16]}")
    print(f"Received:  {len(image_bytes):,} bytes  sha256={hashlib.sha256(image_bytes).hexdigest()[:16]}")
    print(f"Identical: {original == image_bytes}")
    print(f"Saved to:  {out_path}")
```  

**The output.** Running the client against the server produces:  

```
Resources advertised:
{
  "resources": [
    {
      "name": "wizard",
      "uri": "img://wizard",
      "description": "",
      "mimeType": "image/jpeg"
    }
  ]
}

Received: uri=img://wizard  mime=image/jpeg
Base64 blob length: 58,044 chars

Original:  43,531 bytes  sha256=1eb1575e3719bde6
Received:  43,531 bytes  sha256=1eb1575e3719bde6
Identical: True
Saved to:  /tmp/wizard_received.jpg
```  

A few things to take away:  

* The wire field is <font color="purple">`blob`</font>, not `text` - this is the binary path, distinct from the `text` field used by string-returning resources.  
* The base64 expansion is the standard 4/3 ratio (43,531 bytes → 58,044 base64 chars). The bytes themselves are unchanged - encoding is purely transport, not transformation.  
* Once decoded back to bytes on the client side, the SHA-256 matches the original byte-for-byte. <font color="purple">No fidelity loss anywhere in the round-trip.</font>  
* In a real session, you wouldn't typically write the bytes to disk - the host (Claude Code, Claude Desktop, etc.) decodes the blob automatically and feeds it to the model as a vision input. Saving it to disk here is just to prove the round-trip integrity.  

The same pattern works for any binary content — swap `mime_type` for `application/pdf`, `image/png`, `audio/wav`, etc., and the wire format is identical.  

### Host Attaching a Resource  

One subtlety worth flagging because it trips people up: in Claude Code, resources don't appear in your conversation automatically. The host has to decide to attach them. The common ways this happens:  

* The user explicitly attaches one in the UI (in some clients, an `@`-mention pattern)  
* The host auto-attaches based on context (e.g., an IDE plugin attaches the file you have open)  
* A tool returns a resource _link_, and the model asks the host to read it  

If you write a resource and Claude doesn't seem to "see" it, that's not a bug - the model genuinely doesn't have access until the host attaches it. This is the opposite of tools, where the model receives the full `tools/list` advertisement at session start and can call any tool whenever the conversation calls for it.  

> <font color="purple">Rule of thumb:</font> if the model needs to autonomously decide _when_ to fetch something, make it a tool (e.g. `get_palette(name)`). If the user or host should be in charge of attaching it, make it a resource. The same underlying function can often be exposed both ways - and for many internal servers, that's the right answer.  

> <font color="purple">Note for Claude Code specifically:</font> Claude Code ships two built-in host-side tools — `ListMcpResourcesTool` and `ReadMcpResourceTool` — that let the model request resource reads from any registered MCP server. So while resources are host-attached at the protocol level, in Claude Code the model can effectively grab them on its own initiative by calling these wrapper tools. Other MCP clients may or may not provide this convenience, so if you're writing a resource that needs to work everywhere, don't assume the model can self-trigger reads — design for the host-attached path and treat Claude Code's meta-tools as a bonus.  

> <font color="purple">Binary resources in Claude Code, in practice:</font> When Claude Code reads a binary resource (an image, PDF, etc.), it doesn't pipe the blob straight into the model's vision context the way an inline-attached image would be. Instead it writes the bytes to a file under `~/.claude/projects/<SESSION_ID>/tool-results/mcp-resource-<id>.<ext>` and returns a text payload describing the saved path. The model then runs a follow-up `Read` on that path to actually see the image. End result is the same — the model sees the bytes, with full multimodal capability — but the flow is `ReadMcpResourceTool → blob saved to disk → path returned to model → Read tool on path → vision input`. Other MCP clients may decode and inject the blob directly; this two-step path is Claude Code's specific behavior.  

---  

# Authentication  

So far every server in this doc has been wide open — anyone who can reach the port can call any tool. That is fine for `127.0.0.1`-only experiments, but the moment a server is reachable on the network (or, worse, the public internet), you need some way to keep strangers out.  

MCP auth is really three different concerns stacked on top of each other:  

| Layer | The question it answers | Who is involved |
|-------|-------------------------|-----------------|
| <font color="purple">Layer 1 — bearer token</font> | "Do you have the secret word? If so, come in." | Client ↔ MCP server |
| <font color="purple">Layer 2 — OAuth 2.1</font> | "Which specific user is connecting, and have they consented to let *this* client act on their behalf?" | User + Client + MCP server |
| <font color="purple">Layer 3 — server-side downstream auth</font> | "The MCP server itself needs to call Google/Slack/Discord on the user's behalf — where does *that* token live?" | MCP server ↔ Google/Slack/etc. |

Layer 1 doesn't know who you are — only that you hold a valid token. Layer 2 adds per-user identity, scoped permissions, and revocation. Layer 3 isn't really an MCP concern at all — it's the MCP server playing OAuth client to some other service — but it's where most "real" servers spend their auth complexity, so it's worth naming.  

This section covers <font color="purple">layer 1</font> in full, with a working example. Layers 2 and 3 are walked through conceptually further down.  

## Layer 1: Bearer Token  

Bearer-token auth is dumb and effective. The server holds a string; the client sends that string in the `Authorization` header on every request; the server compares them. No user identity, no expiration, no scopes — anyone holding the token is treated as authorized. "Bearer" is the OAuth term for exactly that semantic: <font color="purple">whoever bears the token is treated as the rightful holder, no questions asked.</font>  

It is the right tool when:  

* You host the server for yourself or a small trusted group  
* The server already sits behind a VPN or private network  
* You don't need to know *which* user is calling, only that the caller is allowed  

It is the wrong tool when you need per-user accounting, revocation without rotating everyone's token, or you're letting a third-party agent hit your server on a user's behalf. Those needs push you to layer 2.  

### The Three FastMCP Pieces  

FastMCP exposes three types you'll touch for layer 1:  

| Type | Role |
|------|------|
| <font color="purple">`TokenVerifier`</font> | A `Protocol` you implement. Has one async method, `verify_token(token: str) -> AccessToken \| None`. Return `None` to reject; return an `AccessToken` to accept. |
| <font color="purple">`AccessToken`</font> | A small Pydantic model describing the validated token. Required fields: `token`, `client_id`, `scopes`. Optional: `expires_at`, `resource`. |
| <font color="purple">`AuthSettings`</font> | Metadata FastMCP advertises at `/.well-known/oauth-protected-resource`. Even for "just check this string" auth, you must declare an `issuer_url` and `resource_server_url` because the framework still publishes the discovery document (this is how layer 1 and layer 2 share the same machinery). |

You pass two of them as kwargs when you build the `FastMCP` instance:  

```python
mcp = FastMCP(
    "auth-demo",
    token_verifier=StaticTokenVerifier(),
    auth=AuthSettings(
        issuer_url=AnyHttpUrl("http://127.0.0.1:8767"),
        resource_server_url=AnyHttpUrl("http://127.0.0.1:8767"),
    ),
)
```  

> <font color="purple">Note:</font> FastMCP enforces an XOR — you must pass **either** `token_verifier` (layer 1) **or** `auth_server_provider` (layer 2 — the full OAuth provider scaffolding), never both. And you must always pair whichever one you pass with `auth=AuthSettings(...)`. Trying to set just `token_verifier` without `auth=`, or vice versa, raises a `ValueError` at startup.  

### Layer 1 Worked Example: Server  

```python
import os

from mcp.server.fastmcp import FastMCP
from mcp.server.auth.provider import AccessToken, TokenVerifier
from mcp.server.auth.settings import AuthSettings
from pydantic import AnyHttpUrl


# the secret word - in real life this lives in a secret manager, env var, etc.
VALID_TOKEN = os.environ.get("MCP_TOKEN", "hunter2")


# implement the Protocol: one async method that says yes/no
class StaticTokenVerifier(TokenVerifier):
    async def verify_token(self, token: str) -> AccessToken | None:
        if token != VALID_TOKEN:
            return None  # any rejection - bad token, expired, revoked - is None
        return AccessToken(
            token=token,
            client_id="trusted-client",   # purely informational at layer 1
            scopes=["mcp"],               # ditto - tools won't actually check this
        )


mcp = FastMCP(
    "auth-demo",
    host="127.0.0.1",
    port=8767,
    token_verifier=StaticTokenVerifier(),
    auth=AuthSettings(
        # required by the constructor, but at layer 1 these are advertisement-only:
        # they get echoed into /.well-known/oauth-protected-resource and the
        # WWW-Authenticate header. The token verifier never reads them.
        issuer_url=AnyHttpUrl("http://127.0.0.1:8767"),
        resource_server_url=AnyHttpUrl("http://127.0.0.1:8767"),
    ),
)


@mcp.tool()
def hello(name: str) -> str:
    return f"Hello, {name}!"


if __name__ == "__main__":
    mcp.run(transport="streamable-http")
```  

That's the whole server. The `@mcp.tool()` decorator is unchanged from earlier in this doc — auth is enforced at the transport layer, not per-tool, so existing tools just work once auth is wired in.  

### Testing It with curl  

Spin the server up and hit it three ways:  

<font color="purple">No token at all:</font>  

```bash
curl -s -D - -X POST http://127.0.0.1:8767/mcp \
  -H "Accept: application/json, text/event-stream" \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{}}'
```  

```http
HTTP/1.1 401 Unauthorized
www-authenticate: Bearer error="invalid_token", error_description="Authentication required", resource_metadata="http://127.0.0.1:8767/.well-known/oauth-protected-resource"
content-type: application/json

{"error": "invalid_token", "error_description": "Authentication required"}
```  

That `www-authenticate` header is doing real work. It's the same header layer 2 uses to point a confused client at the OAuth discovery document — which means a layer-1 server still publishes a (mostly empty) protected-resource metadata doc as a side effect:  

```bash
curl -s http://127.0.0.1:8767/.well-known/oauth-protected-resource
```  

```json
{"resource":"http://127.0.0.1:8767/","authorization_servers":["http://127.0.0.1:8767/"],"bearer_methods_supported":["header"]}
```  

Layer 1 doesn't actually have an authorization server, but the discovery endpoint exists and points back at itself. That's just the cost of bolting onto the same `BearerAuthBackend` machinery layer 2 uses.  

<font color="purple">Wrong token:</font>  

```bash
curl -s -X POST http://127.0.0.1:8767/mcp \
  -H "Accept: application/json, text/event-stream" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer not-the-password" \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{}}'
```  

Same `401 invalid_token`. The server doesn't leak whether the token was malformed, expired, or just plain wrong — it just rejects.  

<font color="purple">Right token:</font>  

```bash
curl -s -X POST http://127.0.0.1:8767/mcp \
  -H "Accept: application/json, text/event-stream" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer hunter2" \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-06-18","capabilities":{},"clientInfo":{"name":"curl","version":"0"}}}'
```  

```
HTTP 200
event: message
data: {"jsonrpc":"2.0","id":1,"result":{"protocolVersion":"2025-06-18", ... ,"serverInfo":{"name":"auth-demo","version":"1.27.0"}}}
```  

The handshake completes and from here on out every request just needs the same `Authorization: Bearer hunter2` header attached.  

### Layer 1 Worked Example: Python Client  

A minimal client that demonstrates all three cases in one run:  

```python
import json
import os
import sys

import requests


def call(method: str, token: str | None, params: dict | None = None) -> tuple[int, dict | None]:
    headers = {
        "Accept": "application/json, text/event-stream",
        "Content-Type": "application/json",
    }
    if token is not None:
        headers["Authorization"] = f"Bearer {token}"

    payload = {"jsonrpc": "2.0", "id": 1, "method": method}
    if params is not None:
        payload["params"] = params

    r = requests.post("http://127.0.0.1:8767/mcp", json=payload, headers=headers)

    body: dict | None = None
    if r.status_code == 200:
        # streamable-http returns SSE-framed JSON; pull out the data: line
        for line in r.text.splitlines():
            if line.startswith("data:"):
                body = json.loads(line[5:].strip())
                break
    else:
        try:
            body = r.json()
        except Exception:
            body = {"raw": r.text}

    return r.status_code, body


if __name__ == "__main__":
    token = sys.argv[1] if len(sys.argv) > 1 else os.environ.get("MCP_TOKEN")

    init_params = {
        "protocolVersion": "2025-06-18",
        "capabilities": {},
        "clientInfo": {"name": "auth-client", "version": "1"},
    }

    print("--- no token ---")
    code, body = call("initialize", None, init_params)
    print(f"HTTP {code}: {body}")

    print("\n--- wrong token ---")
    code, body = call("initialize", "wrong-password", init_params)
    print(f"HTTP {code}: {body}")

    print(f"\n--- right token ({token!r}) ---")
    code, body = call("initialize", token, init_params)
    print(f"HTTP {code}: server={body['result']['serverInfo']['name']!r}")
```  

Running it against the server above:  

```
$ python auth_client.py hunter2
--- no token ---
HTTP 401: {'error': 'invalid_token', 'error_description': 'Authentication required'}

--- wrong token ---
HTTP 401: {'error': 'invalid_token', 'error_description': 'Authentication required'}

--- right token ('hunter2') ---
HTTP 200: server='auth-demo'
```  

### Practical Notes  

* <font color="purple">Where does the token live?</font> On the server, an env var or secret manager — never hardcoded. On the client, the same. For Claude Code specifically, you'd put it in the server's [`.mcp.json` config](agentic/claude_code/mcp) using a `headers` field on a streamable-HTTP server entry; Claude Code will attach it as `Authorization: Bearer <value>` automatically.  
* <font color="purple">Rotate by changing the env var on the server</font> and restarting. There's no per-token revocation list — the token *is* the access. This is fine for one or two clients; painful past that.  
* <font color="purple">A `TokenVerifier` can be smarter than string-equals.</font> Verify a JWT signature, look the token up in a database, check an expiration timestamp, return a different `AccessToken` per-token with different scopes — the Protocol just asks for "yes with details, or no." String-equals is the simplest possible implementation, not the only one. The instant your verifier starts looking up users in a database and returning a populated `client_id`, you've crossed into something closer to layer 2 in spirit, even if the wire format is still a static bearer.  
* <font color="purple">HTTPS, please.</font> Bearer tokens travel in the clear inside HTTP headers. Anyone watching the wire (or a misconfigured proxy logging headers) sees the token. For anything beyond `localhost`, terminate TLS at a reverse proxy and never hand a bearer over plain HTTP.  


