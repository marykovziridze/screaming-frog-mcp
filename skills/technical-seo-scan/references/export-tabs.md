# ScreamingFrog Export Tab Reference

## Default Export for Technical SEO Scan

```
Internal:All,Response Codes:All,Page Titles:All,Meta Description:All,
H1:All,H2:All,Images:All,Canonicals:All,Directives:All,
Structured Data:All,Hreflang:All
```

## What Each Tab Contains

### Internal:All
All internal URLs found during crawl. Key columns: Address, Status Code, Status, Indexability, Indexability Status, Title 1, Meta Description 1, H1-1, Word Count, Crawl Depth, Inlinks, Outlinks, Response Time, Size, Content Type, Canonical Link Element

### Response Codes:All
All URLs grouped by HTTP status. Key columns: Address, Status Code, Status, Redirect URL, Redirect Type

### Page Titles:All
Title tag analysis. Key columns: Address, Title 1, Title 1 Length, Title 1 Pixel Width, Title 2 (if multiple), Indexability

### Meta Description:All
Meta description analysis. Key columns: Address, Meta Description 1, Meta Description 1 Length, Meta Description 1 Pixel Width, Indexability

### H1:All
Heading analysis. Key columns: Address, H1-1, H1-1 Length, H1-2 (if multiple), Indexability

### H2:All
Subheading analysis. Key columns: Address, H2-1, H2-1 Length, H2-2, Indexability

### Images:All
Image analysis. Key columns: Address, Alt Text, Alt Text Length, Size, Type, Status Code

### Canonicals:All
Canonical tag analysis. Key columns: Address, Canonical Link Element, Canonical Link Element Status, Indexability

### Directives:All
Meta robots and X-Robots analysis. Key columns: Address, Meta Robots 1, X-Robots-Tag 1, Indexability, Indexability Status

### Structured Data:All
Schema markup found. Key columns: Address, Schema Type, Validation Status, Validation Errors

### Hreflang:All
International targeting. Key columns: Address, Hreflang Language, Hreflang URL, Status

## Additional Tabs (for deeper audits)

- `Links:All` — all link relationships
- `Content:All` — word count, readability, near-duplicates
- `Sitemaps:All` — sitemap coverage analysis
- `JavaScript:All` — JS rendering issues
- `Security:All` — HTTPS/mixed content
- `PageSpeed:All` — requires PageSpeed API integration

## Bulk Export Options

For deeper analysis, use the `bulk_export` parameter:
- `All Inlinks` — every internal link with anchor text
- `All Outlinks` — every external link
- `All Redirects` — full redirect mapping
- `All Images` — every image with alt text and size
- `Structured Data:Validation Errors` — schema problems only
