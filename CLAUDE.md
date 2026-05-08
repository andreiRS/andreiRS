# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository overview

Personal website for Razvan Andrei Surdu, hosted on GitHub Pages at `surdu.de`. Acts as a hub: links out to projects/experiments, hosts curated essays and build notes, and surfaces a `/now` page. The repository name (`andreiRS/andreiRS`) also makes `README.md` render on the GitHub profile, so README edits are profile-visible and not site content.

## Stack

- **Astro 5** with TypeScript, content collections via the glob loader.
- **Tailwind CSS v4** via the `@tailwindcss/vite` plugin (no PostCSS, no `tailwind.config.js`). Theme tweaks live in `src/styles/global.css`.
- **Bun** as package manager and task runner. Always use `bun`, never `npm`.
- **GitHub Pages** deployment via the workflow at `.github/workflows/static.yml`.

## Common commands

```bash
bun install          # install deps (CI uses --frozen-lockfile)
bun run dev          # local dev server at http://localhost:4321
bun run build        # produce dist/
bun run preview      # serve dist/ locally for verification
bun astro sync       # regenerate content-collection types after schema changes
```

There are no tests or linters configured.

## Architecture

- `src/pages/` is route-mapped: `index.astro` is the homepage, `now.astro` is `/now`, `writing/[...slug].astro` renders each essay. Add a new page = add a `.astro` file under `src/pages/`.
- `src/layouts/Base.astro` owns `<head>`, OG/Twitter tags, the canonical URL, and the global stylesheet import. Every page wraps its content in `<Base>`.
- `src/components/` holds the homepage sections (Hero, ProofStrip, Now, ProjectList, ProjectRow, WritingList, OpenTo, About, Elsewhere, Footer, Header) plus a small `SectionHeading` helper. The homepage is just these components composed in order.
- `src/content.config.ts` defines two content collections, both backed by `src/content/{name}/*.md`:
  - **`projects`**: each `.md` carries `title, year, status, kind, description, links`. The body is currently unused, reserved for future detail pages.
  - **`writing`**: each `.md` carries `title, date, description, draft`. Setting `draft: true` hides the entry from the writing index. The body is the essay itself.

## How to add content

- **New project**: drop a markdown file into `src/content/projects/`. It appears in the homepage Projects section automatically, sorted by year (newest first), grouped under year separators. Set `kind` to one of `innogames | side | experiment` and `status` to one of `live | building | archived | experiment | tbd`.
- **New essay or build note**: drop a markdown file into `src/content/writing/`. With `draft: false` it appears at `/writing` and at `/writing/<filename-without-extension>`. Standard markdown works; the article page applies inline Tailwind utilities to elements (`h2`, `h3`, `p`, `code`, `pre`, lists, links) so no plugin is needed.

## Writing architecture

Two separate sites by design:

- `surdu.de/writing` (this repo) holds **curated, finished essays and build notes**, written for an audience.
- `razvan-andrei-surdu.eu` is a separate site syncing from an Obsidian vault and holds **raw study notes and reading**. It is linked from the homepage (`<Elsewhere>`) and from the writing-index empty state, but content is never duplicated between the two.

## Deploy

`.github/workflows/static.yml` runs on every push to `main` (and via manual dispatch). The build job installs Bun, runs `bun install --frozen-lockfile`, then `bun run build`, and uploads `./dist` as the Pages artifact. The deploy job publishes it. There is no preview/staging environment: pushing to `main` is publishing.

`bun.lock` is committed (the workflow needs it for `--frozen-lockfile`).

## Conventions

- Contact email used on the page is `andrei@surdu.de` (matches the git user). Hero CTA points to `#projects` (anchor on the homepage); secondary CTA is the email link.
- The page is dark-only (slate-900 background, slate-200 text). Don't add a theme toggle without asking.
- Footer year auto-updates via `new Date().getFullYear()` in `Footer.astro`. Don't hard-code it.
- The `README.md` on `main` renders on the GitHub profile (the `andreiRS/andreiRS` special-repo trick) and is **not** project documentation.
- Research notes for the redesign live in `research/`. They are reference material, not content shipped to the site.

## Open content gaps

These are content placeholders that the site owner fills in over time. Don't invent new copy unless asked, but feel free to point them out:

- `Now.astro` items (last updated quarterly).
- Years and statuses for the InnoGames games and side projects flagged TBD in their markdown frontmatter.
- First real essays in `src/content/writing/` (the `hello-world.md` placeholder is `draft: true`).
- Project screenshots/videos (will require enabling per-project detail pages under `src/pages/projects/`).
