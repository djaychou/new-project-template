---
name: seo
description: >
  Comprehensive SEO analysis for any website or business type. Performs full site
  audits, single-page deep analysis, technical SEO checks (crawlability, indexability,
  Core Web Vitals with INP), schema markup detection/validation/generation, content
  quality assessment (E-E-A-T framework per Dec 2025 update extending to all
  competitive queries), image optimization, sitemap analysis, and Generative Engine
  Optimization (GEO) for AI Overviews, ChatGPT, and Perplexity citations. Analyzes
  AI crawler accessibility (GPTBot, ClaudeBot, PerplexityBot), llms.txt compliance,
  brand mention signals, and passage-level citability. Industry detection for SaaS,
  e-commerce, local business, publishers, agencies. Triggers on: "SEO", "audit",
  "schema", "Core Web Vitals", "sitemap", "E-E-A-T", "AI Overviews", "GEO",
  "technical SEO", "content quality", "page speed", "structured data".
user-invokable: true
argument-hint: "[command] [url]"
---

# SEO: Universal SEO Analysis Skill

Comprehensive SEO analysis across all industries (SaaS, local services,
e-commerce, publishers, agencies). Orchestrates 12 specialized sub-skills and 7 subagents
(+ optional extension sub-skills like seo-dataforseo).

## No arguments? Show the guide.

If the user runs `/seo` with no arguments (or `/seo help`), do NOT run any
analysis. Instead, print this guide exactly:

---

**What do you want to do?**

| I want to... | Run this |
|---|---|
| Get a full audit + have fixes applied for me | `/optimize-seo` |
| Get a full audit report (analysis only, no changes) | `/seo audit <url>` |
| Check one specific page in depth | `/seo page <url>` |
| Check if my site is crawlable, fast, and secure | `/seo technical <url>` |
| Evaluate content quality and E-E-A-T signals | `/seo content <url>` |
| Check or generate structured data (JSON-LD) | `/seo schema <url>` |
| Audit image optimization (alt text, sizes, formats) | `/seo images <url>` |
| See how my site appears to AI search (ChatGPT, Perplexity) | `/seo geo <url>` |
| Analyze or generate my sitemap | `/seo sitemap <url>` |
| Build an SEO strategy from scratch | `/seo plan <business-type>` |
| Plan pages at scale (programmatic SEO) | `/seo programmatic` |
| Create competitor comparison pages | `/seo competitor-pages` |
| Audit international/hreflang setup | `/seo hreflang` |

**Tips:**
- `<url>` can be a live URL or `localhost:3000` if your dev server is running
- `/optimize-seo` is the all-in-one: it audits, asks what to fix, fixes it, and re-scores
- Most commands work without a URL — they'll analyze your codebase directly

---

Then stop. Wait for the user to pick.

## Quick Reference

| Command | What it does |
|---------|-------------|
| `/seo audit <url>` | Full website audit with parallel subagent delegation |
| `/seo page <url>` | Deep single-page analysis |
| `/seo sitemap <url or generate>` | Analyze or generate XML sitemaps |
| `/seo schema <url>` | Detect, validate, and generate Schema.org markup |
| `/seo images <url>` | Image optimization analysis |
| `/seo technical <url>` | Technical SEO audit (9 categories) |
| `/seo content <url>` | E-E-A-T and content quality analysis |
| `/seo geo <url>` | AI Overviews / Generative Engine Optimization |
| `/seo plan <business-type>` | Strategic SEO planning |
| `/seo programmatic [url\|plan]` | Programmatic SEO analysis and planning |
| `/seo competitor-pages [url\|generate]` | Competitor comparison page generation |
| `/seo hreflang [url]` | Hreflang/i18n SEO audit and generation |
| `/seo dataforseo [command]` | Live SEO data via DataForSEO (extension) |
| `/seo image-gen [use-case] <description>` | AI image generation for SEO assets (extension) |

## Orchestration Logic

When the user invokes `/seo audit`, delegate to subagents in parallel:
1. Detect business type (SaaS, local, ecommerce, publisher, agency, other)
2. Spawn subagents: seo-technical, seo-content, seo-schema, seo-sitemap, seo-performance, seo-visual, seo-geo
3. Collect results and generate unified report with SEO Health Score (0-100)
4. Create prioritized action plan (Critical -> High -> Medium -> Low)

For individual commands, load the relevant sub-skill directly.

## Industry Detection

Detect business type from homepage signals:
- **SaaS**: pricing page, /features, /integrations, /docs, "free trial", "sign up"
- **Local Service**: phone number, address, service area, "serving [city]", Google Maps embed
- **E-commerce**: /products, /collections, /cart, "add to cart", product schema
- **Publisher**: /blog, /articles, /topics, article schema, author pages, publication dates
- **Agency**: /case-studies, /portfolio, /industries, "our work", client logos

## Quality Gates

Read `references/quality-gates.md` for thin content thresholds per page type.
Hard rules:
- WARNING at 30+ location pages (enforce 60%+ unique content)
- HARD STOP at 50+ location pages (require user justification)
- Never recommend HowTo schema (deprecated Sept 2023)
- FAQ schema for Google rich results: only government and healthcare sites (Aug 2023 restriction); existing FAQPage on commercial sites -> flag Info priority (not Critical), noting AI/LLM citation benefit; adding new FAQPage -> not recommended for Google benefit
- All Core Web Vitals references use INP, never FID

## Reference Files (loaded on-demand)

**Analysis workflows** (load the relevant one based on user request):
- `references/audit.md`: Full website audit process and report structure
- `references/page.md`: Deep single-page analysis
- `references/technical.md`: Technical SEO audit (9 categories)
- `references/content.md`: E-E-A-T and content quality analysis
- `references/schema-workflow.md`: Schema detection, validation, and generation
- `references/images.md`: Image optimization analysis
- `references/sitemap.md`: Sitemap analysis and generation
- `references/geo.md`: AI Overviews / GEO optimization
- `references/plan.md`: Strategic SEO planning with industry templates
- `references/programmatic.md`: Programmatic SEO analysis
- `references/competitor-pages.md`: Competitor comparison page generation
- `references/hreflang.md`: Hreflang/i18n SEO audit and generation

**Data references** (load as needed for specific lookups):
- `references/cwv-thresholds.md`: Core Web Vitals thresholds
- `references/schema-types.md`: Schema types with deprecation status
- `references/eeat-framework.md`: E-E-A-T evaluation criteria
- `references/quality-gates.md`: Content length and uniqueness thresholds

**Templates and assets**:
- `assets/plan-templates/`: Industry-specific SEO plan templates (saas.md, ecommerce.md, etc.)
- `schema/templates.json`: JSON-LD schema templates

## Scoring Methodology

### SEO Health Score (0-100)
Weighted aggregate of all categories:

| Category | Weight |
|----------|--------|
| Technical SEO | 22% |
| Content Quality | 23% |
| On-Page SEO | 20% |
| Schema / Structured Data | 10% |
| Performance (CWV) | 10% |
| AI Search Readiness | 10% |
| Images | 5% |

### Priority Levels
- **Critical**: Blocks indexing or causes penalties (immediate fix required)
- **High**: Significantly impacts rankings (fix within 1 week)
- **Medium**: Optimization opportunity (fix within 1 month)
- **Low**: Nice to have (backlog)

## Subagents

For parallel analysis during audits:
- `seo-technical` -- Crawlability, indexability, security, CWV
- `seo-content` -- E-E-A-T, readability, thin content
- `seo-schema` -- Detection, validation, generation
- `seo-sitemap` -- Structure, coverage, quality gates
- `seo-performance` -- Core Web Vitals measurement
- `seo-visual` -- Screenshots, mobile testing, above-fold
- `seo-geo` -- AI crawler access, llms.txt, citability, brand mention signals
- `seo-dataforseo` -- Live SERP, keyword, backlink, local SEO data (extension, optional)
- `seo-image-gen` -- SEO image audit and generation plan (extension, optional)

## Error Handling

| Scenario | Action |
|----------|--------|
| Unrecognized command | List available commands from the Quick Reference table. Suggest the closest matching command. |
| URL unreachable | Report the error and suggest the user verify the URL. Do not attempt to guess site content. |
| Sub-skill fails during audit | Report partial results from successful sub-skills. Clearly note which sub-skill failed and why. Suggest re-running the failed sub-skill individually. |
| Ambiguous business type detection | Present the top two detected types with supporting signals. Ask the user to confirm before proceeding with industry-specific recommendations. |
