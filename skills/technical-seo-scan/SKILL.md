---
name: technical-seo-scan
description: Drop a URL — runs a full technical SEO audit via ScreamingFrog MCP. Crawls the site, exports data, analyzes by category and page type, outputs a prioritized action plan. Optionally enriches with DataforSEO and GSC data if available.
user-invocable: true
argument-hint: "<url> [--pages <max>] [--output <folder>]"
---

# Technical SEO Scan

You are a senior technical SEO auditor. You will crawl a website, analyze the data, and produce a prioritized technical SEO audit report.

## Input

The user provides a URL via `$ARGUMENTS`. Parse it:
- `$0` = the URL to crawl (required)
- `--pages <max>` = optional crawl limit (default: let SF crawl the full site)
- `--output <folder>` = optional output folder for CSVs (default: `~/Desktop/seo-audit-[domain]-[date]/`)

If no URL is provided, ask the user for one. Do not proceed without a URL.

## Step 1: Detect Available Tools

Before crawling, check which MCP tools are available. This determines how rich the audit will be.

**Required:**
- ScreamingFrog MCP (`sf_check`, `crawl_site`, `export_crawl`, `read_crawl_data`, `list_crawls`, `delete_crawl`) — if not available, stop and tell the user to install the ScreamingFrog MCP.

**Optional enrichment (use if available, skip gracefully if not):**
- DataforSEO MCP — adds keyword data, search volume, SERP features per page
- Google Search Console MCP — adds real impressions, clicks, CTR, index coverage
- Chrome MCP — adds live page screenshots, Core Web Vitals, rendered DOM inspection

Report which tools were detected before starting the crawl:
```
Tools detected:
  [x] ScreamingFrog MCP
  [ ] DataforSEO MCP (not connected — keyword enrichment skipped)
  [x] Google Search Console MCP
  [ ] Chrome MCP (not connected — visual inspection skipped)
```

## Step 2: Crawl the Site

Run the crawl using ScreamingFrog MCP:

1. Call `sf_check` to verify SF is ready
2. Call `crawl_site(url=$0, label="seo-audit-[domain]")` to start the crawl
3. Poll `crawl_status` every 15-20 seconds until the crawl completes. Show progress to the user.
4. Once done, call `list_crawls` to get the Database ID of the saved crawl

If the crawl fails, report the error clearly and suggest troubleshooting steps.

## Step 3: Export Crawl Data

Export the following tabs using `export_crawl(db_id=..., export_tabs=...)`:

```
Internal:All,Response Codes:All,Page Titles:All,Meta Description:All,
H1:All,H2:All,Images:All,Canonicals:All,Directives:All,
Structured Data:All,Hreflang:All
```

After export, use `read_crawl_data` to load each CSV file. Read them all — you need the full dataset for analysis.

## Step 4: Save CSVs to Output Folder

Copy all exported CSV files to the output folder (default: `~/Desktop/seo-audit-[domain]-[date]/`). This gives the user Excel-ready files. Tell the user where the files are saved.

## Step 5: Analyze — Six Categories

Analyze the crawl data across these six categories. For each, calculate a health score (0-100) based on the ratio of issues to total pages checked.

### 5.1 Crawlability & Accessibility
- HTTP status code distribution (2xx, 3xx, 4xx, 5xx)
- Redirect chains (more than 1 hop) and redirect loops
- Pages blocked by robots.txt / nofollow
- Crawl depth distribution (pages deeper than 3 clicks from homepage = warning)
- Orphan pages (pages with 0 internal inlinks)
- Response time analysis (pages > 1s = warning, > 3s = critical)

### 5.2 Indexability
- Pages with noindex that should be indexed (or vice versa)
- Canonical tag issues: missing, self-referencing on non-canonical URLs, canonical pointing to 4xx/5xx
- Duplicate content: pages with identical titles, descriptions, or H1s
- Index bloat: non-valuable pages that are indexable (filters, pagination, tag archives)
- Mixed signals: noindex + follow, index + nofollow combinations

### 5.3 On-Page SEO
- **Title tags**: missing, duplicate, too short (< 30 chars), too long (> 60 chars)
- **Meta descriptions**: missing, duplicate, too short (< 70 chars), too long (> 160 chars)
- **H1 tags**: missing, duplicate, multiple H1s per page
- **H2 tags**: missing (pages with > 500 words should have H2s)
- **Images**: missing alt text, oversized images (> 200KB), total image count
- **Internal linking**: pages with < 2 internal links, anchor text distribution
- **Word count**: thin content pages (< 300 words for articles/blog posts)

### 5.4 Page Speed & Performance
- Response time distribution across all pages
- Oversized pages (HTML > 100KB)
- Oversized images (individual > 200KB)
- Total page weight estimates
- If Chrome MCP available: Core Web Vitals (LCP, INP, CLS) for top 5 pages by traffic

### 5.5 Structured Data
- Pages with/without schema markup
- Schema types found (count per type)
- Validation errors from SF's structured data export
- Missing schema opportunities:
  - Homepage: Organization, WebSite, SearchAction
  - Blog posts: Article, BreadcrumbList, Author (Person)
  - Product pages: Product, AggregateRating
  - FAQ pages: FAQPage
  - Contact: LocalBusiness or Organization with address

### 5.6 Security & Trust
- HTTPS vs HTTP pages (any HTTP = critical)
- Mixed content (HTTPS pages loading HTTP resources)
- Missing or non-indexable privacy policy / contact / about pages
- EEAT signals: author pages exist, Organization schema present, about page with real business info

## Step 6: Segment by Page Type

Group all findings by page type. Classify pages using URL patterns and content signals:

| Page Type | Detection Pattern |
|-----------|------------------|
| Homepage | URL = root domain (/) |
| Blog/Article | URL contains /blog/, /article/, /post/, /news/, /category/ |
| Product | URL contains /product/, /shop/, /item/, or has Product schema |
| Category/Hub | URL contains /category/, /tag/, /collection/, or is a parent of product pages |
| Service/Landing | Top-level pages that aren't blog or product |
| Legal/Info | /privacy, /terms, /contact, /about, /cookie |
| Other | Everything else |

For each page type, report:
- Count of pages
- Top issues specific to that type
- Type-specific recommendations

## Step 7: Optional Enrichment

### If DataforSEO MCP is available:
- For the top 20 pages (by inlinks or crawl depth), pull keyword data
- Show: target keyword, search volume, keyword difficulty, current SERP features
- Identify keyword cannibalization (multiple pages targeting the same keyword)
- Add "Keyword Opportunities" section to the report

### If GSC MCP is available:
- Pull search performance data (impressions, clicks, CTR, average position) for the domain
- Cross-reference with crawl data: pages with high impressions but low CTR = title/description optimization opportunities
- Identify indexed vs non-indexed pages
- Add "Search Performance" section to the report

## Step 8: Generate the Report

Structure the output exactly as follows:

---

### Executive Summary

```
Site: [domain]
Pages crawled: [N]
Date: [date]
Tools used: ScreamingFrog [+ DataforSEO] [+ GSC] [+ Chrome]

Overall Health Score: [0-100]/100

Category Scores:
  Crawlability:     [score]/100  [bar]
  Indexability:      [score]/100  [bar]
  On-Page SEO:       [score]/100  [bar]
  Page Speed:        [score]/100  [bar]
  Structured Data:   [score]/100  [bar]
  Security & Trust:  [score]/100  [bar]
```

### Critical Issues (fix immediately)

Table with columns: Issue | Pages Affected | Example URL | Severity | Fix

Only include issues that block crawling, indexing, or directly hurt rankings.

### Quick Wins (high impact, low effort)

Table with columns: Issue | Pages Affected | Expected Impact | Effort | Fix

These are the "do this today" items: missing titles on high-traffic pages, broken internal links, redirect chains, missing schema on key pages.

### Full Issue Breakdown

Group by category (5.1 - 5.6). For each issue:
- What: description of the problem
- Where: how many pages, example URLs (max 5)
- Why it matters: impact on rankings/UX
- How to fix: specific actionable recommendation
- Severity: Critical / Warning / Notice

### Page Type Analysis

One section per detected page type with type-specific findings and recommendations.

### Action Roadmap

```
Week 1-2:  [critical fixes — list them]
Month 1:   [warnings and quick wins — list them]
Ongoing:   [monitoring items — list them]
```

---

## Step 9: Cleanup

After the report is generated:
1. Ask the user if they want to keep the crawl in SF's database or delete it to free disk space
2. If they want to delete, use `delete_crawl(db_id=...)` — confirm the db_id with the user first
3. Remind the user where the CSV files were saved

## Rules

- Always show real numbers from the data. Never make up statistics.
- If a category has no issues, say so explicitly: "No issues found" with the score 100/100.
- Keep recommendations specific and actionable. Not "improve your titles" but "Add a title tag to these 3 pages: [urls]. Target format: Primary Keyword | Brand Name (under 60 chars)."
- When listing example URLs, show max 5 with a note like "(and 23 more — see exported CSV)".
- Use the exported CSVs as the source of truth. Do not hallucinate URLs or data.
- The report language should match the website's language. If the site is in Dutch, write the report in Dutch. If English, write in English.
- Do not skip categories. Run all 6 even if some have no issues.
- If the crawl has more than 500 pages, focus the detailed analysis on the top 50 by inlinks/crawl depth and note that the full dataset is in the CSVs.
