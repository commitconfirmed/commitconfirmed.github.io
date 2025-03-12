# commitconfirmed.github.io
Personal Blog using Hugo


# Random notes

Added the below to the tailwind theme as I wanted a title (layouts/partials/header.html)

``` html
  {{ if .Site.Params.header.title }}
  <div class="hidden md:flex items-center px-2 text-3xl bold text-slate-800 dark:text-slate-200 ">
    {{ .Site.Params.header.title }}
  </div>
  {{ end }}
```