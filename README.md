# smithpeakscreations.github.io

Static site for [smithpeakscreations.com](https://smithpeakscreations.com), served from
`main` by GitHub Pages. It is the public home for four iOS apps: a landing page per app
carrying an App Store link, an FAQ and a support address, plus the publicly-reachable
privacy-policy and support URLs that App Store Connect requires. Apps that sell
something also get a terms-of-use page.

## Structure

```
index.html                    Landing page — hero, app grid, contact
apps/*.html                   One landing page per app — badge, features, FAQ, support
privacy/*.html                One policy per app
terms/*.html                  One terms-of-use page per app that sells something
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

Slugs are shared across `apps/`, `privacy/`, `terms/`, and `assets/icons/` — one slug per
app, everywhere.

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

**These nine URLs are immutable.** They are submitted inside App Store Connect
listings — the `privacy/` files as each app's Privacy Policy URL, the `apps/` files as its
Support URL — and several are additionally hard-coded inside the apps themselves. Do not
rename, move, or extension-strip these files:

- `/privacy/scorekeeppro.html` &nbsp;&nbsp; `/apps/scorekeeppro.html`
- `/privacy/thesportpulse.html` &nbsp;&nbsp; `/apps/thesportpulse.html` &nbsp;&nbsp; `/terms/thesportpulse.html`
- `/privacy/homecentered.html` &nbsp;&nbsp; `/apps/homecentered.html`
- `/privacy/365strong.html` &nbsp;&nbsp; `/apps/365strong.html`

`/privacy/365strong.html` is hard-coded in the 365 Strong app under Profile &rarr; About
&rarr; Privacy Policy. `/privacy/thesportpulse.html` and `/terms/thesportpulse.html` are
both hard-coded in The Sport Pulse under Settings, in `SettingsView.swift`.

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
2. `privacy/<slug>.html` — copy `privacy/homecentered.html` (or
   `privacy/thesportpulse.html`, if the app uses third-party SDKs) and rewrite the body.
   If the app sells anything, also `terms/<slug>.html` — copy `terms/thesportpulse.html`.
3. `apps/<slug>.html` — copy an existing app page. Update the head block (title,
   description, absolute `og:url`), the hero (icon, eyebrow, tagline, price/iOS/version
   line, badge `id`), the features, the FAQ, and the support address.
4. A card in `index.html`, linking to `apps/<slug>.html`. Keep exactly one `<a>` inside
   the card — see the note in `assets/site.css` under "App card".
5. Two entries in `sitemap.xml`, or three if there is a terms page.
6. In App Store Connect, set the **Support URL** to
   `https://smithpeakscreations.com/apps/<slug>.html` and the **Privacy Policy URL** to
   `https://smithpeakscreations.com/privacy/<slug>.html`. For an app with a subscription,
   also set the EULA / Terms of Use link to `https://smithpeakscreations.com/terms/<slug>.html`.

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

## Legal pages

Every app has a privacy policy. Apps that sell something also get a terms-of-use page
under `terms/`, because App Review expects one alongside an in-app purchase, and an
auto-renewing subscription additionally requires a working Terms of Use link inside the
binary under App Store Guideline 3.1.2.

**One document per app, not one shared document.** The apps differ enough in what they
collect and what they sell that a shared page would have to hedge every clause. Copy
`terms/thesportpulse.html` and rewrite the app-specific parts — sections 2, 4, 5, 7, and
11 are the ones that actually change. Section 13 carries the ten acknowledgements Apple
requires in a custom EULA; keep it, and check it against the current Exhibit A of
Schedule 1 in the Developer Program License Agreement, which Apple revises.

**The contracting party is Samuel Smith**, an individual, operating under the name Smith
Peaks Creations. Smith Peaks Creations is a domain and a brand — there is no registered
entity — so it cannot be a party to a contract or a data controller. Never write it as
"the Company".

## Known follow-up

Every policy now describes what its app actually does. The Sport Pulse's was the last
generator output still in the tree.

`apps/scorekeeppro.html` still answers "How is my data handled?" by linking to the
policy rather than stating the position on the page, which is what the other three now
do. Its policy has been accurate since #4, so that FAQ can be filled in.

ScoreKeep Pro and 365 Strong have no terms-of-use page. Neither sells anything, so
Apple's standard EULA covers them and one is optional. Home Centered has a complete but
feature-flagged premium subscription (`AppFeatures.premiumEnabled`); switching it on
requires a `terms/homecentered.html`, an in-binary link to it, and a privacy-policy
rewrite, since the paid tier includes iCloud sync.
