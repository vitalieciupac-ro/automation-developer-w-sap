# Automation Developer Foundations with SAP (S/4HANA and GUI)

A 6-hour, instructor-led course for beginner UiPath Automation Developers, published as a website with [MkDocs](https://www.mkdocs.org/) and the [Material](https://squidfunk.github.io/mkdocs-material/) theme.

## What's here

```
mkdocs.yml                     # site config and navigation
docs/                          # all course content (Markdown)
  index.md                     # home page
  getting-started/             # overview + setup
  foundations/                 # the six modules + agenda + wrap-up
  facilitators/                # facilitator guide
  next-steps.md
  stylesheets/extra.css        # UiPath brand colours
.github/workflows/deploy.yml   # auto-builds and deploys to GitHub Pages
```

## Edit the content

All pages are plain Markdown in `docs/`. Change a file, and the site updates when you push (see below). To preview locally:

```bash
pip install mkdocs-material
mkdocs serve
# open http://127.0.0.1:8000
```

## Publish to GitHub Pages

1. Push this folder to a GitHub repository.
2. In the repo, go to **Settings → Pages** and set **Source: GitHub Actions**.
3. Every push to `main` rebuilds and publishes the site automatically.

Your site will be at `https://<your-username>.github.io/<repo-name>/`.

> Remember to update `site_url` and `repo_url` in `mkdocs.yml` to match your username and repo name.
