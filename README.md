# filebrowser-boringbox

A maintained-by-one-household fork of [File Browser](https://github.com/filebrowser/filebrowser)
(`v2.63.23`, the final upstream release — upstream was archived 2026-09-01) with one feature added:
**the UI is themeable without forking it again.**

- Every look-and-feel literal (fonts, accent colour, corner radii, shadows, header height,
  transitions) is now a CSS custom property on `:root`, defaulting to the exact upstream value.
  Verified pixel-for-pixel against stock `v2.63.23` on 20 screens, light and dark
  (`docs/PIXELDIFF.md`).
- A drop-in theme is a single `custom.css` handed to `--branding.files`. No rebuild.
- One extra branding flag: `--branding.disableCredits`.
- Ships with the **Boring Box** theme (`theme/`): black, flat, square, system fonts.
  That's the one we use at home; it's the reason this fork exists.

Everything else is upstream, untouched. Design notes and the full variable table: `docs/THEMEABLE.md`.

## Use it

```
filebrowser config set --branding.files /path/to/theme --branding.theme dark --branding.disableCredits
```

Write your own theme by overriding the variables listed in `docs/THEMEABLE.md` in a `custom.css`.

## Build

```
cd frontend && pnpm install && pnpm run build && cd ..
go build -o filebrowser .
```

## Who made this

The owner of this repo (Mu Yin) wanted File Browser to look like the rest of his home dashboard,
found upstream archived, and asked Claude (Anthropic's AI) to do the fork properly instead of
patching stylesheets. **The code, docs and tests in the two commits on top of upstream were written
by Claude, directed and reviewed by Mu.** Issues and PRs are read by Mu and worked by Claude;
expect replies to say so.

## Scope, honestly

- No new features beyond theming. Not a community successor to File Browser.
- Security fixes: we will pull in anything the community forks publish, on a best-effort basis.
- One known upstream issue we intend to fix here because it bites us: unrevocable JWT sessions
  (upstream #5216). Tracked on the `sessions` branch when it exists.

## License

Apache-2.0, same as upstream. Upstream's README is kept as `UPSTREAM-README.md`.
