# SEO Audit — {YYYY-MM-DD}

## Audit Methodology

This audit combines:
- Manual code review (codebase exploration via Read/Grep/Glob)
- Automated analysis via claude-seo skills (technical, content, schema, images, GEO)
- Lighthouse audits via Chrome DevTools MCP (SEO, Accessibility, Best Practices)
- Evals checklist (78 items across 11 weighted categories)
- SEO methodology and common pitfalls reference

## Summary

{2-3 sentence executive summary. Include total issues found by severity and overall health score.}

---

## SEO Health Score: {XX}/100

| Category | Weight | Score | Weighted |
|----------|--------|-------|----------|
| Crawlability & Indexing | 12% | /100 | |
| HTML Semantics | 8% | /100 | |
| Metadata | 10% | /100 | |
| Structured Data | 10% | /100 | |
| Performance & CWV | 10% | /100 | |
| Security Headers | 5% | /100 | |
| Content Quality & E-E-A-T | 23% | /100 | |
| Internal Linking | 8% | /100 | |
| AI Search Readiness (GEO) | 10% | /100 | |
| Images | 5% | /100 | |
| **Total** | **100%** | | **{XX}** |

---

## Evals Checklist

> Each item scored against the current codebase. Reference: `.claude/skills/optimize-seo/evals-checklist.md`

### 1. Crawlability & Indexing

| ID | Check | Status | Score | Notes |
|----|-------|--------|-------|-------|
| C-01 | Server-renderable pages | | /5 | |
| C-02 | generateStaticParams() on dynamic routes | | /5 | |
| C-03 | sitemap.ts covers all URLs | | /5 | |
| C-04 | robots.ts configured | | /5 | |
| C-05 | AI crawler rules | | /3 | |
| C-06 | No redirect chains | | /4 | |
| C-07 | Canonical URLs consistent | | /5 | |
| C-08 | noindex on utility pages | | /3 | |
| C-09 | Custom not-found.tsx | | /3 | |
| C-10 | llms.txt present | | /3 | |
| C-11 | ISR/static generation | | /4 | |
| C-12 | On-demand revalidation | | /3 | |

### 2. HTML Semantics

| ID | Check | Status | Score | Notes |
|----|-------|--------|-------|-------|
| H-01 | Single h1 per page | | /5 | |
| H-02 | Heading hierarchy | | /4 | |
| H-03 | Semantic elements | | /3 | |
| H-04 | lang attribute | | /3 | |
| H-05 | Links use Link, not button | | /4 | |
| H-06 | No target="_blank" on internal links | | /5 | |

### 3. Metadata

| ID | Check | Status | Score | Notes |
|----|-------|--------|-------|-------|
| M-01 | title + description on every page | | /5 | |
| M-02 | Open Graph tags | | /4 | |
| M-03 | Twitter card tags | | /3 | |
| M-04 | Canonical URLs | | /5 | |
| M-05 | robots directives for hidden content | | /3 | |
| M-06 | metadataBase set | | /3 | |
| M-07 | Dynamic generateMetadata() | | /4 | |
| M-08 | Keyword-optimized titles/descriptions | | /4 | |

### 4. Structured Data

| ID | Check | Status | Score | Notes |
|----|-------|--------|-------|-------|
| S-01 | Organization schema | | /5 | |
| S-02 | WebSite + SearchAction | | /4 | |
| S-03 | BreadcrumbList | | /5 | |
| S-04 | Page-specific schemas | | /5 | |
| S-05 | No deprecated schema types | | /4 | |
| S-06 | Schema validation passes | | /4 | |
| S-07 | ImageObject with dimensions | | /3 | |
| S-08 | VideoObject for embeds | | /3 | |

### 5. Performance & CWV

| ID | Check | Status | Score | Notes |
|----|-------|--------|-------|-------|
| P-01 | Image optimization config | | /5 | |
| P-02 | loading.tsx on key routes | | /3 | |
| P-03 | error.tsx boundaries | | /2 | |
| P-04 | CWV monitoring | | /3 | |
| P-05 | Minimal JS bundle | | /3 | |
| P-06 | priority on above-fold images | | /4 | |
| P-07 | LCP < 2.5s | | /5 | |
| P-08 | CLS < 0.1 | | /4 | |
| P-09 | INP < 200ms | | /4 | |

### 6. Security Headers

| ID | Check | Status | Score | Notes |
|----|-------|--------|-------|-------|
| X-01 | HSTS | | /4 | |
| X-02 | X-Frame-Options | | /3 | |
| X-03 | X-Content-Type-Options | | /3 | |
| X-04 | Referrer-Policy | | /3 | |
| X-05 | Permissions-Policy | | /2 | |

### 7. Content Quality & E-E-A-T

| ID | Check | Status | Score | Notes |
|----|-------|--------|-------|-------|
| Q-01 | Descriptive alt text | | /5 | |
| Q-02 | Blog word count | | /4 | |
| Q-03 | Publishing cadence | | /3 | |
| Q-04 | Author bios with credentials | | /5 | |
| Q-05 | Keyword-optimized headings | | /4 | |
| Q-06 | Content depth on hub pages | | /4 | |
| Q-07 | E-E-A-T: Experience | | /5 | |
| Q-08 | E-E-A-T: Expertise | | /5 | |
| Q-09 | E-E-A-T: Authoritativeness | | /5 | |
| Q-10 | E-E-A-T: Trustworthiness | | /5 | |
| Q-11 | No fake/placeholder content | | /5 | |

### 8. Internal Linking

| ID | Check | Status | Score | Notes |
|----|-------|--------|-------|-------|
| L-01 | Breadcrumbs (UI + JSON-LD) | | /5 | |
| L-02 | Related content sections | | /4 | |
| L-03 | Cross-linking between types | | /4 | |
| L-04 | Footer links | | /3 | |
| L-05 | Entity mentions linked | | /4 | |
| L-06 | Internal links without target="_blank" | | /5 | |

### 9. AI Search Readiness (GEO)

| ID | Check | Status | Score | Notes |
|----|-------|--------|-------|-------|
| G-01 | AI crawlers allowed | | /4 | |
| G-02 | llms.txt structured | | /4 | |
| G-03 | Passage-level citability | | /4 | |
| G-04 | Brand mention signals | | /3 | |
| G-05 | SSR for critical content | | /5 | |
| G-06 | Structured data coverage | | /4 | |
| G-07 | Question-based headings | | /3 | |
| G-08 | Tables/lists for comparisons | | /3 | |

### 10. Images

| ID | Check | Status | Score | Notes |
|----|-------|--------|-------|-------|
| I-01 | Alt text on all images | | /5 | |
| I-02 | No oversized images | | /4 | |
| I-03 | Modern formats (WebP/AVIF) | | /4 | |
| I-04 | sizes attribute on key images | | /3 | |
| I-05 | Lazy loading below-fold | | /3 | |
| I-06 | Width/height for CLS prevention | | /4 | |
| I-07 | OG images on key pages | | /3 | |

### 11. Lighthouse Validation

| ID | Check | Status | Score | Notes |
|----|-------|--------|-------|-------|
| LH-01 | SEO >= 90 on indexable pages | | /5 | |
| LH-02 | Accessibility >= 90 | | /4 | |
| LH-03 | Best Practices >= 90 | | /3 | |
| LH-04 | No critical failed audits | | /5 | |
| LH-05 | heading-order passes | | /4 | |
| LH-06 | is-crawlable passes | | /5 | |
| LH-07 | meta-description present | | /3 | |
| LH-08 | link-text descriptive | | /3 | |

---

## Issues Found

### CRITICAL

{List critical issues with file paths, line numbers, and recommended fixes}

### HIGH

{High-severity issues}

### MEDIUM

{Medium-severity issues}

### LOW

{Low-severity issues}

---

## Lighthouse Results

| Page | Device | SEO | A11y | Best Practices | Failed Audits |
|------|--------|-----|------|----------------|---------------|
| {homepage} | Desktop | | | | |
| {listing page} | Desktop | | | | |
| {detail page} | Desktop | | | | |
| {blog post} | Desktop | | | | |
| {category page} | Desktop | | | | |

---

## Priority Action Items

| # | Action | Severity | Status | Est. Effort | Source |
|---|--------|----------|--------|-------------|--------|
| 1 | | | PENDING | | |

**Status values**: PENDING | APPROVED | DONE | DEFERRED | REJECTED | FAILED

---

## Deferred Items

| # | Action | Reason | Date Deferred |
|---|--------|--------|---------------|

---

## Rejected Items

| # | Action | Reason | Date Rejected |
|---|--------|--------|---------------|

---

## Project-Specific Context

{Accumulated over time. Include completed phases, architecture notes, lessons learned.}

---

## Changelog

| Date | Phase | Action | Items Affected |
|------|-------|--------|----------------|
| {date} | Audit | Initial audit completed | All items assessed |
