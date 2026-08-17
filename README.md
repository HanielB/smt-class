# Theory and Practice of SMT Solving

Course websites, one directory per edition:

- `2024.2/` — https://hanielb.github.io/smt-class/2024.2/
- `2026.2/` — https://hanielb.github.io/smt-class/2026.2/

Each directory is a self-contained Jekyll site (just-the-docs theme). The root
`index.html` is a landing page linking to the editions.

Deployment is done by `.github/workflows/pages.yml`, which builds every edition
and publishes them together to GitHub Pages. The repo's Pages source must be set
to **GitHub Actions** (Settings → Pages); if it is left on "Deploy from a
branch", GitHub's own build races this workflow and overwrites the site with an
unstyled default-theme version.

## Adding a new edition

1. Copy the previous edition's directory (or the scaffolding plus whatever
   material carries over) to `YYYY.N/`.
2. Update `baseurl` in `YYYY.N/_config.yml` to `/smt-class/YYYY.N`.
3. Add a build step for it in `.github/workflows/pages.yml`.
4. Add a link in the root `index.html`.

## Local preview

From the repo root (one shared `Gemfile`):

```sh
bundle install
bundle exec jekyll serve -s 2026.2 -d _site/2026.2
```

Then open http://localhost:4000/smt-class/2026.2/ (the configured `baseurl` is
kept when serving locally).
