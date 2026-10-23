# Changelog
All notable changes to this project are documented here. Versions follow [Semantic Versioning](https://semver.org/).
API endpoints are versioned separately under `/api/v1`; breaking API changes will ship as `/api/v2` alongside v1.

## [1.3.0] — 2026-10-23
### Added
- **Google AI Studio import:** `metadata.json`; `npm run dev` / `npm start` now run `scripts/dev.mjs`, a zero-config preview on port 3000 (builds, local PGlite database, migrations, demo listings, temporary JWT secret, Netlify-style routing and headers). No environment variables required. Optional `PREVIEW_ADMIN_PASSWORD` creates a preview-only `preview-admin` login.
- Embedded-preview mode in `scripts/local-server.mjs` (`PREVIEW_EMBED=true`, set by `npm run dev`): the page can render inside AI Studio's preview iframe, the public origin is read from proxy headers so form/login Origin + CSRF checks keep working, and cookies get `SameSite=None; Secure; Partitioned` behind HTTPS. Local only; Netlify headers are unchanged.
- `GEMINI.md` + `AGENTS.md`: editing rules for AI tools (no stack migration, keep `netlify.toml`/functions, strict CSP, no secrets, no invented facts, lint + test before finishing).
- `.gitattributes` (LF line endings, binary assets marked) so Windows/GitHub/AI Studio round-trips can't corrupt files.
- Demo-first gate (Prompt Pack §0A/§0B): deploy previews and branch deploys are always `noindex`; production stays `noindex` (meta + `X-Robots-Tag` via generated `dist/_headers` and every function response, `Disallow: /` robots.txt) until `ALLOW_INDEXING=true`.
- Demo Preview banner (auto-shown while `config/business.json` has placeholders, auto-removed when they're filled); `data-demo="true"` on placeholder chips and demo-listing badges; `DEMO_CONTENT_REGISTRY.md` (registry, buyer notes, demo-to-live gate); `npm run demo:check [-- --strict]`.
- Build now writes `/.well-known/security.txt`, `/llms.txt` and `/humans.txt`.
- README: "Needs from the owner" checklist and an AI Studio → GitHub → Netlify quick start; `.env.example` documents `ALLOW_INDEXING` and preview-only variables.
- Tests (43): AI Studio/Netlify portability guards, every function exports a handler + config, indexing-gate matrix, demo tagging, security/llms files, embedded-preview framing + origin checks, and that the plain local server keeps `X-Frame-Options: DENY`.
### Changed
- Node **22** for builds, functions and preview (`.nvmrc`, `netlify.toml`, `engines >=22.12`). Node 20 is end-of-life and Netlify swaps functions off it, so build and runtime could drift. Dependencies unchanged.
- `netlify dev` moved to `npm run dev:netlify` (builds first; `[dev] framework = "#static"` in `netlify.toml` stops the CLI auto-running the preview server).

## [1.2.0] — 2026-10-22
### Added
- Official Allen's Auto Refinement badge logo (`public/images/logo.png`) replaces the drawn "A" mark: header nav (48 px, 44 px on phones, wordmark beside it from 600 px up), footer (96 px), admin login (88 px) and admin sidebar (44 px); alt text "Allen's Auto Refinement logo". Built to AVIF + WebP at 48/96/192/384 px via the existing sharp pipeline (cached between builds).
- Favicons + web app manifest (`public/icons/`): `/favicon.ico` (16/32/48), SVG, 16/32/48 PNG, 180 px apple-touch-icon, 192/512 Android icons and `/site.webmanifest` at the publish root, plus fingerprinted copies under `/assets/icons/`.
- Shared `iconLinks()` head block (`src/views/head.mjs`) on every document — prerendered pages, 404/500, thank-you, admin shell and function-rendered inventory pages: SVG / 32 / 16 / `.ico` icon links, apple-touch-icon, manifest and `theme-color` `#1b3a5c` (replaces the old `#0b0b0d`).
- Page-load intro: on the first page of a visit the header badge scales in followed by a soft light sweep (also on hover/focus); the admin login badge does the same. Disabled under `prefers-reduced-motion` and forced colours; CSS only, no layout shift.
- Default `og:image` / `twitter:image` is now a 1200×630 logo card (absolute URL from `SITE_URL`) with width/height/alt; vehicle pages keep their own photo. AutoDealer JSON-LD `logo` is a 512 px PNG `ImageObject`; `image` lists the logo then the hero photo.
- Tests (36): every page — public, admin, 404/500, thank-you, function-rendered `/inventory`, `/inventory/:slug` and the function 404 — has the icon links, manifest and a single theme-color, and every linked file exists; manifest icons exist; `favicon.ico` is a real 3-size ICO; header/footer badge markup + alt; OG/JSON-LD logo URLs; cache-header rules.
### Changed
- `netlify.toml`: `/favicon.ico` and `/site.webmanifest` cached 1 day (+ explicit `Content-Type`), other unhashed root icons 1 week — never immutable; hashed logo/icon copies fall under the existing `/assets/*` 1-year immutable rule. CSP unchanged (`manifest-src 'self'` and `img-src 'self'` already cover it).
- Local server serves `.ico`, `.webmanifest` and `.avif` with correct MIME types; smoke test also checks `/favicon.ico` and `/site.webmanifest`.
### Removed
- Build-generated placeholder `favicon.svg` and the hero-photo Open Graph JPEG.

## [1.1.0] — 2026-10-22
### Added
- Ambient "liquid clearcoat" background on every page via the shared layout (`ambientLayer()` in `src/views/layout.mjs`; admin shell gets a calm variant): fixed full-viewport WebGL fragment shader (`public/js/ambient.js`, external ES module, fingerprinted) — midnight/graphite paint with domain-warped flow (~45 s drift), faint iridescent metallic flakes, two broad showroom-light reflections gliding across (~38 s / ~55 s), flowing aero contour lines, cursor-following glow (fine pointers only), fine film grain (CSS-scrolled noise tile).
- Per-page and per-section moods with ~1.4 s crossfades driven by IntersectionObserver (`data-mood`: warm near Services/Restoration, steel blue near Inventory/Sell, bright near Protection; page default from the path).
- Extras: light-beam dust motes in the home hero (2D canvas, runs only while the hero is visible), hydrophobic water-bead shimmer on the Protection cards, animated gradient seams between sections and above the footer, soft scrims behind text-heavy blocks.
- Test coverage: every prerendered page and function-rendered inventory page includes the layer and points at the built module; module is never loaded eagerly (33 tests).
### Performance & accessibility
- Module is lazy-loaded by `site.js` / `admin.js` after `load` + idle (does not affect LCP); renders at ½ CSS-pixel resolution (admin ⅓·, ~6 fps), capped at 30 fps, pauses on hidden tabs, stops on WebGL context loss; falls back to an animated CSS gradient without WebGL and to a static gradient with JS off, `prefers-reduced-motion`, Save-Data, forced colors and print. `pointer-events: none`, `position: fixed`, `contain: strict` — no layout shift.
- Shader output is soft-capped at ~12 % sRGB so all site text colours keep ≥ 4.5:1 contrast on top of it.
- No CSP change was needed (WebGL, CSSOM style writes and the `data:` grain tile are already allowed by the existing policy).

## [1.0.0] — 2026-10-22
### Added
- Netlify architecture: prerendered static marketing pages (`/`, `/services`, `/services/detailing|restoration|protection`, `/sell`, `/about`, `/contact`, `/privacy`, `/terms`, `/accessibility`, `404`, `500`) with unique meta, canonical, Open Graph/Twitter and JSON-LD.
- Server-rendered, edge-cached `/inventory` (query-string filters/sort/pagination) and `/inventory/:slug` (gallery, specs, Car + Offer JSON-LD, inquiry/viewing form); tag-based cache purge on staff edits.
- Dynamic `/sitemap.xml` (static pages + every published available/pending vehicle) and `robots.txt`.
- Staff portal at `/admin`: JWT access + rotating refresh tokens, roles, lockout, password change, staff users; dashboard KPIs; inventory CRUD with optimistic locking, statuses, mark sold, photos (Netlify Blobs, sharp re-encode), CSV export; cash-offer leads with "Accept & add to inventory"; buyer inquiries; service quotes; notes.
- Public forms with CSRF double-submit + Origin check, honeypot, time-trap, zod validation, idempotency keys, edge + DB rate limits; sell form with multi-step UI and photo upload.
- Postgres via Neon serverless driver; versioned migrations + `schema_version`; Shane seeded from the provided bcrypt hash.
- Email notifications (Resend or SMTP) with timeouts, backoff + jitter retries, circuit breaker, dead-letter queue and hourly scheduled retry; nightly JSON backups to Blobs (30 retained).
- Health check, structured JSON logs with request IDs, feature flags, CI workflow, Dependabot, docs (README, CHECKLIST, RUNBOOK, incident response + postmortem template).
- Demo inventory (3 vehicles, `is_demo`, labeled "Demo listing", noindex, excluded from sitemap/structured data) with `npm run seed-demo` / `npm run clear-demo`.
