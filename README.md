# Estudio Altiplano — studio website

Bilingual (ES/EN) single-page site for the web studio. Zero build step, zero dependencies —
one HTML file with inline CSS and vanilla JS. Deploys as static files to Cloudflare Pages.

**This is project #1.** It's your sales tool, your live demo, and the thing you point clients at.

---

## Contact mode: email or WhatsApp

The site runs in **email-only mode** by default: `CONFIG.whatsapp` is an empty string, so every
CTA becomes a prefilled `mailto:`, the floating button stays hidden, and the button copy reads
"Escríbenos por correo".

To switch the whole site to WhatsApp, set one value in [index.html](index.html):

```js
whatsapp: "5214441234567"   // country code + number, digits only
```

That single change flips the icons, the button labels, the floating button and the per-package
prefilled messages. Nothing else to edit.

---

## Before you deploy: 3 find-and-replace edits

| # | Find | Replace with | Where |
|---|---|---|---|
| 1 | `Estudio Altiplano` | Your real studio name | [index.html](index.html), [404.html](404.html) — 8 occurrences |
| 2 | `hola@estudioaltiplano.mx` | Your real email | [index.html](index.html) — `CONFIG.email` + footer + `mailto:` |
| 3 | `estudioaltiplano.mx` | Your real domain | [index.html](index.html), [robots.txt](robots.txt), [sitemap.xml](sitemap.xml) |

Also swap the inline SVG logo mark (the orange chevron) once you have a real logo.

---

## How the ES/EN toggle works

No framework, no JSON files, no page reload.

- Spanish is the source of truth: it's the visible text in the HTML.
- Any element that needs translating carries a `data-en` attribute with the English string.
- On load, the script caches each element's Spanish text into `data-es`, so switching back is lossless.
- `setLang()` swaps `textContent`, updates `<html lang>`, the meta description, the WhatsApp
  message language, and `aria-pressed` on the toggle.
- The choice persists in `localStorage`. First-time visitors with an English browser get English.

**To add a new translatable string:** put the Spanish in the HTML, add `data-en="English here"`.
That's it — the element must be a *leaf* (no child elements), otherwise `textContent` will wipe
its children. Split into sibling `<span>`s when you need inline markup, like the hero headline does.

---

## Structure

```
Sitio-Estudio/
├─ index.html      ← the whole site
├─ 404.html
├─ robots.txt
├─ sitemap.xml
├─ _headers        ← security headers for Cloudflare Pages
└─ .gitignore
```

Sections: hero → services (12 capabilities) → packages (3, no prices) → process (6 steps) →
work → about → FAQ → CTA → footer.

---

## Run locally

```bash
cd "Sitio-Estudio"
python3 -m http.server 8000
# http://localhost:8000
```

Test on your phone over the same Wi-Fi:

```bash
ipconfig getifaddr en0    # e.g. 192.168.1.42 → http://192.168.1.42:8000
```

---

## Deploy to Cloudflare Pages

1. Push this folder to a GitHub repo (`sitio-estudio`).
2. Cloudflare dashboard → **Workers & Pages** → **Create** → **Pages** → **Connect to Git**.
3. Build settings: **framework preset = None**, **build command = empty**,
   **output directory = `/`**. There's no build step.
4. Deploy. You get `sitio-estudio.pages.dev` in about a minute.
5. **Custom domains** → add your domain. Cloudflare handles DNS and SSL automatically.

`_headers` is applied by Cloudflare on deploy — verify at [securityheaders.com](https://securityheaders.com).

> The CSP allows `'unsafe-inline'` because the CSS and JS are inline in the HTML. If you later
> move them to `assets/`, tighten the policy by dropping `'unsafe-inline'`.

---

## Pre-launch checklist

- [ ] All 4 find-and-replace edits done
- [ ] WhatsApp buttons open a chat with the right number and a prefilled message (test on a real phone)
- [ ] Language toggle works both directions, on every section
- [ ] Real logo in place of the placeholder chevron
- [ ] `assets/img/og.jpg` created (1200×630) — the `og:image` currently points at a file that doesn't exist yet
- [ ] Lighthouse ≥ 90 performance, ≥ 95 accessibility, 100 SEO
- [ ] Tested at 320 px and on a real Android phone throttled to Slow 4G
- [ ] Domain in your name, auto-renew ON
- [ ] Submitted to Google Search Console

---

## Deliberate decisions

**Packages show no prices.** Every business needs something different, and a public price
either scares off a good client or anchors you low. The CTA sends a WhatsApp with the package
name prefilled, so you know what they clicked before you reply.

**Google Fonts is loaded from the CDN.** Fine for now. When you want the last few Lighthouse
points, self-host with [gwfh.mranftl.com](https://gwfh.mranftl.com) and drop `fonts.googleapis.com`
from the CSP.

**No contact form.** In Mexico, WhatsApp converts better than a form, and skipping the form
avoids triggering the LFPDPPP privacy-notice obligation. Add one only when a client asks.

**No analytics yet.** Add the Cloudflare Web Analytics snippet after deploy — it's cookieless,
so no consent banner.
