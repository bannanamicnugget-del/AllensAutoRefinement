# Demo Content Registry & Demo Notes

This site is a **free demo / proposal build** (Website Prompt Pack §0A). The design and features are finished;
every fact about the business that isn't known for certain is **sample content** and is listed here.
Nothing on the site is a fabricated review, rating, award, licence number, statistic or staff name.

## How sample content is marked
- **On the page:** placeholder facts render as dashed amber "placeholder" chips. Every one carries `data-demo="true"`.
- **Demo Preview banner:** a thin banner at the top of every public page (`data-demo="true"`). It shows automatically
  while `config/business.json` still contains any `PLACEHOLDER` value and disappears on its own once all are replaced.
- **Demo vehicle listings:** 3 sample vehicles (`is_demo = true` in the database) with a "Demo listing" badge and banner.
  They're `noindex`, left out of the sitemap and out of Car/Offer structured data.
- **Search engines:** demo, deploy-preview and branch deploys send `noindex` (meta tag + `X-Robots-Tag` header) and a
  `Disallow: /` robots.txt until `ALLOW_INDEXING=true` is set on the production site.
- **Check what's left any time:** `npm run demo:check` (add `-- --strict` to fail if anything remains).

## Registry
All business facts live in **one file**: `config/business.json`. Fill it in once and it flows to every page,
the footer, the JSON-LD structured data, `security.txt` and `llms.txt`.

| # | Page(s) / location | What it is now | What real info replaces it (`config/business.json` key) |
|---|---|---|---|
| 1 | Footer, Contact, JSON-LD | Placeholder street address and ZIP | `address.street`, `address.postalCode` |
| 2 | JSON-LD (map pin) | Placeholder coordinates | `geo.latitude`, `geo.longitude` |
| 3 | Header call button, footer, Contact, mobile bar | Placeholder phone | `phone` |
| 4 | Footer, Contact, `security.txt` | Placeholder email | `email` |
| 5 | Privacy policy | Placeholder privacy-request email | `privacyEmail` |
| 6 | Footer, Contact, JSON-LD | Placeholder opening hours (Mon–Fri, Sat, Sun) | `hours[].opens`, `hours[].closes` |
| 7 | Contact, footer | Placeholder hours note | `hoursNote` |
| 8 | Inventory pages, Terms, JSON-LD | Payment methods beyond cash | `paymentMethods` |
| 9 | JSON-LD (area served) | Placeholder service area | `serviceArea` |
| 10 | Footer, inventory pages, Terms | Virginia MVDB dealer certificate number | `dealerLicense` |
| 11 | Vehicle pages, Terms | As-is / warranty disclosure (attorney to approve) | `asIsDisclosure` |
| 12 | About | Headline, owner story, owner name | `about.headline`, `about.story`, `about.owner` |
| 13 | Footer, JSON-LD `sameAs` | Facebook, Instagram, Google Business Profile links | `social.*` |
| 14 | Privacy, Terms | Policy effective date, legal entity name | `legal.effectiveDate`, `legal.entityName` |
| 15 | `/inventory`, home "Featured" | 3 demo vehicles (2024 Toyota Tacoma, 2023 Toyota Corolla Cross, 2020 Dodge Charger) | Real listings added in the staff portal; then `npm run clear-demo` |
| 16 | Home hero, About, Services, appraisal section | Decorative vehicle photos (`public/images/*.png`) | Fine to keep as mood imagery. Confirm the source/licence; replace with real shop photos if you want to show real work. Never caption them as the client's own cars or jobs. |

## What we need from the business (Demo Notes for the buyer)
1. The facts in rows 1–14 (10 minutes on a call is usually enough).
2. Real photos of the shop, team or finished work, if they want them shown (optional).
3. Their accepted payment methods and the as-is / warranty wording their attorney approves.
4. Dealer licence details, if they sell vehicles (Virginia MVDB).
5. Who should receive lead emails (for the optional email alerts).
6. Their domain name (and DNS access) for go-live.

## Demo-to-live gate (launch is blocked until every box is ticked)
- [ ] `npm run demo:check -- --strict` passes (no placeholders left) — the Demo Preview banner and chips are gone.
- [ ] Demo listings removed (`npm run clear-demo` or delete them in the staff portal).
- [ ] `SITE_URL` set to the real domain in Netlify; custom domain + HTTPS live.
- [ ] `ALLOW_INDEXING=true` set in Netlify for the **Production** context, then redeploy (removes noindex, restores the real robots.txt and sitemap reference).
- [ ] Privacy policy and Terms reviewed by a Virginia-licensed attorney.
- [ ] Client confirms in writing that every photo, claim and detail on the site is real and permitted.
