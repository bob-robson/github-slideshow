# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A GitHub Learning Lab "Introducing GitHub" course repo: a Jekyll site (built for GitHub Pages) that renders a [reveal.js](https://github.com/hakimel/reveal.js/) slide deck. Course participants add slides by committing Markdown files to `_posts/`. It also hosts an unrelated standalone page, `pombo-crazy/index.html`.

## Commands

Requires Ruby + Bundler (the `github-pages` and `html-proofer` gems from the `Gemfile`).

- `script/setup` installs gems (`bundle install`) and runs `git submodule update --init`.
- `script/server` serves locally with live rebuild (`bundle exec jekyll serve`); extra args are passed through.
- `script/cibuild` is the CI check: `jekyll build --baseurl "."` followed by `htmlproofer _site/index.html --empty-alt-ignore`. There are no other tests or linters.
- `script/stage` is legacy GitHub-internal tooling (pushes to `ghe.io`); don't use it.

## Architecture

- **Slides are Jekyll posts.** Each file in `_posts/` (`YYYY-MM-DD-name.md`, front matter `layout: slide` and `title:`) is one slide. `index.html` loops `site.posts reversed`, so slides appear in **ascending date order**. The intro slide is dated `0000-01-01` to stay first.
- **Rendering chain:** `index.html` (layout `presentation`) → for each post, `_includes/slide.html` wraps it in a `<section>` (the post title becomes an `<h1>`; optional front matter `slide-id`, `classes` and `data` map to the section's id, classes and `data-*` attributes) → `_layouts/presentation.html` puts everything inside `.reveal .slides` and includes `head.html` (CSS) and `script.html` (JS). `_layouts/slide.html` is used only when a post is viewed on its own page (permalink `/:title`). `_layouts/print.html` is unused by the deck.
- **reveal.js is vendored, not installed.** `node_modules/reveal.js` is committed to git and there is no `package.json`; edit or replace those files directly rather than running npm. Asset paths in `_includes/` reference `node_modules/reveal.js/...` (CSS uses `{{ site.baseurl }}`, the plugin dependencies in `script.html` are relative).
- **The `reveal:` block in `_config.yml` is not wired up.** `_includes/script.html` calls `Reveal.initialize` with hardcoded options (`hash: true` plus the markdown/highlight/notes plugins), so changing presentation behavior means editing `script.html`. The `_config.yml` values that do take effect are things like `title`, `solarized.theme` (the `<html>` class), `permalink`, `markdown`/`highlighter` and `exclude`.
- **`pombo-crazy/index.html`** is a self-contained, no-dependency animation (inline CSS/JS, Web Audio for music/SFX, canvas rendering, Portuguese UI and voice lines). It has nothing to do with the slide deck; Jekyll copies it to `_site/pombo-crazy/` as-is.

## Conventions

- `.editorconfig`: tabs by default; 2 spaces for JSON/JS/CSS/SCSS/YAML/HTML; 4 spaces for Markdown (with a trailing newline).
