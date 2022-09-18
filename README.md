
# Bootstrap new dark mode test

**Attention! Under construction!**

[It works with this Bootstrap source!!!](https://github.com/twbs/bootstrap/tree/new-masthead-darkmode)

## SCSS

Generates CSS from background, foreground and accent colors and Bootstrap colors. It's very small and includes everything you need to create a new color scheme and customize it. It only uses CSS variable override.

## Files

### Colors

```bash
/site/assets/color/_palette.scss
/site/data/color/palette.yml
```

Contains background, foreground and accent colors only. **gen-light** and **gen-dark** same color as Bootstrap light and dark, except for the accent color. The other colors are just samples.

### Root

```bash
/site/assets/color/root.scss
```

It's like bootstrap .root.scss and modifies the following classes:

- .accordion-button
- .list-group-item
- .btn-close
- .progress
- .carousel
- .form-select
- .form-switch
- .form-check-input
- .form-range
- .form-control

### Theme colors

```bash
/site/assets/color/theme-colors.scss
```

Everything that uses $theme-colors variable:

- .text-*
- .bg-*
- .text-bg-*
- .link-*
- .border-*
- .alert-*
- .table-*
- .list-group-item-*
- .btn-
- .btn-outline-*

### Other scss

```bash
/site/assets/color/docs.scss
```

Overrides some CSS classes for a better visual experience.

```bash
/site/assets/color/_theme-*.scss
```

Copy from Bootstrap /scss/mixins directory. With a very small modification.

## Install

Copy all files to bootstrap sources directory. **Except .git and README.md!**

### File: /packages.json

Add this content after [this line](https://github.com/twbs/bootstrap/blob/new-masthead-darkmode/package.json#L101)

```json
"color-start": "npm-run-all --parallel color-watch-css docs-serve",
"color-watch-css": "nodemon --watch site/assets/color/ --ext scss --exec \"npm run color-css-lint\"",
"color-css-lint": "npm-run-all --aggregate-output --continue-on-error --parallel color-css-lint-*",
"color-css-lint-stylelint": "stylelint \"site/assets/color/*.scss\" --cache --cache-location .cache/.stylelintcache --rd -i site/assest/color-themes/*",
"color-css-lint-vars": "fusv site/assets/color/"
```

### File /site/layouts/partials/docs-navbar.html

Add this content after [this line](https://github.com/twbs/bootstrap/blob/new-masthead-darkmode/site/layouts/partials/docs-navbar.html#L118)

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

### File: /site/layouts/partials/icons.html

Add this content after [this line](https://github.com/twbs/bootstrap/blob/new-masthead-darkmode/site/layouts/partials/icons.html#L81)

```html
{{ range $.Site.Data.color.palette }}
<symbol id="{{ .name }}" viewBox="0 0 16 16">
    <circle style="fill:{{ .body_bg }};stroke:currentColor;stroke-width:1;stroke-miterlimit:4;stroke-dasharray:none;stroke-opacity:1" cx="8" cy="8" r="7.5" />
</symbol>
{{ end }}
```

### File: /site/layouts/partials/stylesheet.html

Add this content after [this line](https://github.com/twbs/bootstrap/blob/new-masthead-darkmode/site/layouts/partials/stylesheet.html#L27)

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

## Execute

```bash
npm install
npm run color-start
```
