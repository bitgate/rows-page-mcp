# rows.page MCP server

[rows.page](https://rows.page) is a place for your rows. Your agent pushes CSV, TSV, JSON, JSONL or Parquet with one tool call and gets back a link a human can explore: filter, chart columns, run SQL, read pinned findings, and see what changed since the last push. Viewing is free, no signup needed. Anonymous datasets live 7 days unless someone keeps them.

The server is remote only: `https://rows.page/mcp`, streamable HTTP, stateless JSON-RPC. There is nothing to install. A `GET` on the endpoint returns the server card.

## Connect

**Claude Code**

```sh
claude mcp add --transport http rows https://rows.page/mcp
```

**Claude Desktop** (`claude_desktop_config.json`, needs Node.js)

```json
{
  "mcpServers": {
    "rows": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://rows.page/mcp"]
    }
  }
}
```

**Cursor** (`.cursor/mcp.json`)

```json
{
  "mcpServers": {
    "rows": {
      "url": "https://rows.page/mcp"
    }
  }
}
```

**VS Code** (`.vscode/mcp.json`)

```json
{
  "servers": {
    "rows": {
      "type": "http",
      "url": "https://rows.page/mcp"
    }
  }
}
```

**Windsurf** (`mcp_config.json`)

```json
{
  "mcpServers": {
    "rows": {
      "serverUrl": "https://rows.page/mcp"
    }
  }
}
```

**Codex CLI** (`~/.codex/config.toml`)

```toml
[mcp_servers.rows]
url = "https://rows.page/mcp"
```

**Gemini CLI** (`~/.gemini/settings.json`)

```json
{
  "mcpServers": {
    "rows": {
      "url": "https://rows.page/mcp",
      "type": "http"
    }
  }
}
```

### API key (optional)

Anonymous pushes work. An API key (created at https://rows.page/account) pushes into your account, unlocks `list_datasets`, and lets you re-push by a stable `key`. Send it as `Authorization: Bearer rpk_...` or append `?apiKey=rpk_...` to the URL.

```sh
claude mcp add --transport http rows https://rows.page/mcp --header "Authorization: Bearer rpk_..."
```

## Tools

| Tool | What it does |
|---|---|
| `push` | Push `csv`, `tsv`, `json`, `jsonl`, `rows` (array of objects) or a `url`. Optional `title`, `summary`, `key`, `id_column`, `findings[]` and provenance (`source`, `run`, `commit`). Pass `dataset` + `token` for a new version. Returns the link plus next steps for your human. |
| `pin_finding` | Pin a saved view (title, note, `q` filter, `sort`, `cols` or SQL) to a dataset. The page opens on its findings. |
| `get_dataset` | Meta, versions, findings, columns and the first 20 rows. |
| `list_datasets` | Your datasets, newest first. Needs an API key. |
| `delete_dataset` | Delete a dataset you own. Its URL stops working immediately. |
| `get_limits` | Your plan, its caps and usage. |

Every push is an immutable version. Identical bytes return `unchanged: true`. Re-pushes report rows new, gone and changed (set `id_column` so changed rows can be matched) plus a diff URL. Finding filters use the rows.page query language (`status:failed latency_ms:>2000`, `(country:NL OR country:BE) amount:>500`) or SQL starting with `SELECT`.

## Example prompts

```
Push results.csv to rows.page titled "Eval run 412", pin the failed rows as a finding with a one-line note on what they have in common, and give me the link.
```

```
Query the last 24h of orders, push them to rows.page with key nightly-orders and id_column order_id, then tell me how many rows changed since yesterday.
```

```
Fetch https://example.com/export.parquet, push it to rows.page, pin a finding for amount:>500 sorted by -amount, and hand me the owner link.
```

## Limits

Anonymous: 100 MB per file (16 MB inline per MCP push, use `url` for bigger files), deleted 7 days after creation unless kept, 50 pushes and 1 GB per day per IP, 60 MCP calls per minute. Free, Pro and Max plans: https://rows.page/pricing.

## Links

- Docs: https://rows.page/docs/mcp
- REST API: https://rows.page/docs/api
- OpenAPI: https://rows.page/openapi.json
- llms.txt: https://rows.page/llms.txt
- Official MCP registry name: `page.rows/rows`

Made by [Bitgate](https://bitgate.com). MIT licensed.
