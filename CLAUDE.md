# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A static personal portfolio website (data engineering / analytics engineering focus) for Abdelhamid Ben Brahim, hosted on GitHub Pages. Plain HTML, CSS, and vanilla JS — no build step, no framework, no backend.

## Technical Constraints (from PRODUCT_SPEC.md — treat as hard rules)

- Must remain fully static — no backend, no server-side code.
- No frameworks (no React, Vue, etc.) and no Jekyll.
- Must work as-is on GitHub Pages.
- Keep dependencies minimal; avoid adding build tooling (bundlers, npm packages) unless truly necessary.
- No inline styles — all styling goes through `assets/css/styles.css`.

## Running / Testing

There is no build or test step. Since pages load content via `fetch()`, opening `index.html` directly via `file://` will fail (CORS on fetch) — serve the repo root with a simple static server, e.g.:

```
python -m http.server 8000
```

Then visit `http://localhost:8000/index.html`.

Deployment is automatic via `.github/workflows/static.yml`: any push to `main` triggers a GitHub Pages deploy of the entire repo as-is (no build step runs in CI).

## Architecture

**Content/markup split**: Page chrome (nav, footer, hero, page-specific static markup) lives directly in each `.html` file. Dynamic, repeatable content (profile info, featured projects, additional projects, skills, blog post list) lives in `content/content.json` and is fetched and rendered client-side by inline `<script>` blocks at the bottom of `index.html`, `projects.html`, and `blog.html`.

- `content/content.json` is the single source of truth for: `profile`, `featuredProjects`, `additionalProjects`, `skills`, `blogPosts`. To add/edit a project or blog post listing, edit this file — do not hardcode project/post data into HTML.
- Each page's inline script independently fetches `content/content.json` and has its own small render functions (e.g. `renderFeaturedCard`, `renderSkills` in `index.html`). There's no shared JS module — rendering logic is duplicated per page by design (no bundler to share code through).
- Individual blog posts are full standalone HTML files under `blog/` (e.g. `blog/pipeline-optimisation.html`), linked to from `content.json`'s `blogPosts[].url`. Adding a new post means: write the HTML file in `blog/`, then add a corresponding entry to `content.json`'s `blogPosts` array.
- `assets/css/styles.css` is the one hand-written stylesheet used by all pages.

**Legacy/unused directories** — do not edit or treat as live code:
- `assets/sass/` and `assets/portfolio/` (including `assets/portfolio/content.json`) are leftovers from an earlier template and are not referenced by any current HTML page. The live data file is `content/content.json`, not `assets/portfolio/content.json`.

## Design Principles (from PRODUCT_SPEC.md)

- Clean, modern, light-mode, data/finance-style aesthetic; minimal but structured.
- Content over visuals; quantified, specific achievements over generic descriptions.
- Featured projects ordered with best/most impactful first.
- Scroll/card-based layout for projects — no tab-based navigation.
