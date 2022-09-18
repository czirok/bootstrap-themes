
# Bootstrap new dark mode test

**Attention! Under construction!**

It only works with this Bootstrap source: [https://github.com/twbs/bootstrap/tree/new-masthead-darkmode](https://github.com/twbs/bootstrap/tree/new-masthead-darkmode)

## What are the requirements for customize?

[https://getbootstrap.com](https://getbootstrap.com) and [https://sass-lang.com](https://sass-lang.com)

## SCSS

Generates CSS from background, foreground and accent colors, and Bootstrap colors. It's very small and includes everything you need to create a new color scheme and customize it. It only uses CSS variable override. **gen-light** and **gen-dark** same color as Bootstrap light and dark, except for the accent color. The other colors are just samples.

Everything based on [nesting color modes](https://bootstrap.czirok.net/docs/5.2/customize/color/#nesting-color-modes) and [new theme colors](https://bootstrap.czirok.net/docs/5.2/customize/color/#new-theme-colors).

You can easily set your favorite background, foreground and accent colors for light and dark theme.

Add the colors to these files:

```bash
/site/assets/color/_palette.scss # (Needed for CSS)
/site/data/color/palette.yml     # (Needed for UI)
```

Follow the installation guide below and you're done.

## Customizable colors and icons

What's not listed just works. Big thanks to the Bootstrap team.

### Content

- [Tables](https://bootstrap.czirok.net/docs/5.2/content/tables/)

### Forms

- [Controls](https://bootstrap.czirok.net/docs/5.2/forms/form-control/)
- [Select](https://bootstrap.czirok.net/docs/5.2/forms/select/#default)
- [Checks and radios](https://bootstrap.czirok.net/docs/5.2/forms/checks-radios/#checks)
  - [Switch](https://bootstrap.czirok.net/docs/5.2/forms/checks-radios/#switches)
- [Range](https://bootstrap.czirok.net/docs/5.2/forms/range/)

### Components

- [Accordion](https://bootstrap.czirok.net/docs/5.2/components/accordion/)
- [Alerts](https://bootstrap.czirok.net/docs/5.2/components/alerts/)
- [Carousel](https://bootstrap.czirok.net/docs/5.2/components/carousel/)
- [Close button](https://bootstrap.czirok.net/docs/5.2/components/close-button/)
- [List group](https://bootstrap.czirok.net/docs/5.2/components/list-group/)
- [Progress](https://bootstrap.czirok.net/docs/5.2/components/progress/)
- [Buttons](https://bootstrap.czirok.net/docs/5.2/components/buttons/)
  - [Outline buttons](https://bootstrap.czirok.net/docs/5.2/components/buttons/)

### Helpers

- [Color & background](https://bootstrap.czirok.net/docs/5.2/helpers/color-background/)
- [Colored links](https://bootstrap.czirok.net/docs/5.2/helpers/colored-links/)]

### Utilities

- [Background](https://bootstrap.czirok.net/docs/5.2/utilities/background/)
- [Borders](https://bootstrap.czirok.net/docs/5.2/utilities/borders/)
- [Colors](https://bootstrap.czirok.net/docs/5.2/utilities/colors/)

## Install

After you are done with [Bootstrap Contribute](https://getbootstrap.com/docs/5.2/getting-started/contribute/), and everything works, copy all files to bootstrap sources directory. **Except .git and README.md!**

Open the **/packages.json** file, and add this content after [this line](https://github.com/twbs/bootstrap/blob/new-masthead-darkmode/package.json#L101)


```json
"color-start": "npm-run-all --parallel color-watch-css docs-serve",
"color-watch-css": "nodemon --watch site/assets/color/ --ext scss --exec \"npm run color-css-lint\"",
"color-css-lint": "npm-run-all --aggregate-output --continue-on-error --parallel color-css-lint-*",
"color-css-lint-stylelint": "stylelint \"site/assets/color/*.scss\" --cache --cache-location .cache/.stylelintcache --rd -i site/assest/color-themes/*",
"color-css-lint-vars": "fusv site/assets/color/"
```

Open the **/site/layouts/partials/docs-navbar.html** file, and add this content after [this line](https://github.com/twbs/bootstrap/blob/new-masthead-darkmode/site/layouts/partials/docs-navbar.html#L118)

```html
{{ range $.Site.Data.color.palette }}
<li>
    <button type="button" class="dropdown-item d-flex align-items-center active" data-bs-theme-value="{{ .name }}">
    <svg class="bi me-2 opacity-50 theme-icon"><use href="#{{ .name }}"></use></svg>
    {{ replace .name "-" " " | title }}
    <svg class="bi ms-auto d-none"><use href="#check2"></use></svg>
    </button>
</li>
{{ end }}
```

Open the **/site/layouts/partials/icons.html** file, and add this content after [this line](https://github.com/twbs/bootstrap/blob/new-masthead-darkmode/site/layouts/partials/icons.html#L81)

```html
{{ range $.Site.Data.color.palette }}
<symbol id="{{ .name }}" viewBox="0 0 16 16">
    <circle style="fill:{{ .body_bg }};stroke:currentColor;stroke-width:1;stroke-miterlimit:4;stroke-dasharray:none;stroke-opacity:1" cx="8" cy="8" r="7.5" />
</symbol>
{{ end }}
```

Open the **/site/layouts/partials/stylesheet.html** file, and add this content after [this line](https://github.com/twbs/bootstrap/blob/new-masthead-darkmode/site/layouts/partials/stylesheet.html#L27)

```html
{{- $postcssColorOptions := dict "use" "autoprefixer" "noMap" true -}}
{{- $targetRootCssPath := path.Join "/docs" .Site.Params.docs_version "assets/color/color.root.css" -}}
{{- $sassRootOptions := dict "targetPath" $targetRootCssPath "outputStyle" "expanded" "precision" 6 -}}
{{- $colorRoot := resources.Get "color/root.scss" | toCSS $sassRootOptions | postCSS $postcssColorOptions }}

<link href="{{ $colorRoot.Permalink | relURL }}" rel="stylesheet" />

{{- $targetThemeColorsCssPath := path.Join "/docs" .Site.Params.docs_version "assets/color/color.theme-colors.css" -}}
{{- $sassThemeColorsOptions := dict "targetPath" $targetThemeColorsCssPath "outputStyle" "expanded" "precision" 6 -}}
{{- $colorThemeColors := resources.Get "color/theme-colors.scss" | toCSS $sassThemeColorsOptions | postCSS $postcssColorOptions }}

<link href="{{ $colorThemeColors.Permalink | relURL }}" rel="stylesheet" />

{{- $targetDocsThemeCssPath := path.Join "/docs" .Site.Params.docs_version "assets/color/color.docs.css" -}}
{{- $sassDocsThemeOptions := dict "targetPath" $targetDocsThemeCssPath "outputStyle" "expanded" "precision" 6 -}}
{{- $colorDocsTheme := resources.Get "color/docs.scss" | toCSS $sassDocsThemeOptions | postCSS $postcssColorOptions }}

<link href="{{ $colorDocsTheme.Permalink | relURL }}" rel="stylesheet" />
```

## Using npm scripts

```bash
npm run color-start
```
