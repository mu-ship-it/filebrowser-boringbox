# Customization

You can customize the styles, branding and icons of your File Browser instance in order to give it a personal touch.

## Custom Branding

You can customize File Browser to use your own branding. This includes the following:

- **Name**: the name of the instance that shows up on the tab title, login pages, and some other places.
- **Disable External Links**: disables all external links, except to the documentation.
- **Disable Used Percentage**: disables the disk usage information on the sidebar.
- **Branding Folder**: directory which can contain two items:
  - `custom.css`, containing a global stylesheet to apply to all users.
  - `img`, a directory which can replace all the [default logotypes](https://github.com/filebrowser/filebrowser/tree/master/frontend/public/img) from the application.

This can be configured by the administrator user, under **Settings → Global Settings**. You can also update the configuration directly using the [CLI](cli/filebrowser-config-set.md):

```sh
filebrowser config set --branding.name "My Name" \
  --branding.files "/abs/path/to/my/dir" \
  --branding.disableExternal
```

> [!NOTE] 
>
> If you are using Docker, you need to mount a volume with the `branding` directory in order for it to be accessible from within the container.

### Theme Variables

The `custom.css` in the branding folder is loaded after the built-in styles, so it can override any of the CSS custom properties below. Their defaults reproduce the stock look; the existing colour variables (`--background`, `--surfacePrimary`, `--textPrimary`, `--blue`, ...) in `frontend/src/css/_variables.css` can be overridden the same way, for `:root` (light) and `:root.dark`.

| Variable | Default | Used for |
|---|---|---|
| `--font-ui` | `"Roboto", sans-serif` | all interface text |
| `--font-mono` | `monospace` | text editor fields, shell |
| `--accent` | `var(--blue)` | buttons, links, selection, active tabs |
| `--accent-hover` | `var(--dark-blue)` | hovered buttons, flat button text |
| `--accent-text` | `#fff` | text and icons on `--accent` |
| `--radius-control` | `0.1em` | buttons, inputs, dropdowns |
| `--radius-tile` | `0.2em` | mosaic items, share box, dialog list items |
| `--radius-card` | `2px` | cards and dialogs |
| `--radius-search` | `0.3em` | search box |
| `--radius-round` | `50%` | round icon buttons, counters |
| `--shadow-card` | Material card shadow | cards and dialogs |
| `--shadow-tile` / `--shadow-tile-hover` | soft two-layer shadow | mosaic items, share box, mobile selection bar |
| `--shadow-bar` | `0 0 5px var(--borderPrimary)` | header, search, sidebar and shell on mobile |
| `--shadow-button` | `0 0 5px var(--divider)` | raised buttons |
| `--shadow-menu` | `0 2px 4px var(--borderPrimary)` | context menu |
| `--transition-fast` / `--transition-base` | `0.1s ease` / `0.2s ease` | hover and layout transitions |
| `--header-height` | `4em` | header bar and everything positioned below it |
| `--header-padding` | `0.5em 0.5em 0.5em 1em` | header bar |
| `--listing-item-padding` | `1em` | rows in list view |

For example, a flat, square theme:

```css
:root {
  --radius-control: 0;
  --radius-tile: 0;
  --radius-card: 0;
  --radius-search: 0;
  --shadow-card: none;
  --shadow-tile: none;
  --shadow-tile-hover: none;
  --shadow-bar: none;
  --shadow-button: none;
  --shadow-menu: none;
}
```

### Custom Icons

To replace the default logotype and favicons, you need to create an `img` directory under the branding directory. The structure of this directory must mimic the one from the [default logotypes](https://github.com/filebrowser/filebrowser/tree/master/frontend/public/img):

```
img/
  logo.svg
  icons/
    favicon.ico
    favicon.svg
    (...)
```

Note that there are different versions of the same favicon in multiple sizes. To replace all of them, you need to add versions for all of them. You can use the [Real Favicon Generator](https://realfavicongenerator.net/) to generate these for you from your base image. 

> [!NOTE]
>
> The icons are cached by the browser, so you may not see your changes immediately. You can address this by clearing your browser's cache.
