# Leslie Cedar, LCSW — Deployment Notes

## What's in this build

```
leslie-site/
├── index.html                       Homepage
├── about.html                       About / bio / approach
├── services.html                    Services hub (links to 4)
├── services/
│   ├── individual-therapy.html
│   ├── trauma-therapy.html
│   ├── anxiety-therapy.html
│   └── depression-therapy.html
├── faq.html                         7 questions, FAQPage schema
├── contact.html                     NAP, hours, fees, embedded map, service area
├── 404.html
├── sitemap.xml                      All 9 indexable pages
├── robots.txt                       Allow all + sitemap
├── CNAME                            lesliecedartherapy.com
└── .nojekyll                        Tells GH Pages to skip Jekyll
```

## Push it to the repo

The current `losmanzanos/leslie` repo has a single `index.html`. Replace it with these files at the **root** of the repo (NOT in a subfolder).

```bash
# from your local clone of losmanzanos/leslie
git rm index.html
cp -r /path/to/leslie-site/* /path/to/leslie-site/.nojekyll ./
git add .
git commit -m "Full site build: 4 service pages, SEO, schema, sitemap"
git push origin main
```

GitHub Pages will rebuild automatically. Within a minute it'll be live at
https://losmanzanos.github.io/leslie/

## Custom domain cutover (lesliecedartherapy.com)

`CNAME` is already set to `lesliecedartherapy.com`. Once you push, GitHub Pages will pick it up. Then on her registrar:

1. **A records** for the apex (`lesliecedartherapy.com`) → these four GH Pages IPs:
   - `185.199.108.153`
   - `185.199.109.153`
   - `185.199.110.153`
   - `185.199.111.153`
2. **CNAME** for `www` → `losmanzanos.github.io`
3. In the repo: Settings → Pages → enforce HTTPS once the cert provisions (usually 10–60 min).
4. Cancel WordPress hosting **after** DNS confirms (use `dig lesliecedartherapy.com` or check GH Pages dashboard).

⚠️ One thing to know: every page's `<link rel="canonical">` and OG `og:url` already point to `https://lesliecedartherapy.com/...`. That's intentional — it tells Google the production domain even while the GH Pages staging URL is also reachable. If you'd rather wait to push canonicals until DNS is live, do a global find-and-replace of `https://lesliecedartherapy.com` → `https://losmanzanos.github.io/leslie` before pushing, then revert after cutover.

## Pending image swaps (drop-in)

Two filenames are referenced but not yet on disk — drop the files into `/assets/` to activate:

- **`assets/accent-footer-corner.png`** — the new clock + flowers torn-corner image you sent. Goes in the footer of every page (top-right, rotated 180°). Already wired up; just save the PNG (with transparent background) at this path. Recommend ~1200px wide.
- (Optional) **`assets/service-individual-poppy-wide.jpg`** — already auto-generated from your existing poppy source by tightening the crop. If you'd rather use the wheat image you sent, save it at that exact path (1600×960 landscape works well) and the existing reference picks it up.

## Image hosting — IMPORTANT before cancelling WordPress

Several images are still hot-linked from the existing WordPress install:

- `https://lesliecedartherapy.com/wp-content/uploads/2025/07/IMG_9210.jpg` — Leslie's headshot (about.html, index.html)
- `https://lesliecedartherapy.com/wp-content/uploads/2025/07/IMG_9015-crop-1.png` — Individual therapy hero
- `https://lesliecedartherapy.com/wp-content/uploads/2025/07/cd3ebba2f52563b8d7eeb28641affe66-1-crop.jpeg` — Trauma hero
- `https://lesliecedartherapy.com/wp-content/uploads/2025/07/walk-on-the-beach-2872037_1280-1.jpg` — Anxiety hero
- `https://lesliecedartherapy.com/wp-content/uploads/2025/06/aron-yigin-ym-2_JVXO9k-unsplash-scaled.jpg` — Depression hero

**These will break the moment WP is cancelled** because, once DNS points to GH Pages, the `/wp-content/...` paths return 404 from the static site.

**Fix before cutover:** download these into `/assets/` in the repo and search-replace the URLs to `https://lesliecedartherapy.com/assets/<filename>`. Quick recipe:

```bash
mkdir -p assets
for url in \
  "https://lesliecedartherapy.com/wp-content/uploads/2025/07/IMG_9210.jpg" \
  "https://lesliecedartherapy.com/wp-content/uploads/2025/07/IMG_9015-crop-1.png" \
  "https://lesliecedartherapy.com/wp-content/uploads/2025/07/cd3ebba2f52563b8d7eeb28641affe66-1-crop.jpeg" \
  "https://lesliecedartherapy.com/wp-content/uploads/2025/07/walk-on-the-beach-2872037_1280-1.jpg" \
  "https://lesliecedartherapy.com/wp-content/uploads/2025/06/aron-yigin-ym-2_JVXO9k-unsplash-scaled.jpg"
do
  curl -L -o "assets/$(basename "$url")" "$url"
done
# Then in each .html file, replace the wp-content URLs with /assets/<filename>
```

Or — easier — just send me/Claude the photos and we'll batch the swap.

## SEO setup (do these once you're live)

The site is fully optimized client-side: titles, meta descriptions, keywords, canonicals, Open Graph, Twitter cards, JSON-LD for `LocalBusiness`/`MedicalBusiness`, `Person`, `Service` × 4, `FAQPage`, `BreadcrumbList`, `WebSite`, `ContactPage`. To turn that into rankings:

1. **Google Search Console** — add `lesliecedartherapy.com`, verify (DNS TXT or HTML file), and submit `/sitemap.xml`. Request indexing on the homepage and each service page individually after the first crawl.
2. **Google Business Profile** — claim/update the listing. NAP must match exactly: `Leslie Cedar, LCSW · 455 Fairway Drive, Deerfield Beach, FL 33441 · 954-370-3350`. Tag the categories `Psychotherapist`, `Mental Health Service`. Add photos, hours, services, and link back to the relevant page on the site.
3. **Bing Webmaster Tools** — same drill. Free traffic.
4. **Citations / NAP consistency** — Psychology Today (already verified), TherapyDen, GoodTherapy, Yelp, Healthgrades, Yellow Pages. Make sure the address and phone are byte-identical everywhere.
5. **Reviews** — even 3–5 Google reviews would dramatically lift local ranking. Most therapists have an HIPAA-compliant way to ask satisfied clients (or referring colleagues) for a review of the practice itself, not the clinical experience.
6. **Backlinks** — Florida Society for Clinical Social Work directory, NASW-Florida, Columbia alumni directory, any local Boca/Deerfield community boards. A handful of relevant local links beats dozens of low-quality ones.

## Things you may want to swap

- **Hero image (homepage)** — currently a soft-light Unsplash photo. If she has a preferred photo, drop it into `/assets/hero.jpg` and update `index.html`.
- **Headshot** — using the existing WP photo. Upgrade per the asset section above.
- **OG share images** (`/assets/og-home.jpg`, `og-services.jpg`, etc.) — these paths are referenced but the files don't exist yet. Either create 1200×630 JPGs and drop them in `/assets/`, or remove the `og:image` references on pages where you don't have one. (Not site-breaking — just means link previews on Facebook/iMessage/LinkedIn will be bare.)
- **Analytics** — no GTM/GA installed. If she wants to keep analytics, paste her GA4 measurement tag into the `<head>` of every page (or just the homepage if she's only tracking visits).
- **Contact form (Formspree wire-up)** — `contact.html` now has a full form ready to go. To activate it:
  1. Sign up at [formspree.io](https://formspree.io), create a new form, point it at `lcedartherapy@gmail.com`.
  2. Copy the form's endpoint ID (looks like `xyzabc123`).
  3. In `contact.html`, search for `YOUR_FORM_ID` and replace with the real ID.
  4. That's it. The JS already handles AJAX submit + inline thank-you, with graceful fallback to a native POST if you forget the JS. A honeypot field is included for spam.

## Style/system notes

- Tailwind via CDN — no build step. If you ever want to bundle/minify, drop in a Vite or Eleventy setup later.
- Fonts: Inter + Playfair Display (Google Fonts).
- Color palette: `#f7f5f2` page bg, `#ece7df` / `#f1ede7` / `#ebe6de` accent sections, `#faf8f4` cards, `#e7dfd5` borders, `#1e1e1e` text & primary buttons, `#555` secondary text, `#777` tertiary.
- Mobile menu, sticky header with backdrop blur, soft-shadow cards, the dotted gridline section pattern from the original — all carried through every page.

## Quick local preview

```bash
cd leslie-site
python3 -m http.server 8000
# open http://localhost:8000
```
