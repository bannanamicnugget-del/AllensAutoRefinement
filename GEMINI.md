# Rules for AI editors (Google AI Studio / Gemini / Antigravity, Codex, Claude, Cursor…)

This repo is a **working Netlify site**. GitHub is the source of truth and Netlify is where it runs.
Edit it in place. Do **not** convert it to another stack.

## Never do this
- Don't migrate it to React, Vite, Next.js, Angular or TypeScript, and don't add an `index.html` or `src/main.*` at the repo root. Pages are prerendered by `scripts/build.mjs` from `src/views/**` into `dist/`.
- Don't delete, rename or move: `netlify.toml`, `netlify/functions/`, `migrations/`, `config/`, `scripts/build.mjs`, `scripts/dev.mjs`, `scripts/local-server.mjs`, `package-lock.json`, `metadata.json`.
- Don't change `[build] command = "npm run build"`, `publish = "dist"` or `[functions] directory = "netlify/functions"`.
- Don't add inline `<script>` blocks, inline `style="…"` attributes, `on*=` handlers, CDN scripts/fonts or external images. The Content-Security-Policy (`src/lib/security.mjs` and `netlify.toml`, which must stay identical) is `'self'` only, so those break on the live site. Put CSS in `public/css/*.css` and JS in `public/js/*.js`.
- Don't put real secrets, passwords or API keys in any file. Secrets live in Netlify environment variables (AI Studio Secrets for the preview only).
- Don't invent reviews, ratings, prices, awards, licence numbers, years in business, staff names or business facts. Unknown facts stay as `PLACEHOLDER — …` values in `config/business.json` (they render as dashed "placeholder" chips and are kept out of structured data).
- Don't edit `dist/` or `src/generated/` (build output, git-ignored). Don't commit `node_modules/`, `.env`, `.pglite/` or `.blobs/`.
- Don't re-encode or rewrite the binary files in `public/images`, `public/fonts` or `public/icons` unless asked to replace them.

## Where things go
- Business details (name, phone, hours, address, about text, legal entity): `config/business.json` only.
- Page markup: `src/views/pages/*.mjs`, shared pieces in `src/views/components.mjs` / `layout.mjs`. Use the `html` tag from `src/lib/html.mjs` (it escapes by default).
- New server endpoint: a new file in `netlify/functions/` exporting `default` (a `handler(...)` from `src/lib/http.mjs`) and `export const config = { path: '/api/v1/…', method: [...] }`.
- Database change: a new numbered file `migrations/00N_description.sql` (additive only; never edit an old migration).
- Feature switches: `config/features.json`.

## Preview vs production
- `npm run dev` = zero-config preview on port 3000 (PGlite local database, demo listings, temporary secret). Local only.
- Netlify Functions features that only exist on Netlify (edge rate limits, cache purge, real Blobs storage, scheduled functions) are emulated or skipped in the preview. That's expected.

## Before you finish any change
1. `npm run lint`
2. `npm test` (builds, then runs 40+ tests: SEO tags, CSP, forms, auth, inventory, icons)
3. Note the change in `CHANGELOG.md`.
