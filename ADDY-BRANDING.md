# Addy branding for Opale

This fork of [Opale](https://github.com/gagnieray/opale) is rebranded to match the
**[addy.vn](https://addy.vn/)** brand, for the Addy self-hosted Redmine.

## Palette

Pulled from the addy.vn stylesheet (`--color-*` design tokens):

| Role | Token | Hex |
|---|---|---|
| Primary (navy) | `--color-primary` | `#1a2f4e` |
| Primary 700 | `--color-primary-700` | `#2a4063` |
| Primary 50 | `--color-primary-50` | `#f4f6fa` |
| Accent (orange) | `--color-accent` | `#f08c2e` |
| Accent 600 | `--color-accent-600` | `#d97718` |
| Interactive blue | `--color-ring` | `#0074c9` |
| Foreground on brand | — | `#ffffff` |

## What was remapped

Only the **brand chrome** is rebranded, in `src/sass/_variables.scss`. Redmine's
semantic status / tracker / priority colors are intentionally left distinct so
issues stay scannable.

| Opale variable | Was | Now | Drives |
|---|---|---|---|
| `$brand-primary` | `#628db6` | `#1a2f4e` | header, top menu, progress bars, active components, focus ring |
| `$blue` | `#169` | `#0074c9` | links, info states, primary buttons |
| `$orange` | `#fc8c12` | `#f08c2e` | warnings, accents |
| `$link-hover-color` | `$red` | `#d97718` | link hover |
| `$sidebar-link-active-side` | `$red` | `#f08c2e` | active sidebar stripe |

The Addy tokens are defined as `$addy-*` variables at the top of the Colors block
in `_variables.scss`.

## Rebuild

The theme compiles `src/sass/**` → `stylesheets/application.css`. You do **not**
need a full `npm install` — dart-sass + autoprefixer via `npx` is enough and
matches `Gruntfile.js` (compressed + autoprefixer):

```sh
npx sass@1.101.0 src/sass/application.scss stylesheets/application.css --style=compressed --no-source-map
npx -p postcss@8 -p postcss-cli@11 -p autoprefixer@10 postcss stylesheets/application.css --use autoprefixer --replace --no-map
```

(Or, with the full toolchain installed: `npm install && npm run build`.)

## Deploy on Redmine 6.1

Redmine 6.1 serves theme CSS as a **Propshaft precompiled, digested asset** from
`public/assets` — not the raw file. After rebuilding `stylesheets/application.css`
you must, inside the Redmine container:

```sh
# 1. re-precompile so the new CSS gets a digested file in public/assets
RAILS_ENV=production bin/rails assets:precompile

# 2. make Opale the active theme (only needed once; it's a DB setting)
RAILS_ENV=production bin/rails runner "Setting.ui_theme = 'opale'"
```

Then hard-refresh the browser. Editing the theme file and restarting alone is
**not** enough — the precompile step is what actually publishes the change.
