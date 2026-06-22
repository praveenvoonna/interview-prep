# Interview Prep

From-scratch engineering study notes with interview-ready talking points, published as a
searchable static site with [MkDocs Material](https://squidfunk.github.io/mkdocs-material/)
and deployed on [Netlify](https://www.netlify.com/).

All study material lives under [`docs/`](./docs/). Each course follows the same format:
**"The One Idea"** analogy → real details → **"Say this in the interview"** one-liners.

## Run locally

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
mkdocs serve            # live-reload preview at http://127.0.0.1:8000
```

Build the static site into `site/`:

```bash
mkdocs build
```

## Deploy to Netlify

This repo includes `netlify.toml`, so deployment is zero-config:

1. Push this repo to GitHub (public).
2. In Netlify: **Add new site → Import an existing project** → pick this repo.
3. Netlify reads `netlify.toml` and runs `pip install -r requirements.txt && mkdocs build`,
   publishing the `site/` directory. No manual settings needed.

Every push to the default branch triggers an automatic redeploy.

## Structure

```
docs/                     # all study notes (auto-built into the site nav)
mkdocs.yml                # site + theme config
requirements.txt          # mkdocs-material
netlify.toml              # Netlify build/publish config
```
