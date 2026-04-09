# screaming-frog-mcp

MCP server that lets Claude run [Screaming Frog SEO Spider](https://www.screamingfrog.co.uk/seo-spider/) headless crawls, export data, and manage crawl storage — without anyone opening the GUI.

Type a URL into Claude. Screaming Frog runs in the background. You get the data back. That's it.

Forked from [bzsasson/screaming-frog-mcp](https://github.com/bzsasson/screaming-frog-mcp) v0.1.0 with 10+ bug fixes. The original had issues that made it unusable in practice — pipe deadlocks that hung crawls, false GUI detection that blocked everything after the first run, a delete command that could wipe your entire crawl database. All fixed.

---

## Includes a technical SEO scan skill

This isn't just an MCP server. The repo ships with a ready-to-use Claude skill: **[technical-seo-scan](skills/technical-seo-scan/SKILL.md)**.

Drop a URL. The skill crawls the site, exports the data, scores six categories, segments findings by page type, and gives you a prioritized action plan. Actual numbers, actual URLs, actual fixes — not a generic checklist.

**What it checks:**
- Crawlability — status codes, redirect chains, orphan pages, crawl depth
- Indexability — noindex misuse, canonical conflicts, duplicate content
- On-page — titles, meta descriptions, H1s, images, thin content
- Page speed — response times, oversized pages and images
- Structured data — schema coverage, validation errors, missing opportunities
- Security — HTTPS, mixed content, EEAT signals

**What you get back:**
- Health score per category (0-100)
- Critical issues to fix now
- Quick wins — high impact, low effort
- Full breakdown by page type (homepage, blog, product, category)
- Action roadmap (week 1, month 1, ongoing)
- CSV files saved to your Desktop for Excel

Works with just Screaming Frog. If you also have DataforSEO or Google Search Console MCPs connected, the skill picks them up and enriches the report with keyword data and real search performance.

---

## What's fixed

The original v0.1.0 had bugs that made it break in real use. Here's what we patched:

- **Pipe deadlock** — stdout/stderr redirected to log files instead of PIPE. Crawls no longer hang when SF produces large output.
- **GUI detection** — uses `psutil` instead of `ps aux`. Works on Mac and Windows. Headless CLI processes no longer get mistaken for the GUI.
- **Stale crawl cleanup** — SF leaves a temp `crawl.seospider` file inside its own app bundle when a crawl gets interrupted. Every crawl after that fails. Now auto-cleaned before each run.
- **Delete safety** — `delete_crawl(".")` used to resolve to the root data directory and wipe everything. Fixed.
- **Export dir leak** — failed exports left temp directories on disk forever. Now cleaned up.
- **Input validation** — stricter character allowlists for CLI arguments and db_id.

---

## Requirements

- [Screaming Frog SEO Spider](https://www.screamingfrog.co.uk/seo-spider/) with a paid license — headless crawls don't work on the free version
- Python 3.10+

---

## Installation

### Mac

```bash
pip install git+https://github.com/marykovziridze/screaming-frog-mcp.git
```

Add to `~/Library/Application Support/Claude/claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "screaming-frog": {
      "command": "screaming-frog-mcp"
    }
  }
}
```

### Windows

Install [uv](https://docs.astral.sh/uv/) first — it bundles its own Python, no separate install needed:

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Add to `C:\Users\[name]\AppData\Roaming\Claude\claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "screaming-frog": {
      "command": "uvx",
      "args": ["--from", "git+https://github.com/marykovziridze/screaming-frog-mcp.git", "screaming-frog-mcp"],
      "env": {
        "SF_CLI_PATH": "C:\\Program Files (x86)\\Screaming Frog SEO Spider\\ScreamingFrogSEOSpiderCli.exe"
      }
    }
  }
}
```

Restart Claude desktop after editing the config.

### Installing the skill

Copy the `skills/technical-seo-scan/` folder to your Claude skills directory:

```bash
# Mac / Linux
cp -r skills/technical-seo-scan ~/.claude/skills/

# Windows (PowerShell)
Copy-Item -Recurse skills/technical-seo-scan $env:USERPROFILE\.claude\skills\
```

Then type `/technical-seo-scan https://example.com` in Claude.

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

## Rough edges

Being honest about what's not perfect yet:

- **Windows stale crawl path** — we know where SF dumps its temp file on Mac (`Contents/Java/`). On Windows it's probably next to the CLI exe, but not confirmed yet. If your crawls fail after an interruption on Windows, check for a `crawl.seospider` file in your SF install directory and delete it.
- **No crawl progress percentage** — SF's headless CLI doesn't report progress mid-crawl. You just know it's running or done.
- **Large sites** — tested on a 160-page site (19 seconds). Haven't stress-tested on 10k+ page sites yet. The skill caps detailed analysis at 50 pages and points you to the CSVs for the rest.

---

## License

MIT — see [LICENSE](LICENSE)

---

## Credits

Original MCP server by [Boaz Sasson](https://github.com/bzsasson/screaming-frog-mcp).
