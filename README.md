# toddgerarden.com

Personal academic site. Quarto → static HTML, served by GitHub Pages from `docs/`.

No package manager, no lockfile, no CI. The only dependency is the `quarto` binary.

## Updating

1. Edit `index.qmd`. Publications are plain markdown paragraphs inside a
   `::: {.pub-list}` div — add or move one entry.
2. If the CV changed, rebuild it in `~/Dropbox/CV/current cv/` and copy it to
   **both** locations — `s/` preserves the old Squarespace URL, which is still
   linked from elsewhere, and it will serve a stale CV if you forget it:

   ```bash
   CV="$HOME/Dropbox/CV/current cv/todd-gerarden-cv.pdf"
   cp "$CV" files/ && cp "$CV" s/
   ```
3. `quarto preview` to check it, then `quarto render` to write `docs/`.
4. `git add -A && git commit -m "..." && git push`

The commit must include `docs/` — that's what GitHub serves.

## Layout

| Path | What it is |
|---|---|
| `index.qmd` | all page content |
| `custom.scss` | the entire design, ~195 lines |
| `_quarto.yml` | project + site config |
| `files/` | CV and ungated paper PDFs |
| `images/` | headshot, favicon |
| `fonts/` | Newsreader, self-hosted as variable fonts (optical-size axis intact) |
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

Live at <https://toddgerarden.com>, GitHub Pages out of `docs/` on `main`.

- **Pages source**: `main` / `/docs`. GitHub auto-enabled Pages when the repo was
  named `tgerarden.github.io` and defaulted it to the repo *root*, which has no
  `index.html` — so `/` 404'd until the path was changed. Changing the source via
  the API does not trigger a rebuild; force one if it looks stuck.
- **Custom domain**: set in the Pages config *and* committed as `CNAME`, which
  Quarto copies into `docs/` on every render. Because the custom domain is set,
  `tgerarden.github.io` 301s to `toddgerarden.com` — so it can't be used as a
  preview URL. To preview online before a DNS change, detach the domain from the
  Pages settings temporarily (the `CNAME` file can stay put); it re-attaches on
  the next build. Note that detaching or attaching the domain makes GitHub commit
  to `docs/CNAME` itself, so expect stray "Create CNAME" / "Delete CNAME" commits
  and pull before your next push.
- **Zero external requests.** `$web-font-path: false` in `custom.scss` kills the
  Google Fonts `@import` that the `cosmo` theme otherwise hides inside the
  compiled stylesheet — invisible in `index.html`, but the browser still fetches
  it.

### DNS at Namecheap

| Type | Host | Value |
|---|---|---|
| A | `@` | `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153` |
| CNAME | `www` | `tgerarden.github.io.` |
| CNAME | `aem2850` | `tgerarden.github.io.` — the course site, leave alone |

### If HTTPS shows a certificate warning

GitHub verifies DNS at the moment you attach the custom domain, and does not
reliably retry. Attaching it *before* DNS pointed at GitHub left the certificate
stuck on "not yet issued" while HTTPS served GitHub's `*.github.io` cert, which
doesn't match the domain.

Fix: with DNS already correct, detach and re-attach the custom domain. The
certificate is approved within seconds. Then

```bash
gh api -X PUT repos/tgerarden/tgerarden.github.io/pages -F https_enforced=true
```

Note `-F`, not `-f` — the API wants a boolean, and `-f` sends a string and
silently does nothing.

**Set DNS first, attach the domain second.** That avoids the whole problem.
