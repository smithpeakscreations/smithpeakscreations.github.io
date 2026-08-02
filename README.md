# smithpeakscreations.github.io

Static site for [smithpeakscreations.com](https://smithpeakscreations.com), served from
`main` by GitHub Pages. Its primary job is hosting publicly-reachable privacy-policy
URLs so iOS apps pass App Store Connect review.

## Structure

```
index.html              Landing page — hero, app grid, contact
404.html                Not-found page
privacy/*.html          One policy per app
assets/site.css         The entire design system (tokens, components, dark mode)
assets/og.html          Source for the link-preview card
assets/og.png           Rendered link-preview card (1200x630)
CNAME                   Custom domain
```

## Rules for editing

**No build step.** Files are committed as-is and served as-is. Everything works when
opened straight off disk (`file://`). Hand-editing one file is the whole workflow — no
npm, no bundler, no framework.

**These three URLs are immutable.** They are submitted inside App Store Connect
listings. Do not rename, move, or extension-strip these files:

- `/privacy/scorekeeppro.html`
- `/privacy/thesportpulse.html`
- `/privacy/homecentered.html`

**No external requests.** No CDN, no webfont, no analytics. A privacy policy page
should not phone anyone. Imagery is inline SVG, CSS, or a data URI. If you add
something that makes a network request, you have broken the point of the site.

**Zero JavaScript.** Dark mode is `prefers-color-scheme` only; a manual toggle would
require JS.

**Legal copy is the product.** Restyle it, re-chunk it, add anchors — but do not
delete, summarize away, or reword substantive clauses. Every page keeps one `<h1>`, its
`<h2>`/`<h3>` hierarchy, and a visible "Last updated" date near the top, and must stay
readable with CSS disabled.

## Regenerating the OG image

`assets/og.png` is the one binary asset, rendered once from `assets/og.html`:

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless --disable-gpu --hide-scrollbars \
  --window-size=1200,630 \
  --screenshot=assets/og.png \
  "file://$PWD/assets/og.html"
```

## Previewing locally

```bash
python3 -m http.server 8000
```

## Known follow-up

The ScoreKeep Pro and The Sport Pulse policies are still unedited output from a policy
generator, and describe data collection that may not match what those apps actually do.
Home Centered's policy is the rewritten standard the other two should be brought up to.
