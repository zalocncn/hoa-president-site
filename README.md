# hoa-president-site

Marketing site for **HOA President**, a suburban-satire survival card game for iOS —
sibling title to [Pocket Veto](https://pocketvetogame.com), and a deliberate structural
mirror of that site. Fully static: plain HTML, CSS and one hand-written script, no build
step, no framework, no forms, no database, no environment variables, no analytics, and not
one byte fetched from a third party. That's a security decision (see `vercel.json`'s
Content-Security-Policy), not laziness: a site with no server code and no third-party
script has no injection surface and nothing to leak. The CSP is strict enough that it
forbids inline `<script>` and inline `style=""` attributes outright, so every rule lives
in `styles.css` and every line of behaviour in `app.js`.

This repo is intentionally separate from the game's own (private) repository, so this site
can stay public without exposing game source, unreleased card content, or internal docs.

## Structure

```
index.html       /            — landing page
privacy.html     /privacy     — privacy policy (clean URL via vercel.json)
terms.html       /terms       — terms of use (clean URL via vercel.json)
styles.css                    — shared stylesheet (self-hosted fonts, no CDN)
app.js                        — the phone in the landing page's sticky rail
fonts/                        — Spectral + Barlow Condensed TTFs and their OFL licences
screens/                      — the six app screens the phone pages through
vercel.json                   — clean URLs + security headers
```

`font-src` is `'self'`, so the two typefaces are served from `fonts/` rather than linked
from Google Fonts — a `<link>` to `fonts.googleapis.com` would simply be blocked.

The palette is the app's own (`HOAPresident/DesignSystem/Palette.swift` in the game repo):
"dusk over the subdivision" — dark spruce ground `#0C120C`, warm cream `#F2EFE2`, mint
category chrome `#8FC9A4`, lawn green `#7FC98B`, and the Garden `#7FBF6E` / Bylaws
`#C79A55` slate colors in the footer. If the app's palette changes, re-sync the token
block at the top of `body.lp` in `styles.css` by hand.

The privacy policy is transcribed from the game repo's `docs/PRIVACY_POLICY.md`. If that
source document changes, re-sync `privacy.html` by hand — there's no build pipeline
linking the two repos on purpose.

## Pre-launch state

The price card's "Coming soon to the App Store" pill is a placeholder. Once the App Store
Connect record exists, replace it with a real link (`<a class="pill" href="...">`) — the
TODO comment sits right above it in `index.html`.

## Local preview

```
python3 -m http.server 8080
```

Then open `http://localhost:8080/`, `http://localhost:8080/privacy.html`, and
`http://localhost:8080/terms.html`. (Vercel's clean-URL rewrite, which serves `/privacy`
without the `.html` suffix, only applies once deployed.)

## Deploy

```
cd <repo root>
vercel --prod
```

The Vercel project is `hoa-president-site`, serving the apex domain
`hoapresidentgame.com` (with `www` 308-redirecting to it). After any deploy, confirm
`/`, `/privacy`, and `/terms` resolve, and
optionally `curl -I` the live response to confirm the CSP, HSTS, X-Content-Type-Options,
X-Frame-Options, Referrer-Policy, and Permissions-Policy headers are present.

## Updating

Since this is fully static, any edit is just an HTML/CSS change, committed and
redeployed — no migrations, no env vars, no dependencies to bump.
