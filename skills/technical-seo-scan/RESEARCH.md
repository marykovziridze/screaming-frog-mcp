# Technical SEO Scan Skill — Research Notes

Compiled April 9, 2026 from three parallel research tracks.

---

## 1. What Top Technical SEO Audits Check

### Core Categories (6)

| Category | Key Checks |
|----------|-----------|
| **Crawlability** | Robots.txt, XML sitemaps, crawl depth, orphan pages, redirect chains/loops, crawl budget waste |
| **Indexability** | noindex misuse, canonical conflicts, duplicate content, index bloat, parameter handling |
| **On-Page** | Title tags, meta descriptions, H1s (missing/duplicate/length), thin content, internal linking, anchor text |
| **Page Speed** | LCP, INP (replaced FID March 2024), CLS, TTFB, render-blocking resources, image optimization |
| **Structured Data** | Schema validation (JSON-LD), coverage of key types (FAQ, Product, Article, BreadcrumbList, Organization), rich result eligibility |
| **Security & Infrastructure** | HTTPS enforcement, mixed content, mobile-friendliness, viewport config |

### Priority Framework (Industry Standard)

| Level | Label | Definition |
|-------|-------|------------|
| 1 | **Critical** | Blocks crawling, indexing, or causes direct ranking loss |
| 2 | **Warning** | Degrades performance/UX but doesn't block indexing |
| 3 | **Notice** | Best-practice gaps, low-impact optimizations |

Overlay: **effort vs. impact matrix** — high impact + low effort = quick wins first.

### Page-Type Segmentation

- **Homepage**: Authority signals, load speed, Organization/WebSite schema
- **Category/Hub**: Faceted navigation, pagination, crawl depth, thin content
- **Product**: Product schema, unique content, out-of-stock handling
- **Blog/Article**: Article schema, author pages (EEAT), freshness, internal linking
- **Location**: LocalBusiness schema, NAP consistency

### Professional Output Format

1. Executive summary — health score, top 5 critical issues
2. Prioritized issue table — Issue, Severity, Pages Affected, Fix, Effort, Impact
3. Category scorecards — pass/fail per category
4. Per-page-type breakdown
5. Action roadmap — phased (Week 1-2: critical, Month 1: warnings, Ongoing: monitoring)

### 2025-2026 Changes

- INP replaced FID as Core Web Vital (March 2024)
- AI Overviews — structured data eligibility for AI citation
- Crawl budget efficiency — more critical as Google focuses on "crawl waste"
- AI-generated content detection — thin/AI pages flagged

### EEAT Technical Signals

- Author pages with bio, credentials, social links
- Organization + Person schema
- About/Contact pages indexable with real business info
- Topical authority — internal link clusters around expertise areas

---

## 2. ScreamingFrog Export Capabilities

### Main Export Tabs

Internal, External, Protocol, Response Codes, Page Titles, Meta Description, Meta Keywords, H1, H2, Images, Canonicals, Pagination, Directives, Hreflang, JavaScript, Links, Resources, Structured Data, Sitemaps, PageSpeed, Content, Custom Search, Custom Extraction, Analytics, Search Console

### Key Columns Available

Address, Status Code, Indexability, Indexability Status, Title 1/2, Meta Description 1/2, H1-1/H1-2, Word Count, Content Type, Canonical Link Element, Meta Robots, X-Robots-Tag, Response Time, Last Modified, Size (bytes), Crawl Depth, Inlinks, Outlinks, Unique Inlinks, Link Score (internal PageRank)

### Built-in Reports

- Crawl Overview, Redirect Chains, Redirect & Canonical Chains
- Insecure Content, SERP Summary, Structured Data validation
- Orphan Pages (requires GA/GSC), Near Duplicates, Sitemap mismatches

### Bulk Exports

- All Inlinks / Outlinks (with anchor text, follow/nofollow)
- All Images (with alt text, size)
- All Redirects (3xx)
- Client Error (4xx) Inlinks, Server Error (5xx) Inlinks
- Hreflang URLs, Missing Return Tags
- Structured Data validation errors
- Sitemaps: URLs Not In Sitemap
- Near Duplicates, Exact Duplicates
- Canonicalized, Non-Indexable Canonicals

### Config Files (.seospiderconfig)

Controls: crawl limits, user-agent, custom extraction (XPath/CSS/Regex up to 100 rules), include/exclude URL patterns, crawl speed, JS rendering, robots.txt handling, API integrations, HTTP headers/cookies/auth.

---

## 3. Claude Skill Design Patterns

### Skill File Structure

```
skills/technical-seo-scan/
  SKILL.md           # required — YAML frontmatter + markdown instructions
  references/        # templates, checklists loaded on demand
  scripts/           # executable helpers
  examples/          # example outputs
```

### SKILL.md Format

```yaml
---
name: technical-seo-scan
description: What it does and when to trigger
allowed-tools: Bash(git *) Read
context: fork                    # run in subagent (optional)
argument-hint: "<url>"
---

Instructions in markdown. Supports $ARGUMENTS, $0, $1 placeholders.
Dynamic injection via !`shell command` syntax.
```

### Multi-Tool Orchestration

- **Graceful degradation**: check tool availability, fall back when missing
- **Subagent delegation**: main skill delegates to specialist sub-skills in parallel
- **Skill chaining**: each skill defines what it reads/writes/promotes to shared state

### Output Patterns from Top SEO Skills

- Weighted scoring tables (Technical 22%, Content 23%, Schema 10%)
- Prioritized action plans: Critical > High > Medium > Low
- Executive summary with health score (0-100)
- Every recommendation must include specific numbers
- Structured sections per audit area
