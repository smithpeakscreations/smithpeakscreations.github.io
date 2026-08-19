# smithpeakscreations.github.io

Static site for [smithpeakscreations.com](https://smithpeakscreations.com), served from
`main` by GitHub Pages. It is the public home for four iOS apps: a landing page per app
carrying an App Store link, an FAQ and a support address, plus the publicly-reachable
privacy-policy and support URLs that App Store Connect requires.

## Structure

```
index.html                    Landing page — hero, app grid, contact
apps/*.html                   One landing page per app — badge, features, FAQ, support
privacy/*.html                One policy per app
404.html                      Not-found page
sitemap.xml, robots.txt       Discovery
assets/site.css               The entire design system (tokens, components, dark mode)
assets/og.html                Source for the link-preview card
assets/og.png                 Rendered link-preview card (1200x630)
assets/icons/*.png            App icons for the homepage cards (144x144, shown at 48px)
assets/icons/*-256.png        App icons for the app-page heroes (256x256, shown at 96px)
assets/appstore-badge-*.svg   Apple's official badge art, black + white variants
CNAME                         Custom domain
```

## The apps

Slugs are shared across `apps/`, `privacy/`, and `assets/icons/` — one slug per app,
everywhere.

| App | Slug | App Store ID | Price | Support address |
|---|---|---|---|---|
| ScoreKeep Pro | `scorekeeppro` | `6744083508` | Free | `smithpeakscreations+skprosupport@gmail.com` |
| The Sport Pulse | `thesportpulse` | `6740729768` | $1.99 | `smithpeakscreations+spsupport@gmail.com` |
| Home Centered | `homecentered` | `6797084066` | Free | `smithpeakscreations+hcsupport@gmail.com` |
| 365 Strong | `365strong` | `6798088194` | Free | `smithpeakscreations+365strongsupport@gmail.com` |

Store links use the short canonical form `https://apps.apple.com/app/id<ID>` with no
country segment, so they localise to the visitor's storefront.

## Rules for editing

**No build step.** Files are committed as-is and served as-is. Everything works when
opened straight off disk (`file://`). Hand-editing one file is the whole workflow — no
npm, no bundler, no framework.

**These eight URLs are immutable.** They are submitted inside App Store Connect
listings — the `privacy/` files as each app's Privacy Policy URL, the `apps/` files as its
Support URL — and `365strong.html` is additionally hard-coded in the 365 Strong app under
Profile &rarr; About &rarr; Privacy Policy. Do not rename, move, or extension-strip these
files:

- `/privacy/scorekeeppro.html` &nbsp;&nbsp; `/apps/scorekeeppro.html`
- `/privacy/thesportpulse.html` &nbsp;&nbsp; `/apps/thesportpulse.html`
- `/privacy/homecentered.html` &nbsp;&nbsp; `/apps/homecentered.html`
- `/privacy/365strong.html` &nbsp;&nbsp; `/apps/365strong.html`

Apple surfaces the Support URL on every product page, and a missing or dead one is a
rejection risk under Guideline 1.5. If one of these pages has to move, update App Store
Connect in the same sitting.

**No external requests.** No CDN, no webfont, no analytics. A privacy policy page
should not phone anyone. Imagery is inline SVG, CSS, a data URI, or a file committed
under `assets/`. If you add something that makes an *outbound* request, you have broken
the point of the site.

**The App Store badge is Apple's artwork.** `assets/appstore-badge-black.svg` and
`-white.svg` come from Apple's marketing toolbox and must be used unmodified — no
recolouring, no redrawing, no squashing, and never below 40pt wide. The black variant is
the default; the white one is swapped in for dark mode by a `<picture>` media source,
which needs no JavaScript:

```html
<picture>
    <source srcset="../assets/appstore-badge-white.svg" media="(prefers-color-scheme: dark)">
    <img class="badge-appstore" src="../assets/appstore-badge-black.svg"
         width="150" height="50" alt="Download <App> on the App Store" decoding="async">
</picture>
```

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

## Adding or updating an app icon

Icons live in `assets/icons/`, named after the app's slug, in two sizes. Source both from
the app's `Assets.xcassets/AppIcon.appiconset/` 1024×1024 master:

```bash
sips -s format png -Z 144 /path/to/AppIcon.appiconset/1024.png --out assets/icons/<slug>.png
sips -s format png -Z 256 /path/to/AppIcon.appiconset/1024.png --out assets/icons/<slug>-256.png
```

`<slug>.png` is 144×144 for the homepage card (3× its 48px display size);
`<slug>-256.png` is 256×256 for the app-page hero (~2.7× its 96px display size).

App Store icons are opaque squares — iOS applies its own rounding, and `.app-icon`
rounds to 12px here. If a master has an alpha channel, flatten it first or it will show
the card through and vanish in dark mode:

```bash
sips -g hasAlpha /path/to/icon.png
```

`assets/icons/scorekeeppro.png` was flattened onto white this way, since its master is a
transparent monogram.

For an app with no icon yet, use the striped placeholder instead of an `<img>`:

```html
<div class="app-icon app-icon-placeholder" aria-hidden="true">app<br>icon</div>
```

## Adding an app

Six steps, in this order. Every slug is the same string throughout.

1. `assets/icons/<slug>.png` at 144x144 for the homepage card, and
   `assets/icons/<slug>-256.png` at 256x256 for the app-page hero. Both come from the
   1024x1024 master (see below).
2. `privacy/<slug>.html` — copy `privacy/homecentered.html` and rewrite the body.
3. `apps/<slug>.html` — copy an existing app page. Update the head block (title,
   description, absolute `og:url`), the hero (icon, eyebrow, tagline, price/iOS/version
   line, badge `id`), the features, the FAQ, and the support address.
4. A card in `index.html`, linking to `apps/<slug>.html`. Keep exactly one `<a>` inside
   the card — see the note in `assets/site.css` under "App card".
5. Two entries in `sitemap.xml`.
6. In App Store Connect, set the **Support URL** to
   `https://smithpeakscreations.com/apps/<slug>.html` and the **Privacy Policy URL** to
   `https://smithpeakscreations.com/privacy/<slug>.html`.

## Adding app screenshots

Each `apps/*.html` has a commented-out `.shot-gallery` block marking where they go.
Uncomment it, then commit one file per shot under `assets/screenshots/<slug>/`. Give
every shot a real `alt` describing the screen, not "screenshot 1".

The shots already on the App Store can be pulled at full resolution from Apple's CDN —
`itunes.apple.com/lookup?id=<ID>` returns `screenshotUrls`, and swapping the trailing
`/320x480bb.jpg` for `/626x0w.png` gives a 626x1360 PNG, which is 2x the 230px display
width. Export fresh from Xcode instead if you'd rather.

## Previewing locally

```bash
python3 -m http.server 8000
```

## Known follow-up

The ScoreKeep Pro and The Sport Pulse policies are still unedited output from a policy
generator, and describe data collection that may not match what those apps actually do.
Home Centered's policy is the rewritten standard the other two should be brought up to.

Because of that, `apps/scorekeeppro.html` and `apps/thesportpulse.html` deliberately make
no privacy claims of their own — their "How is my data handled?" answer just links to the
policy, where 365 Strong and Home Centered state plainly that nothing leaves the device.
Once those two policies are rewritten, their FAQs should be filled in to match.

There is no terms-of-service page yet. A single shared `terms.html` covering all four
apps is the intended shape when one is written; nothing currently links to it.
