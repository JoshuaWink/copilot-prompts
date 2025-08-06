---
applyTo: '**.py'
---
# MCP Python SDK (mcp[cli]) Architecture & Command Cheatsheet

## Overview
A quick reference for the MCP Python SDK (`mcp[cli]`): server structure, decorators, commands, and usage patterns. Use this as a lookup for building, running, and debugging MCP servers and clients.

---

## Core Server Architecture

| Component         | Import Path                                 | Usage Example / Notes                                  |
|-------------------|---------------------------------------------|--------------------------------------------------------|
| Server Class      | `from mcp.server.fastmcp.server import FastMCP` | `server = FastMCP(name="My MCP Server")`              |
| Tool Decorator    | `@server.tool` (from instance)              | `@server.tool(name, description)`                      |
| Resource Decorator| `@server.resource` (from instance)          | `@server.resource(uri, name, description)`             |
| Prompt Decorator  | `@server.prompt` (from instance)            | `@server.prompt(name, description)`                    |
| Run Server        | `server.run(transport="stdio")`            | Options: `stdio`, `sse`, `streamable-http`             |

---

## Decorator Usage Patterns

| Decorator         | Example Syntax                                                                 |
|-------------------|-------------------------------------------------------------------------------|
| Tool              | `@server.tool(name="add", description="Add two numbers.")`                 |
| Resource          | `@server.resource(uri="file:///hello.txt", name="Hello File")`             |
| Prompt            | `@server.prompt(name="GreetUser", description="Say hello")`                |

---

## Common Server Commands

| Command / Action         | Example / Syntax                                      | Description                                  |
|-------------------------|-------------------------------------------------------|----------------------------------------------|
| Install SDK             | `pip install "mcp[cli]"`                             | Install the MCP Python SDK                   |
| Run Server (stdio)      | `python server.py`                                    | Start server in stdio mode                   |
| Run Server (http)       | `server.run(transport="streamable-http")`            | Start server with HTTP API                   |
| List Tools (Inspector)  | `tools/list` (JSON-RPC)                               | List all registered tools                    |
| List Resources          | `resources/list` (JSON-RPC)                           | List all registered resources                |
| List Prompts            | `prompts/list` (JSON-RPC)                             | List all registered prompt templates         |
| Call Tool (Client)      | `client.call_tool("add", {"a": 2, "b": 3})`      | Call a tool from the client                  |
| Read Resource (Client)  | `client.read_resource("file:///hello.txt")`          | Read a resource from the client              |
| Render Prompt (Client)  | `client.render_prompt_template("GreetUser", {...})`  | Render a prompt template from the client     |

---

## Inspector & Debugging

| Tool / Command          | Description                                           |
|------------------------|------------------------------------------------------|
| MCP Inspector          | [Inspector Tool](https://modelcontextprotocol.io/inspector/) |
| JSON-RPC Traffic       | View raw requests/responses for debugging             |
| Error Handling         | Inspector shows error details and schemas             |
| Introspection          | Use Inspector to auto-discover tools/resources/prompts|

---

## Client Usage & Commands

| Action                      | Example / Syntax                                                      | Description                                  |
|-----------------------------|-----------------------------------------------------------------------|----------------------------------------------|
| Import Client               | `from mcp.client import MCPClient`                                    | Import the MCP Python client                 |
| Create Client (stdio)       | `client = MCPClient(transport="stdio", command=["python", "server.py"])` | Connect to a stdio server                    |
| Call Tool                   | `client.call_tool("add", {"a": 2, "b": 3})`                      | Call a tool by name with arguments           |
| Read Resource               | `client.read_resource("file:///hello.txt")`                          | Read a resource by URI                       |
| Render Prompt Template      | `client.render_prompt_template("GreetUser", {"user": "Alice"})`   | Render a prompt template with arguments      |
| List Tools                  | `client.list_tools()`                                                 | List all available tools                     |
| List Resources              | `client.list_resources()`                                             | List all available resources                 |
| List Prompts                | `client.list_prompts()`                                               | List all available prompt templates          |

### Minimal Client Example

```python
from mcp.client import MCPClient

client = MCPClient(transport="stdio", command=["python", "server.py"])

# Call a tool
result = client.call_tool("add", {"a": 2, "b": 3})
print("Add result:", result)

# Read a resource
resource = client.read_resource("file:///hello.txt")
print("Resource:", resource)

# Render a prompt template
prompt = client.render_prompt_template("GreetUser", {"user": "Alice"})
print("Prompt:", prompt)
```

---

## Best Practices
- Use only instance decorators (`@server.tool`, `@server.resource`, `@server.prompt`).
- Write clear descriptions and argument names for LLM UX.
- Test every change in MCP Inspector first.
- Use absolute URIs for resources.
- For async operations: use `async def` with SDK’s async support.
- Use prompt templates for any LLM/agent workflow you want to expose.

---

## References
- [MCP Python SDK Docs](https://modelcontextprotocol.io/docs/python-sdk/)
- [Inspector Tool](https://modelcontextprotocol.io/inspector/)
- [Official Spec](https://modelcontextprotocol.io/specification)
