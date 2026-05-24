---
name: optimize-images
description: >
  Audit and optimize Next.js Image components for Vercel deployment cost reduction
  and performance. Analyzes next.config, all Image usages, sizes props, deviceSizes,
  formats, and caching. For Next.js + Vercel projects. Use when user says
  "optimize images", "image costs", "Vercel image optimization", "image audit",
  or "reduce image billing".
user-invokable: true
argument-hint: "[path-to-scan]"
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
  - WebFetch
  - Task
  - Edit
---

# Next.js + Vercel Image Optimization Audit

**Target stack:** Next.js (App Router or Pages Router) deployed on Vercel.

This skill audits `next/image` usage across a codebase to reduce Vercel Image Optimization billing and improve performance.

---

## How Vercel Image Optimization Billing Works

Every unique `(src, width, quality, format)` combination that is not already cached triggers a **billable transformation**.

| Metric | When Billed | What It Means |
|---|---|---|
| **Image Transformations** | Every cache MISS or STALE | New optimization of a unique image variant |
| **Image Cache Writes** | Every cache MISS or STALE | Storing result in global cache (8KB units) |
| **Image Cache Reads** | When fetched from shared global cache (not in-region) | Retrieving cached result (8KB units) |

**Plan limits:**
- Hobby: 1,000 source image optimizations/month (hard cap, then 402 errors)
- Pro ($20/mo): 5,000 included, then $5 per additional 1,000

**Cost multipliers to watch:**
- More entries in `deviceSizes` + `imageSizes` = more possible widths = more transformations per source image
- Dual formats (`['image/avif', 'image/webp']`) can double transformations across browser types
- Missing `sizes` prop causes browsers to request the largest variant (100vw assumed)

---

## Audit Steps

### Step 1: Read `next.config` (ts/js/mjs)

Find and read the Next.js config file. Extract the `images` block:

```
images: {
  unoptimized,        // boolean — skips optimization entirely
  minimumCacheTTL,    // seconds — longer = fewer re-transformations
  deviceSizes,        // array — breakpoints for responsive srcset
  imageSizes,         // array — sizes for fixed-width images
  formats,            // array — ['image/avif', 'image/webp'] or single
  remotePatterns,     // array — allowed remote image hosts
  localPatterns,      // array — allowed local image paths
  qualities,          // array — allowed quality values (if configured)
}
```

**Check for:**

| Setting | Ideal | Issue |
|---|---|---|
| `minimumCacheTTL` | `2678400` (31 days) or higher | Low TTL = frequent re-transformations |
| `deviceSizes` | 6-8 entries, no overlap with `imageSizes` | Too many entries = more variants billed |
| `imageSizes` | 5-7 entries for small fixed-size images | Overlapping values with `deviceSizes` waste slots |
| `formats` | `['image/webp']` for cost savings | Dual formats double transformation count |
| `remotePatterns` | Only necessary hosts | Overly broad patterns may optimize unintended images |
| `unoptimized` | `false` in production | Should be `false` for prod, optionally `true` for dev |

**`deviceSizes` defaults:** `[640, 750, 828, 1080, 1200, 1920, 2048, 3840]`
**`imageSizes` defaults:** `[16, 32, 48, 64, 96, 128, 256, 384]`

Flag if:
- Any value appears in BOTH `deviceSizes` and `imageSizes`
- `deviceSizes` has values under 200 (these belong in `imageSizes`)
- Total entries across both exceed 16
- `formats` contains both `image/avif` and `image/webp`

### Step 2: Find All Image Component Usages

Search for all files importing and using `next/image`:

```
Grep: import.*from ['"]next/image['"]
```

For each file, extract every `<Image>` JSX usage with all props:
- `src` — static import or dynamic URL
- `fill` — responsive fill mode
- `width` / `height` — fixed dimensions
- `sizes` — responsive size hints
- `quality` — compression quality (default 75)
- `priority` — disables lazy loading for above-fold
- `loading` — lazy/eager
- `unoptimized` — per-image opt-out
- `placeholder` — blur/empty
- `className` — check for `object-fit` via CSS

### Step 3: Audit Each Image Instance

For every `<Image>` component found, check:

#### 3a. Missing `sizes` on `fill` images (P0 — Critical)

**Rule:** Every `<Image fill>` MUST have a `sizes` prop.

Without `sizes`, the browser assumes the image is `100vw` (full viewport width) and downloads the largest variant from `deviceSizes`. This wastes bandwidth AND triggers an expensive transformation.

**Fix:** Add `sizes` matching the actual rendered width at each breakpoint:

```tsx
// Image in a 3-column grid
<Image fill sizes="(max-width: 768px) 100vw, (max-width: 1024px) 50vw, 33vw" />

// Fixed-width container
<Image fill sizes="300px" />

// Responsive with max-width
<Image fill sizes="(max-width: 375px) 100vw, 375px" />
```

**How to determine correct `sizes`:**
1. Find the parent container's width constraints (Tailwind classes, CSS)
2. Map each breakpoint to the rendered image width
3. Express as media query list, smallest breakpoint first

#### 3b. Inaccurate `sizes` values (P1 — High)

**Rule:** The `sizes` value should match the actual rendered width, not be larger.

Check the parent container's width classes/styles and compare to the `sizes` hint. Flag if:
- `sizes` specifies a width larger than the container (over-fetching)
- `sizes` uses `100vw` but the image is in a constrained container

#### 3c. Small images that don't need optimization (P2 — Medium)

**Rule:** Images under ~10KB (icons, logos, SVGs) should use `unoptimized`.

```tsx
// Logos, tiny icons — skip optimization
<Image src="/logo.svg" unoptimized />
<Image src="/icon.png" width={24} height={24} unoptimized />
```

#### 3d. Missing `priority` on LCP images (P2 — Performance)

**Rule:** The largest above-the-fold image should have `priority`.

This doesn't affect billing but impacts Core Web Vitals (LCP). Check hero images, banner images, and first-visible content images.

#### 3e. Deprecated props (P3 — Cleanup)

**Rule:** Flag legacy `next/legacy/image` props used on `next/image`.

| Deprecated Prop | Replacement |
|---|---|
| `objectFit="cover"` | `className="object-cover"` |
| `objectFit="contain"` | `className="object-contain"` |
| `objectPosition` | `className="object-[position]"` |
| `layout` | Use `fill` or `width`/`height` |

### Step 4: Dev Server Optimization

**Rule:** Consider skipping optimization in development to avoid timeout errors.

The Next.js dev server optimizes images locally, which is slow for remote images and causes `TimeoutError` when loading many images concurrently.

**Recommended config:**

```ts
images: {
  unoptimized: process.env.NODE_ENV === 'development',
  // ... rest of config
}
```

This serves raw source URLs in dev (fast, no timeouts) while production on Vercel still gets full optimization.

---

## Output Format

### Configuration Audit

| Setting | Current | Recommended | Impact |
|---|---|---|---|
| `minimumCacheTTL` | ... | ... | ... |
| `deviceSizes` count | ... | ... | ... |
| `imageSizes` overlap | ... | ... | ... |
| `formats` | ... | ... | ... |
| Dev `unoptimized` | ... | ... | ... |

### Image Component Audit

Group findings by severity:

#### P0 — Missing `sizes` (Critical Cost Impact)

| File | Line | Container Width | Fix |
|---|---|---|---|
| ... | ... | ... | Add `sizes="..."` |

#### P1 — Inaccurate `sizes` (High Cost Impact)

| File | Line | `sizes` Value | Actual Width | Fix |
|---|---|---|---|---|
| ... | ... | ... | ... | Change to `"..."` |

#### P2 — Optimization Opportunities

| File | Line | Issue | Fix |
|---|---|---|---|
| ... | ... | Small image not `unoptimized` | Add `unoptimized` |
| ... | ... | LCP image missing `priority` | Add `priority` |

#### P3 — Deprecated Props

| File | Line | Deprecated | Replacement |
|---|---|---|---|
| ... | ... | `objectFit="cover"` | `className="object-cover"` |

### Cost Reduction Estimate

Summarize potential savings:
- Fixing missing `sizes`: prevents ~X unnecessary large-variant transformations
- Trimming `deviceSizes`: reduces variants per image from X to Y
- Single format: halves transformation count from dual-format
- Higher `minimumCacheTTL`: reduces re-transformation frequency

---

## References

- [Vercel Image Optimization Pricing](https://vercel.com/docs/image-optimization/limits-and-pricing)
- [Vercel Managing Image Optimization Costs](https://vercel.com/docs/image-optimization/managing-image-optimization-costs)
- [Next.js Image Component API](https://nextjs.org/docs/app/api-reference/components/image)
- [Reducing Bandwidth with next/image](https://vercel.com/templates/next.js/reduce-image-bandwidth-usage)
