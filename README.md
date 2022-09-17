
# Bootstrap new dark mode test

**Attention! This is just a test repository. But it works.**

[It works with this Bootstrap source!!!](https://github.com/twbs/bootstrap/tree/new-masthead-darkmode)

## SCSS short description

Generates CSS from background and foreground colors and Bootstrap colors.


### Colors

```bash
/site/assets/color/_palette.scss
/site/data/color/palette.yml
```

Background and foreground colors only. **gen-light** and **gen-dark** same color as Bootstrap light and dark. The other colors are just samples (Very funny).

### Root

```bash
/site/assets/color/root.scss (128 rows.)
```

It's like bootstrap .root.scss

### Theme colors

```bash
/site/assets/color/theme-colors.scss (143 rows.)
```

Everything that uses $theme-colors. Except for shadow.

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

### Copy all files to bootstrap sources directory

### Add it to the end of the scripts

/packages.json

```json
"color-start": "npm-run-all --parallel color-watch-css docs-serve",
"color-watch-css": "nodemon --watch site/assets/color/ --ext scss --exec \"npm run color-css-lint\"",
"color-css-lint": "npm-run-all --aggregate-output --continue-on-error --parallel color-css-lint-*",
"color-css-lint-stylelint": "stylelint \"site/assets/color/*.scss\" --cache --cache-location .cache/.stylelintcache --rd -i site/assest/color-themes/*",
"color-css-lint-vars": "fusv site/assets/color/"
```

### Add it to the end of the &lt;li class="nav-item dropdown"&gt; menu

/site/layouts/partials/docs-navbar.html

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

### Add it to the end of the icons svg

/site/layouts/partials/icons.html

```html
{{ range $.Site.Data.color.palette }}
<symbol id="{{ .name }}" viewBox="0 0 16 16">
    <circle style="fill:{{ .body_bg }};stroke:currentColor;stroke-width:1;stroke-miterlimit:4;stroke-dasharray:none;stroke-opacity:1" cx="8" cy="8" r="7.5" />
</symbol>
{{ end }}
```

### Add it to the end of the stylesheet

/site/layouts/partials/stylesheet.html

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
