# screaming-frog-mcp

MCP server that lets Claude run [Screaming Frog SEO Spider](https://www.screamingfrog.co.uk/seo-spider/) headless crawls, export data, and manage crawl storage — without anyone opening the GUI.

Type a URL into Claude. Screaming Frog runs in the background. You get the data back. That's it.

Forked from [bzsasson/screaming-frog-mcp](https://github.com/bzsasson/screaming-frog-mcp) v0.1.0 with bug fixes. The original had issues that made it unusable in practice — pipe deadlocks that hung crawls, false GUI detection that blocked everything after the first run, a delete command that could wipe your entire crawl database. All fixed.

---

## What's fixed

| Bug | Fix |
|-----|-----|
| **Pipe deadlock** | stdout/stderr redirected to log files instead of PIPE. Crawls no longer hang when SF produces large output. |
| **GUI detection** | Uses `psutil` instead of `ps aux`. Works on Mac and Windows. Headless CLI processes no longer get mistaken for the GUI. |
| **Stale crawl cleanup** | SF leaves a temp `crawl.seospider` file inside its own app bundle when a crawl gets interrupted. Every crawl after that fails. Now auto-cleaned before each run. |
| **Delete safety** | `delete_crawl(".")` used to resolve to the root data directory and wipe everything. Fixed. |
| **Export dir leak** | Failed exports left temp directories on disk. Now cleaned up. |
| **Input validation** | Stricter character allowlists for CLI arguments and db_id. |

---

## Requirements

- [Screaming Frog SEO Spider](https://www.screamingfrog.co.uk/seo-spider/) with a paid license — headless crawls require a license
- Python 3.10+
- [uv](https://docs.astral.sh/uv/) (recommended) or pip

---

## Installation

### Mac

```bash
uvx --from git+https://github.com/marykovziridze/screaming-frog-mcp screaming-frog-mcp
```

Add to `~/Library/Application Support/Claude/claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "screaming-frog": {
      "command": "uvx",
      "args": ["--from", "git+https://github.com/marykovziridze/screaming-frog-mcp", "screaming-frog-mcp"]
    }
  }
}
```

### Windows

Install [uv](https://docs.astral.sh/uv/) first:

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Add to `C:\Users\[name]\AppData\Roaming\Claude\claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "screaming-frog": {
      "command": "uvx",
      "args": ["--from", "git+https://github.com/marykovziridze/screaming-frog-mcp", "screaming-frog-mcp"],
      "env": {
        "SF_CLI_PATH": "C:\\Program Files (x86)\\Screaming Frog SEO Spider\\ScreamingFrogSEOSpiderCli.exe"
      }
    }
  }
}
```

Restart Claude Desktop after editing the config.

---

## Tools

| Tool | What it does |
|------|-------------|
| `sf_check` | Verify SF is installed and licensed |
| `crawl_site` | Start a headless crawl |
| `crawl_status` | Check crawl progress |
| `list_crawls` | List saved crawls in SF's database |
| `export_crawl` | Export crawl data as CSV |
| `read_crawl_data` | Read and filter exported CSV data |
| `delete_crawl` | Delete a saved crawl |
| `storage_summary` | Show disk usage of crawl storage |

---

## Configuration

| Variable | Default | Notes |
|----------|---------|-------|
| `SF_CLI_PATH` | Mac: auto-detected | Set manually on Windows or custom installs |

---

## Known limitations

- **Windows stale crawl path** — auto-cleanup works on Mac. On Windows, if crawls fail after an interruption, check for a `crawl.seospider` file in your SF install directory and delete it manually.
- **No crawl progress percentage** — SF's headless CLI doesn't report progress mid-crawl. You know when it starts and when it finishes.
- **Large sites** — tested on sites up to ~160 pages. Not stress-tested on 10k+ page sites.

---

## License

MIT — see [LICENSE](LICENSE)

---

## Credits

Original MCP server by [Boaz Sasson](https://github.com/bzsasson/screaming-frog-mcp).
