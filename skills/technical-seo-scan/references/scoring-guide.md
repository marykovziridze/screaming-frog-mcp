# Health Score Calculation Guide

Each category is scored 0-100 based on the percentage of pages passing checks.

## Scoring Formula

```
score = 100 - (critical_issues * 10) - (warnings * 3) - (notices * 1)
minimum score = 0
```

Where each issue is counted per unique affected page (not per occurrence).

## Overall Health Score

Weighted average of all six categories:

| Category | Weight |
|----------|--------|
| Crawlability | 20% |
| Indexability | 25% |
| On-Page SEO | 20% |
| Page Speed | 15% |
| Structured Data | 10% |
| Security & Trust | 10% |

## Severity Classification Rules

### Critical (fix immediately)
- Any 5xx server error on indexable pages
- Homepage returns non-200 status
- noindex on pages that should be indexed (commercial/landing pages)
- Canonical loops or canonical pointing to 4xx/5xx
- Redirect loops
- Site not on HTTPS
- Robots.txt blocking important sections

### Warning (fix within 2 weeks)
- 4xx broken internal links
- Redirect chains (2+ hops)
- Missing title tags on indexable pages
- Duplicate title tags across pages
- Missing H1 on indexable pages
- Response time > 1 second
- Crawl depth > 3 clicks
- Missing schema on key page types
- Mixed content (HTTP resources on HTTPS pages)
- Oversized images (> 200KB)

### Notice (fix when time permits)
- Missing meta descriptions
- Meta description too short/long
- Title too short/long (but present)
- Missing image alt text
- Pages with thin content (< 300 words)
- Missing H2 on long-form content
- Non-critical duplicate content (tag pages, filters)
- Missing optional schema types
