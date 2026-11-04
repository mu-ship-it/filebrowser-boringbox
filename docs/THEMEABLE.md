# feat: make the UI themeable via CSS custom properties, add `branding.disableCredits`

> **Status note (not part of the PR body):** upstream `filebrowser/filebrowser` states in its README
> that it was *archived on 2026-09-01* ("no further releases, bug fixes, or security fixes").
> This branch is therefore not submittable upstream as-is; it is written to upstream standards so it
> can go to an active community fork, or be carried locally with minimal merge cost.

Branch: `themeable` (local, `~/fb-violet/src`), on top of `v2.63.23` (`e8a388f`).

Commits:
- `64d77d4` feat(frontend): expose look-and-feel values as CSS custom properties
- `b3f0294` feat: add branding.disableCredits option

## Motivation

`--branding.files` lets an admin ship a `custom.css`, and the colour variables in
`_variables.css` can already be overridden. But corner radii, shadows, the UI font, the accent
colour, transitions and the header height are literals spread over ~15 stylesheets and a few
components. A theme that wants, say, square corners or a system font has to re-declare dozens
of selectors and breaks whenever upstream touches one of them.

This PR replaces those literals with custom properties whose **defaults are the current values**,
so nothing changes visually (verified pixel-for-pixel, below). It also adds one small branding knob
for white-labelled installs.

## Changes

### 1. Theme variables (`frontend/src/css/_variables.css`, `:root`)

| Variable | Default (= previous literal) | Replaces literals in |
|---|---|---|
| `--font-ui` | `"Roboto", sans-serif` | `base.css` body |
| `--font-mono` | `monospace` | `_inputs.css` textarea, `_shell.css` |
| `--accent` | `var(--blue)` | buttons, links, selection, active nav, shell prompt, epub, CSV viewer |
| `--accent-hover` | `var(--dark-blue)` | button hover, flat button text, epub |
| `--accent-text` | `#fff` | text/icons on accent (buttons, search type boxes) |
| `--radius-control` | `0.1em` | `.button`, `.input`, `DropdownModal.vue` |
| `--radius-tile` | `0.2em` | mosaic items, share box, file-list items, upload tiles, inline code |
| `--radius-card` | `2px` | `.card` (incl. dialogs) |
| `--radius-search` | `0.3em` | search box |
| `--radius-round` | `50%` | icon actions, counters, previewer arrows, mobile selection actions |
| `--shadow-card` | 3-layer Material shadow | `.card` |
| `--shadow-tile` | `0 1px 3px rgba(0,0,0,.06), 0 1px 2px rgba(0,0,0,.12)` | mosaic items, share box, mobile selection bar |
| `--shadow-tile-hover` | `0 1px 3px rgba(0,0,0,.12), 0 1px 2px rgba(0,0,0,.24)` | mosaic hover |
| `--shadow-bar` | `0 0 5px var(--borderPrimary)` | header, search, shell, mobile nav/dropdown |
| `--shadow-button` | `0 0 5px var(--divider)` | `.button` |
| `--shadow-menu` | `0 2px 4px var(--borderPrimary)` | context menu |
| `--transition-fast` | `0.1s ease` | every `0.1s ease <prop>` transition |
| `--transition-base` | `0.2s ease` | every `0.2s ease <prop>` transition |
| `--header-height` | `4em` | header, body offset, sidebar, breadcrumbs, search overlay, shell, editor, PDF preview, epub |
| `--header-padding` | `0.5em 0.5em 0.5em 1em` | header |
| `--listing-item-padding` | `1em` | list-view rows |

21 variables. Notes for reviewers:
- Values are unregistered custom properties, so `em` units resolve at the use site exactly as before.
- Variables that reference theme colours (`--shadow-bar`, `--accent`, ...) live on `:root`; `.dark`
  is applied to the same `<html>` element and nothing redefines those colours deeper in the tree,
  so dark mode resolves as before (verified, below).
- Left as literals on purpose (single-use, or not a theme concept): toast/CSV viewer/conflict-dialog
  radii and shadows, the upload progress bar radius, the "Copied!" tooltip, `ease-in-out` transitions.
- Documented in `docs/customization.md` → *Theme Variables*, with a flat/square example.

### 2. `branding.disableCredits`

Hides the `File Browser <version>` line at the bottom of the sidebar. Help link and disk usage stay.
Implemented exactly like `branding.disableExternal`:
`settings.Branding.DisableCredits` → `config init/set --branding.disableCredits` + `config cat`
output → `http/static.go` template data → `window.FileBrowser.DisableCredits` → `constants.ts` →
`Sidebar.vue`; checkbox in *Settings → Global → Branding* (new `settings.disableCredits` string in
`en.json` only, per the Transifex flow). CLI docs regenerated (`go run . docs`, only the two
`config` pages change); `docs/customization.md` bullet added. Test:
`TestGetSettingsDisableCredits` in `cmd/cmd_test.go`, alongside the existing settings flag test.

Not added: a `branding.defaultViewMode`. The existing `--viewMode` user default
(`config init/set --viewMode list`) already does this; adding a second knob would duplicate it.

## Verification

- `pnpm run lint` ✓ · `prettier --check` on touched files ✓ · `pnpm run test` 29/29 ✓ ·
  `pnpm run build` ✓ · `golangci-lint run ./...` 0 issues · `go test ./cmd ./settings` ✓
- Runtime: `disableCredits` off → credits line shown; on → hidden, Help still shown.
- Pixel diff: unmodified v2.63.23 (port 8798) vs this branch (port 8799), both quick setup
  `--noauth --root sample`, Chromium 151 via Playwright, DPR 1, `pixelmatch` threshold 0.

**Light (OS light → default theme)**

| screen | viewport | differing px | diff % |
|---|---|---|---|
| listing | 1280×800 | 0 | 0.00% |
| listing | 390×844 | 0 | 0.00% |
| folder | 1280×800 | 0 | 0.00% |
| folder | 390×844 | 0 | 0.00% |
| upload dialog | 1280×800 | 0 | 0.00% |
| upload dialog | 390×844 | 0 | 0.00% |
| delete confirm | 1280×800 | 0 | 0.00% |
| delete confirm | 390×844 | 0 | 0.00% |
| settings | 1280×800 | 0 | 0.00% |
| settings | 390×844 | 0 | 0.00% |

**Dark (OS dark → `.dark`)**: same 10 pairs, all **0 px / 0.00%**.

Screenshots: `~/fb-violet/shots/{base,themeable}[-dark]/`, diff masks in `~/fb-violet/shots/diff[-dark]/`.
