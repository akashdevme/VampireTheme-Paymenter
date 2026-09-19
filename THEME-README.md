# Sanguine — a vampire-luxury theme for Paymenter

Built on top of Paymenter's default theme. Everything not mentioned below
(page structure, checkout logic, ticket system, etc.) is untouched — only
look and feel changed.

## What's different from the default theme

- **`theme.php`** — new default colors (crimson/rose on near-black or blush
  ivory) plus two new settings: `gold` / `dark-gold` (the hairline trim
  color) and `show_palette_switcher` (turn the floating picker off without
  touching code).
- **`views/layouts/colors.blade.php`** — outputs the new `--color-gold`
  variable the same way every other color is output.
- **`css/app.css`**:
  - Fonts swapped to Cormorant Garamond (headings, via `--font-display`)
    and Inter (body, via `--font-sans`), loaded from Google Fonts in
    `layouts/app.blade.php`.
  - `.card` got a soft crimson-tinted shadow instead of a flat border-only
    look; new `.card-trim` utility adds a thin gold hairline across the
    top — used on the login form, meant for the one or two "signature"
    cards per page, not everywhere.
  - New `.btn-glow` utility (soft glow + press animation), applied to
    `x-button.primary`.
  - A very faint two-tone radial glow behind the page in dark mode only.
  - Visible focus ring in the brand color (keyboard accessibility).
  - At the bottom of the file: the 20 accent palettes (see below).
- **`views/components/palette-switcher.blade.php`** — new component, the
  floating bottom-right button.
- **`views/layouts/app.blade.php`** — loads the two fonts, includes the
  palette switcher.
- **`views/components/navigation/index.blade.php`** — site name now uses
  the display serif.
- **`views/components/button/primary.blade.php`** — added `.btn-glow`.
- **`views/auth/login.blade.php`** — the default theme's login form
  referenced a few color classes (`bg-primary-800`, `text-secondary-500`,
  etc.) that don't exist in this token system and were rendering as
  unstyled; replaced with real tokens and wrapped in `.card-trim`.

## How the palette switcher works

The admin-set colors in Settings → Theme control the *base* look
(background, text, borders) for Light/Dark/System — that's all standard
Paymenter behavior, untouched.

The floating button is a separate, purely front-end layer on top of that:
clicking a swatch sets `data-palette="<id>"` on `<html>` and saves the
choice in the visitor's own browser (`localStorage`, via Alpine's
`$persist` — nothing is sent to the server, so it's per-visitor, not
global). CSS rules like:

```css
html[data-palette="crimson-classic"] { --color-primary: 350 70% 42%; --color-secondary: 340 55% 48%; }
html.dark[data-palette="crimson-classic"] { --color-primary: 350 78% 50%; --color-secondary: 340 65% 56%; }
```

override just `--color-primary` / `--color-secondary` — background, text
and border colors keep following Light/Dark/System as normal.

### Adding, removing, or re-coloring palettes

Two places need to agree on the `id`:

1. `css/app.css` (bottom of the file) — the `html[data-palette="..."]` /
   `html.dark[data-palette="..."]` rule pair for that id.
2. `views/components/palette-switcher.blade.php` — the `$palettes` array
   at the top (`id`, display `name`, and a `swatch` CSS color used only
   for the little preview dot).

Delete a palette by removing it from both places; add one the same way.

### Turning the switcher off

Admin panel → Settings → Theme tab → uncheck "Show Palette Switcher".
Or, to remove it from the code entirely, delete the
`<x-palette-switcher />` line in `views/layouts/app.blade.php`.

## After any edit

Custom CSS/JS needs a rebuild — nothing shows up from just editing the
files:

```bash
npm run build sanguine
```

(Blade/PHP file edits — like tweaking `theme.php` or any `.blade.php`
view — don't need a rebuild, only CSS/JS does.)
