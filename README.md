# Maindex Plugin for OpenClaw

Persistent, relational memory for AI agents. Connect OpenClaw to [Maindex](https://maindex.io) — a structured knowledge graph with multi-tier search, typed associations, collections, and full revision history.

## What's Included

| Component | Description |
|---|---|
| **MCP Integration** | Native Streamable HTTP client connecting to the Maindex MCP server |
| **OAuth Authentication** | Browser-based login on first use; tokens stored and refreshed automatically |
| **Memory Conventions** | Always-on skill: best practices for tagging, canon status, memory kinds, and graph structure |
| **Tool Guidance** | Decision tree for choosing the right Maindex tool |
| **Memory Organizer** | Skill: audit your knowledge graph — find duplicates, create links, standardize tags, build collections |
| **The Archivist** | Proactive behavior: recalls context before answering, stores decisions, maintains your knowledge graph |

## Install

```bash
openclaw plugins install @maindex/openclaw-plugin
openclaw gateway restart
```

On first use, a browser window will open for you to log in to Maindex via OAuth. After authenticating, all Maindex tools are available to your agent automatically.

## Configuration

The plugin works out of the box with default settings. To customize, add to your `~/.openclaw/openclaw.json`:

```json5
{
  plugins: {
    entries: {
      "maindex": {
        enabled: true,
        config: {
          baseUrl: "https://maindex.io/mcp",  // default
          authMode: "oauth"                     // or "apiKey"
        }
      }
    }
  }
}
```

### API Key Authentication (Headless/SSH)

If you're running OpenClaw in an environment without a browser (SSH, Docker, CI), switch to API key auth:

1. Create an API key at [maindex.io/dashboard](https://maindex.io/dashboard/#settings)
2. Configure the plugin:

```json5
{
  plugins: {
    entries: {
      "maindex": {
        enabled: true,
        config: {
          authMode: "apiKey"
        },
        env: {
          MAINDEX_API_KEY: "your-api-key-here"
        }
      }
    }
  }
}
```

## Available Tools

Once connected, your agent has access to all Maindex tools (prefixed with `maindex_`):

- `maindex_remember`, `maindex_update_memory`, `maindex_forget_memory`, `maindex_supersede_memory` — manage memories
- `maindex_search_memories`, `maindex_recall_memories`, `maindex_recall_memory` — find knowledge
- `maindex_associate_memories`, `maindex_get_related_memories` — build and traverse the graph
- `maindex_manage_collection` — organize memories into projects
- `maindex_bulk_remember`, `maindex_bulk_update` — batch operations

## Skills

The plugin ships two skills that teach your agent how to use Maindex effectively:

- **maindex_core** — Memory conventions, tool selection guide, and archivist behavior (loaded automatically)
- **memory_organizer** — Step-by-step workflow for auditing and organizing your knowledge graph

## Links

- [Maindex](https://maindex.io) — Homepage
- [Dashboard](https://maindex.io/dashboard) — Manage API keys and settings
- [Setup Guide](https://maindex.io/help/) — Instructions for all platforms
