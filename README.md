# toddgerarden.com

Personal academic site. Quarto → static HTML, served by GitHub Pages from `docs/`.

No package manager, no lockfile, no CI. The only dependency is the `quarto` binary.

## Updating

1. Edit `index.qmd`. Publications are plain markdown paragraphs inside a
   `::: {.pub-list}` div — add or move one entry.
2. If the CV changed, rebuild it in `~/Dropbox/CV/current cv/` and copy it over:
   `cp "$HOME/Dropbox/CV/current cv/todd-gerarden-cv.pdf" files/`
3. `quarto preview` to check it, then `quarto render` to write `docs/`.
4. `git add -A && git commit -m "..." && git push`

The commit must include `docs/` — that's what GitHub serves.

## Layout

| Path | What it is |
|---|---|
| `index.qmd` | all page content |
| `custom.scss` | the entire design, ~140 lines |
| `_quarto.yml` | project + site config |
| `files/` | CV and ungated paper PDFs |
| `images/` | headshot, favicon |
| `fonts/` | self-hosted `.woff2` |
| `docs/` | rendered output, committed and served |
| `CNAME` | custom domain; Quarto copies it into `docs/` on render |
| `.nojekyll` | stops GitHub running Jekyll over the output |

## If a render fails with a NotFound on a site_libs path

Quarto intermittently fails part-way through copying its support files:

```
ERROR: NotFound: ... lstat '.../site_libs/bootstrap/bootstrap-icons.woff'
```

**Just run `quarto render` again.** It succeeds on the second attempt. Don't
delete `docs/` to try to fix it — emptying the output directory makes this more
likely, not less. Always confirm `docs/index.html` was written before committing.

## Hosting

- Settings → Pages: deploy from branch `main`, folder `/docs`. The custom domain
  field is left empty — the committed `CNAME` sets it.
- DNS at Namecheap: apex `A` records point at GitHub Pages; `www` is a `CNAME` to
  `tgerarden.github.io`.
- Don't delete `aem2850`'s own `CNAME` file — it's what keeps that course site on
  `aem2850.toddgerarden.com`.
