# Antigravity Workspace Template

A starter kit for building AI agents with Google Antigravity. Clone it, add your API key, and start prompting.

[English](/docs/en/) | [中文](README_CN.md) | [Español](/docs/es/)

![License](https://img.shields.io/badge/License-MIT-green)
![Gemini](https://img.shields.io/badge/AI-Gemini_2.0_Flash-blue)

## Why I built this

Every time I started a new project in Cursor or Antigravity, I'd waste an hour on the same decisions: folder structure, how to organize tools, where context files should go, how to handle memory. It's tedious.

So I made a template that handles all that upfront. The idea is simple: after `git clone`, your IDE already knows the architecture and the agent is ready to run.

## Getting Started

**Windows:**
```cmd
git clone https://github.com/study8677/antigravity-workspace-template.git my-project
cd my-project
install.bat
# Edit .env with your API keys
python src/agent.py
```

**Linux/macOS:**
```bash
git clone https://github.com/study8677/antigravity-workspace-template.git my-project
cd my-project
chmod +x install.sh && ./install.sh
nano .env  # Add your API keys
source venv/bin/activate
python src/agent.py
```

That's it. The agent runs and the IDE picks up the config from `.cursorrules` and `.antigravity/rules.md`.

## What's in the box

This isn't a framework or SDK—it's a project structure with working code you can modify.

**Memory system** — Three-tier architecture with hot/warm/cold storage. Recent messages stay verbatim, older ones get embedded in ChromaDB for semantic search, and ancient history gets summarized. There's also an entity graph that tracks relationships between concepts across conversations.

**Tool auto-discovery** — Drop a Python function in `src/tools/` and it's available to the agent. No registration needed.

**Context injection** — Files in `.context/` get loaded into prompts automatically. Good for project docs, coding standards, or anything the agent should always know.

**MCP integration** — Connect to external tools via Model Context Protocol. GitHub, Postgres, Brave Search, Puppeteer—just enable them in `mcp_servers.json`.

**Multi-agent swarm** — Decompose tasks across specialized agents (coder, reviewer, researcher) with a router that coordinates them.

## Project layout

```
src/
├── agent.py          # Main loop
├── memory.py         # Basic JSON memory
├── memory_rag.py     # RAG-enhanced memory with ChromaDB
├── memory_graph.py   # Entity relationship graph
├── mcp_client.py     # MCP server connections
├── swarm.py          # Multi-agent orchestration
├── agents/           # Specialist agents (coder, reviewer, etc.)
└── tools/            # Your custom tools go here

.context/             # Knowledge base files (auto-loaded)
.antigravity/         # IDE rules
artifacts/            # Agent outputs
mcp_servers.json      # MCP server config
```

## Adding a tool

Create a file in `src/tools/`:

```python
# src/tools/my_tool.py
def summarize_text(text: str, max_length: int = 100) -> str:
    """Returns a shortened version of the input text."""
    if len(text) <= max_length:
        return text
    return text[:max_length].rsplit(' ', 1)[0] + '...'
```

Restart the agent. Done.

## MCP servers

Edit `mcp_servers.json` to enable external tools:

```json
{
  "name": "github",
  "transport": "stdio",
  "command": "npx",
  "args": ["-y", "@modelcontextprotocol/server-github"],
  "enabled": true,
  "env": {
    "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
  }
}
```

Pre-configured options: filesystem, GitHub, Postgres, Brave Search, Puppeteer, Slack.

## Multi-agent mode

For complex tasks, the swarm splits work across agents:

```python
from src.swarm import SwarmOrchestrator

swarm = SwarmOrchestrator()
result = swarm.execute("Build a REST API with tests")
```

The router figures out which agents to involve and synthesizes their outputs.

## Memory

The default setup uses `RAGMemoryManager` which gives you:
- **Hot tier**: Last N messages, kept exactly as-is
- **Warm tier**: Older messages embedded in ChromaDB for semantic retrieval
- **Cold tier**: Compressed summaries of ancient history
- **Entity graph**: Tracks relationships ("User prefers TypeScript", "Project uses PostgreSQL")

You can query the graph for context:

```python
from src.memory_graph import EntityGraphMemory

memory = EntityGraphMemory()
relationships = memory.query_relationships("TypeScript")
```

## Docs

More detail in `/docs/en/`:
- [Quick Start](docs/en/QUICK_START.md)
- [Philosophy](docs/en/PHILOSOPHY.md)
- [Zero-Config](docs/en/ZERO_CONFIG.md)
- [MCP Integration](docs/en/MCP_INTEGRATION.md)
- [Swarm Protocol](docs/en/SWARM_PROTOCOL.md)
- [Roadmap](docs/en/ROADMAP.md)

## Contributing

Open an [issue](https://github.com/study8677/antigravity-workspace-template/issues) or submit a PR. Bug reports, feature ideas, and doc improvements all welcome.

## Contributors

- [@devalexanderdaza](https://github.com/devalexanderdaza) — First contributor. MCP integration, demo tools, Agent OS roadmap.
- [@Subham-KRLX](https://github.com/Subham-KRLX) — Dynamic tool loading, context injection, multi-agent protocol.

## License

MIT. See [LICENSE](LICENSE).
