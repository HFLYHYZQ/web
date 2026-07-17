# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A personal blog built with **Hugo** (extended, v0.164.0), using a **hand-written custom theme** (no `themes/` directory, no vendored template). Deployed to GitHub Pages via a GitHub Actions workflow that builds and publishes on every push to `main`.

- Live site: https://HFLYHYZQ.github.io/web/
- Repo: `HFLYHYZQ/web` — note the site is served from a **subpath `/web/`**, not the domain root. `baseURL` in `hugo.toml` and all asset paths must respect this.

## Commands

Hugo is installed via winget (`Hugo.Hugo.Extended`) but **is not on the bash PATH**. Invoke it by full path:

```bash
HUGO="/c/Users/86135/AppData/Local/Microsoft/WinGet/Packages/Hugo.Hugo.Extended_Microsoft.Winget.Source_8wekyb3d8bbwe/hugo.exe"

# Local dev server with live reload (http://localhost:1313/)
"$HUGO" server --port 1313 --bind 127.0.0.1 --baseURL "http://localhost:1313/"

# Production build → ./public
"$HUGO" --gc

# Build mimicking CI (minified, with production baseURL)
"$HUGO" --minify
```

The **extended** Hugo binary is required — styles are authored in SCSS (`assets/scss/main.scss`) and compiled via `resources.Get ... | toCSS` in `layouts/partials/head.html`. Standard Hugo cannot build this site.

There are no tests or linters.

## Deploy

Push to `main` triggers `.github/workflows/hugo.yml`, which installs Hugo extended, builds with `--minify`, and deploys via `actions/deploy-pages`. GitHub Pages **Source must be set to "GitHub Actions"** in repo Settings → Pages (not "Deploy from a branch", which falls back to Jekyll and serves the README instead of the built site). The workflow overrides `baseURL` at build time using `actions/configure-pages` output, so CI-correct URLs are guaranteed regardless of `hugo.toml`.

From China, `git push` to GitHub is intermittently reset by the network — retry on failure.

## Architecture

**Custom theme lives in project-level `layouts/`** (not `themes/`), keeping everything in one place for easy editing:

- `layouts/_default/baseof.html` — shell; pulls `head`, `header`, `footer` partials, leaves a `main` block.
- `layouts/index.html` — **homepage only**. Contains the splash overlay (`#splash` + `共赏` button) and the 4-card nav grid. Splash shows on **every** homepage load (no localStorage gating); a small inline `<script>` fades it out on click/Enter.
- `layouts/_default/list.html` — article list (`content/posts/_index.md` → `/posts/`), renders `.Pages.ByDate.Reverse` as gold-bordered cards.
- `layouts/_default/single.html` — individual article + standalone pages (about, settings, links).
- `layouts/partials/head.html` — compiles & fingerprints the SCSS; do not hand-link CSS.
- `assets/scss/main.scss` — the entire visual design: black-gold palette, sharp-cornered "方正" geometry, system Chinese serif/sans font stack (no web fonts — keeps it CDN-free and China-friendly). Splash, nav-grid, post-list, and post-content styles all live here.

**Content structure** (`content/`): `_index.md` (home), `about/`, `settings/`, `links/` (leaf pages → single layout), and `posts/` (section → list layout, one `.md` per article). The homepage's 4 nav cards point at these four destinations; `前情提要` = `posts/`.

**Splash image**: `资料/璇女.png` is the source asset (user's, tracked in repo); a copy lives at `static/images/xuannv.png` and is referenced via `.Site.Params.splashImage`. If the source changes, recopy to `static/images/`.

## Design context

The visual brief is "黑金方正" (black-gold, square/angular) — see `docs/superpowers/specs/2026-07-13-hugo-heijin-blog-design.md` for the full spec. The site title is 「荒唐言」 and the style/concept name used in copy is 「无言子」 (note: these are distinct from the original spec which used 璇女集/黑金方正 — the copy was renamed after the spec was written; trust the live `hugo.toml`/content, not the spec, for current naming).
