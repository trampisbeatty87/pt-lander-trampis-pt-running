# pt-lander-template

A single-page, conversion-focused **landing page** template for physical therapy clinics.
Built on the same zero-dependency engine as `pt-website-template` — all client content lives in
`content.json`; `build.js` renders it to static HTML in `dist/`. **You never edit `build.js`.**

This is the *lander* product (one focused page driving a single offer), distinct from the
full multi-page website template.

## The focus model

Every lander is built around one **focus** — the audience/angle the page speaks to:

| `focus` value   | Audience                                         |
|-----------------|--------------------------------------------------|
| `running`       | Runners with pain or recurring injuries          |
| `fitness`       | General fitness / active adults                  |
| `golf`          | Golfers (rotation, mobility, swing-related pain) |
| `pt-didnt-work` | People who tried PT before and it failed them    |

The `focus` field in `content.json` documents the angle; the orchestrator seeds the copy from it.
The page **structure** is identical across focuses — only the copy and images change.

## Page structure (top to bottom)

`hero` → `checklist` (pain points) → `ctaBand` → `testimonials` → `cards` (don't let it derail you)
→ `cards#why-us` → `ctaBand` → `steps#how-it-works` → `testimonials#reviews` → `ctaBand` → footer.

The offer CTA repeats ~5×. Nav anchors (`#why-us`, `#how-it-works`, `#reviews`) jump to the
matching sections (any block can take an optional `"id"`).

## What to swap per client (all in `content.json`)

- `brand.name`, `brand.logoText`, `brand.logoImage`, `brand.city`, `brand.phone`, `brand.email`
- `brand.colors.*` — branding (injected as CSS variables)
- `brand.primaryCta` / `brand.headerCta` — the offer button text
- `brand.primaryCtaUrl` — **the booking destination** every CTA points to (HubSpot meeting link, etc.)
- `brand.offer` — the offer headline + detail
- The `hero`, `testimonials`, `steps`, and `cards` copy
- Images in `assets/` (`hero.jpg`, `step-1.jpg`, `step-2.jpg`, `step-3.jpg`) — filled from the
  client's Google Drive folder by the orchestrator. The page renders fine without them (graceful
  fallbacks), so a missing image never breaks the build.

## Reviews / social proof (the `testimonials` block)

One block, three modes — the form/orchestrator picks based on what the client has:

| Client situation | Set in `content.json` | Result |
|---|---|---|
| Already pays for a reviews widget | `"widgetHtml"` = their embed code | Live widget renders in place of cards |
| Has Google reviews, no widget | `"items"` = curated reviews + `"googleUrl"` | Review cards **+** "Read our Google reviews" link-out |
| No reviews yet | a few real `"items"` (or nothing) | Cards only — or the section omits itself entirely |

Rules: we **never fabricate reviews** — `items` are always reviews the client actually provided.
`widgetHtml` wins over `items` if both are set. With none of `items`/`widgetHtml`/`googleUrl`, the
section renders nothing (no empty grid). `googleCta` overrides the link-out label.

## Develop locally

```bash
node build.js     # renders dist/
node serve.js      # builds + serves at http://localhost:8092
```

## Deploy

Netlify runs `node build.js` and publishes `dist/` (see `netlify.toml`, Node 20 pinned).
Used as a GitHub **template repo** — the orchestrator spins off one repo + one Netlify site per
client, commits a client-specific `content.json` + assets, and Netlify auto-builds.
