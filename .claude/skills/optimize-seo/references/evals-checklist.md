# SEO Evals Masterlist

> Canonical checklist for SEO audits on Next.js App Router projects.
> Each item has a unique ID, source, category, and point weight.
> During audits, mark each item as PASS / FAIL / WARN / N-A.

## Scoring

- **PASS** = full points
- **WARN** = half points
- **FAIL** = 0 points
- **N-A** = excluded from scoring (weight redistributed)

---

## 1. Crawlability & Indexing (Weight: 12%)

| ID | Check | Source | Pts |
|----|-------|--------|-----|
| C-01 | All pages server-renderable (no JS-only content blocking crawlers) | master-context 2.1 | 5 |
| C-02 | Dynamic routes use `generateStaticParams()` for static generation | master-context 2.1 | 5 |
| C-03 | `sitemap.ts` covers all indexable URLs (static, dynamic, paginated) | master-context 2.1, seo-sitemap | 5 |
| C-04 | `robots.ts` configured (allow crawlable paths, block `/api/`, internal routes) | master-context 2.1, seo-technical | 5 |
| C-05 | AI crawler rules defined (GPTBot, ClaudeBot, CCBot, etc.) | master-context 2.1, seo-geo | 3 |
| C-06 | No redirect chains or loops (www/non-www consistency) | master-context 2.1, seo-technical | 4 |
| C-07 | Canonical URLs consistent across all pages via shared `SITE_URL` constant | master-context 2.1 | 5 |
| C-08 | `noindex` on utility pages (search, confirmation, admin, hidden content) | master-context 2.1 | 3 |
| C-09 | Custom `not-found.tsx` with `robots: { index: false, follow: true }` + nav links | master-context 2.1 | 3 |
| C-10 | `llms.txt` in public root for AI crawler guidance | master-context 2.1, seo-geo | 3 |
| C-11 | ISR or static generation for all cacheable content | master-context 2.1 | 4 |
| C-12 | On-demand revalidation for content updates (webhooks or manual trigger) | master-context 2.1 | 3 |

---

## 2. HTML Semantics (Weight: 8%)

| ID | Check | Source | Pts |
|----|-------|--------|-----|
| H-01 | Single `<h1>` per page (check Header, Footer, layout components for rogue h1s) | master-context 2.2, Lighthouse | 5 |
| H-02 | Proper heading hierarchy (h1 > h2 > h3, no level skipping) | master-context 2.2, Lighthouse | 4 |
| H-03 | Semantic elements used: `<nav>`, `<main>`, `<article>`, `<section>`, `<footer>` | master-context 2.2 | 3 |
| H-04 | `lang` attribute on `<html>` element | master-context 2.2, Lighthouse | 3 |
| H-05 | Internal links use `<a>` / `<Link>`, not `<button>` with onClick navigation | master-context 2.2 | 4 |
| H-06 | Internal links do NOT use `target="_blank"` (kills link equity) | master-context 2.2 | 5 |

---

## 3. Metadata (Weight: 10%)

| ID | Check | Source | Pts |
|----|-------|--------|-----|
| M-01 | Every page has `title` + `description` | master-context 2.3, seo-page, Lighthouse | 5 |
| M-02 | Open Graph: title, description, image, type, url, siteName | master-context 2.3, seo-page | 4 |
| M-03 | Twitter card: card type, title, description, image | master-context 2.3 | 3 |
| M-04 | Canonical URL via `alternates.canonical` on all pages | master-context 2.3, seo-technical | 5 |
| M-05 | `robots` directives for hidden/draft content (noindex) | master-context 2.3 | 3 |
| M-06 | `metadataBase` set in root layout | master-context 2.3 | 3 |
| M-07 | Dynamic `generateMetadata()` for data-dependent pages | master-context 2.3 | 4 |
| M-08 | Keyword-optimized titles and descriptions (not generic) | master-context 2.3, seo-content | 4 |

---

## 4. Structured Data (Weight: 10%)

| ID | Check | Source | Pts |
|----|-------|--------|-----|
| S-01 | `Organization` schema in root layout (name, description, logo, contact, telephone) | master-context 2.4, seo-schema | 5 |
| S-02 | `WebSite` + `SearchAction` in root layout (enables sitelinks search box) | master-context 2.4, seo-schema | 4 |
| S-03 | `BreadcrumbList` on all pages (visible UI + JSON-LD) | master-context 2.4, seo-schema | 5 |
| S-04 | Page-specific schemas present (Person, Article, FAQPage, CollectionPage, etc.) | master-context 2.4, seo-schema | 5 |
| S-05 | No deprecated schema types or properties | seo-schema | 4 |
| S-06 | Schema validation passes (Google Rich Results Test) | seo-schema | 4 |
| S-07 | `ImageObject` with dimensions where applicable (not plain URL) | seo-schema | 3 |
| S-08 | `VideoObject` for embedded YouTube/Vimeo videos | master-context 2.4 | 3 |

---

## 5. Performance & CWV (Weight: 10%)

| ID | Check | Source | Pts |
|----|-------|--------|-----|
| P-01 | Image optimization: `next/image`, AVIF/WebP formats, responsive `deviceSizes` | master-context 2.5, seo-images | 5 |
| P-02 | `loading.tsx` on key route segments | master-context 2.5 | 3 |
| P-03 | `error.tsx` for route-level error boundaries | master-context 2.5 | 2 |
| P-04 | Core Web Vitals monitoring in place (PostHog, Vercel Analytics, web-vitals) | master-context 2.5 | 3 |
| P-05 | Minimal JS bundle — third-party scripts audited, analytics lazy-loaded | master-context 2.5 | 3 |
| P-06 | Above-fold images use `priority` prop on `next/image` (first 2-3 items) | master-context 2.5, seo-images | 4 |
| P-07 | LCP < 2.5s | Lighthouse | 5 |
| P-08 | CLS < 0.1 | Lighthouse | 4 |
| P-09 | INP < 200ms | Lighthouse | 4 |

---

## 6. Security Headers (Weight: 5%)

| ID | Check | Source | Pts |
|----|-------|--------|-----|
| X-01 | `Strict-Transport-Security` (HSTS) | master-context 2.6, seo-technical | 4 |
| X-02 | `X-Frame-Options` (SAMEORIGIN) | master-context 2.6 | 3 |
| X-03 | `X-Content-Type-Options` (nosniff) | master-context 2.6 | 3 |
| X-04 | `Referrer-Policy` (strict-origin-when-cross-origin) | master-context 2.6 | 3 |
| X-05 | `Permissions-Policy` (disable unused APIs: camera, microphone, geolocation) | master-context 2.6 | 2 |

---

## 7. Content Quality & E-E-A-T (Weight: 23%)

| ID | Check | Source | Pts |
|----|-------|--------|-----|
| Q-01 | All images have descriptive alt text (no generic "image", "thumbnail") | master-context 2.7, seo-images, Lighthouse | 5 |
| Q-02 | Blog posts meet word count minimums (1,200-2,000 words) | master-context 2.7, seo-content | 4 |
| Q-03 | Regular publishing cadence (2+ posts/month) | master-context 2.7, seo-content | 3 |
| Q-04 | Author bios on blog posts with credentials (E-E-A-T) | master-context 2.7, seo-content | 5 |
| Q-05 | Keyword-optimized headings (H2s with search terms, not generic) | master-context 2.7, seo-content | 4 |
| Q-06 | Content depth on hub/category pages (200-400 words unique intro) | master-context 2.7, seo-content | 4 |
| Q-07 | E-E-A-T: Experience signals (case studies, testimonials, quantified results) | seo-content | 5 |
| Q-08 | E-E-A-T: Expertise signals (author credentials, depth of analysis) | seo-content | 5 |
| Q-09 | E-E-A-T: Authoritativeness signals (backlinks, citations, industry recognition) | seo-content | 5 |
| Q-10 | E-E-A-T: Trustworthiness signals (contact info, privacy policy, trust badges) | seo-content | 5 |
| Q-11 | No fake/placeholder content (template testimonials, lorem ipsum, dummy data) | seo-content | 5 |

---

## 8. Internal Linking (Weight: 8%)

| ID | Check | Source | Pts |
|----|-------|--------|-----|
| L-01 | Breadcrumbs present (visible UI + BreadcrumbList JSON-LD) | master-context 2.8 | 5 |
| L-02 | Related content sections on detail pages | master-context 2.8 | 4 |
| L-03 | Cross-linking between content types (blog -> profiles -> categories) | master-context 2.8 | 4 |
| L-04 | Footer contains crawlable links to key pages | master-context 2.8 | 3 |
| L-05 | Entity mentions in content link to their canonical pages | master-context 2.8 | 4 |
| L-06 | All internal links use `<Link>` without `target="_blank"` | master-context 2.8 | 5 |

---

## 9. AI Search Readiness / GEO (Weight: 10%)

| ID | Check | Source | Pts |
|----|-------|--------|-----|
| G-01 | AI crawlers allowed in robots.txt | seo-geo | 4 |
| G-02 | `llms.txt` present and well-structured (URL patterns, citation guidance) | seo-geo | 4 |
| G-03 | Passage-level citability (self-contained answer blocks, TL;DRs) | seo-geo | 4 |
| G-04 | Brand mention signals consistent across meta, headings, schemas | seo-geo | 3 |
| G-05 | All critical content server-side rendered (no JS-only blocks) | seo-geo, seo-technical | 5 |
| G-06 | Comprehensive structured data for AI discoverability | seo-geo, seo-schema | 4 |
| G-07 | Question-based headings for query matching (H2/H3 as questions) | seo-geo | 3 |
| G-08 | Tables and ordered lists for comparative/procedural data | seo-geo | 3 |

---

## 10. Images (Weight: 5%)

| ID | Check | Source | Pts |
|----|-------|--------|-----|
| I-01 | Alt text present on all non-decorative images | seo-images, Lighthouse | 5 |
| I-02 | No oversized images (> 500KB without optimization) | seo-images | 4 |
| I-03 | Modern formats enabled (WebP/AVIF via next/image) | seo-images | 4 |
| I-04 | Responsive images with `sizes` attribute on key components | seo-images | 3 |
| I-05 | Lazy loading on below-fold images (default `next/image` behavior) | seo-images | 3 |
| I-06 | Width/height or aspect-ratio set for CLS prevention | seo-images | 4 |
| I-07 | OG images present on key pages (homepage, profiles, blog posts) | seo-images | 3 |

---

## 11. Lighthouse Validation (derived — feeds into category scores)

| ID | Check | Source | Pts |
|----|-------|--------|-----|
| LH-01 | SEO score >= 90 on all indexable pages | Lighthouse | 5 |
| LH-02 | Accessibility score >= 90 on all pages | Lighthouse | 4 |
| LH-03 | Best Practices score >= 90 on all pages | Lighthouse | 3 |
| LH-04 | No critical failed audits (is-crawlable, meta-description) | Lighthouse | 5 |
| LH-05 | `heading-order` audit passes | Lighthouse | 4 |
| LH-06 | `is-crawlable` audit passes | Lighthouse | 5 |
| LH-07 | `meta-description` audit passes | Lighthouse | 3 |
| LH-08 | `link-text` audit passes (descriptive link text) | Lighthouse | 3 |

---

## Quick Reference

- **Total items**: 78
- **Categories**: 11
- **Max score**: Sum of all Pts columns within weighted categories
- **Health Score formula**: `sum(category_score_pct * category_weight) = 0-100`
