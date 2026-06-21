# Building a simple MCP server with Python and SQLite

MCP. Model Context Protocol. I understand every single word separately, but put them together and I still do not know what it means. Do you?

I am not someone who can fully grasp a new concept without actually getting my hands dirty first. How about we build a simple MCP server together and see what it actually does.

This post builds on the sales database from the [previous post](/2026/05/21/building-a-simple-ai-agent-with-python-ollama-and-sqlite.html). If you do not have `sales.db` set up already, start there.

## Step 1 – Install required software

We need `fastmcp`, which handles the underlying plumbing of MCP so we do not have to.

```bash
python -m venv venv
source venv/bin/activate
pip install fastmcp
```

## Step 2 – Build the server

Save the script below as `server.py`:

```python
from fastmcp import FastMCP
import sqlite3

mcp = FastMCP("my-first-mcp-server")

@mcp.tool() # Declares this function as a tool accessible by agents.
def get_total_sales():
    # Remember this is the database from the previous post.
    conn = sqlite3.connect("sales.db")
    total_sales_amount = conn.execute("select sum(amount) from sales").fetchone()[0] or 0
    conn.close()
    return total_sales_amount

if __name__ == "__main__":
    # This is the address agents can reach us at.
    mcp.run(transport="http", host="127.0.0.1", port=8000)
```

## Step 3 – Build the client

Save the script below as `agent.py`:

```python
from fastmcp import Client
import asyncio

# Remember this is the address we defined for the MCP server.
client = Client("http://127.0.0.1:8000/mcp")

async def main():
    async with client:
        tools = await client.list_tools() # Remember we only exposed 1 tool.
        print(f"Available tools: {[t.name for t in tools]}")

        result = await client.call_tool("get_total_sales")
        print(f"Result: {''.join(a.text for a in result.content if a.type == 'text')}")

if __name__ == "__main__":
    asyncio.run(main())
```

## Step 4 – Run the test

In one terminal, start the server:

```bash
python server.py
```

In a second terminal, run the agent:

```bash
python agent.py
```

If everything works as expected, you should see the output below:
```bash
Connecting to MCP server...
Listing tools...
Available tools: ['get_total_sales']
Calling get_total_sales...
Result: 660.0
```

## Step 5 – Connect to Claude

The Python client above proves the server works. However, the real point of MCP is that any agent can use it and let's get Claude to talk to the MCP server. 

Register the server with Claude CLI with a single cmd:

```bash
claude mcp add --transport http my-first-mcp-server http://127.0.0.1:8000/mcp
```

The registration takes effect immediately and to verify, check inside `~/.claude.json`. It is a big file but I managed to find the section: 

```json
    "/Users/blah/my-first-mcp-server": {
      "allowedTools": [],
      "mcpContextUris": [],
      "mcpServers": {
        "my-first-mcp-server": {
          "type": "http",
          "url": "http://127.0.0.1:8000/mcp"
        }
      },
```

Now we can ask Claude directly:

```bash
❯ get total sales

⏺ Let me load the schema for the total sales tool before calling it.

  Called my-first-mcp-server (ctrl+o to expand)

⏺ Total sales: $660.00
```

Isn't that wonderful? It all seems to work magically with so little effort. An MVP server has a very close resemblance to a REST API but remember the distinction: REST APIs are designed for humans to call and MCP is designed for agents. 

I cannot claim to be an expert on MCP as I barely scratched the surface but doing the exercise helped to deepen my understanding and make me more curious and wanting to explore further.
