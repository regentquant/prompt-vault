# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Prompt Vault is a static single-page web app — a curated library of AI prompts with category filtering, search, copy-to-clipboard, and Firebase-backed copy counts. The entire app lives in one file: `index.html`.

## Architecture

**Single-file app** (`index.html`): contains all HTML, CSS (in `<style>`), and JavaScript (in `<script>`). No build step, no bundler, no framework.

- **Prompt data**: hardcoded in the `PROMPTS` array (JS objects with `id`, `title`, `category`, `description`, `prompt`, `tags`). To add/edit/remove prompts, modify this array directly.
- **Rendering**: vanilla JS functions (`renderNav`, `renderPrompts`) rebuild the DOM from `PROMPTS` on every filter/search change. No virtual DOM or reactivity library.
- **Firebase**: Firebase Realtime Database (compat SDK loaded via CDN) tracks per-prompt copy counts. Config is inline. Copy counts use `ref.transaction()` for atomic increments and `.on('value')` for live updates.
- **Styling**: CSS custom properties in `:root` control the design system (colors, sidebar width). Responsive layout via `@media (max-width: 768px)` — sidebar becomes horizontal nav on mobile.

## Development

No build or install commands. Open `index.html` in a browser to develop locally. Copy counts require internet (Firebase).

## Deployment

GitHub Pages via `.github/workflows/deploy.yml` — auto-deploys the repo root on push to `main`.

## Adding New Prompts

When adding a new prompt to the `PROMPTS` array, always check and adjust:

1. **Category**: Pick the best existing category. If none fits, create a new one — categories are derived automatically from the data.
2. **Title**: Clean, concise title. Remove version numbers, "PROMPT" suffixes, or ALL-CAPS formatting.
3. **Description**: One sentence summarizing what the prompt does and why it's useful.
4. **Content**: The `prompt` field must always start with `# Title` as the first line of the markdown. Clean up any formatting artifacts from the source file.

# AUTO Plan & Code

1. Before writing or editing any code, first output a detailed step-by-step plan (files to modify, specific changes, rationale)
2. Immediately execute the full plan without pausing for confirmation

# AUTO Commit Workflow

- **Commit and push once per task**, not after every individual file change
- A "task" is one logical unit of work (e.g., a feature, a bug fix, a refactor) — bundle all related file changes into a single commit
- Use `git commit -m "subject line"` with a concise, descriptive subject line only (no body, no multi-line)
- Stage only the files you modified: `git add <specific-files>` then commit
- Push to remote after the commit: `git push`
- Do NOT use `--no-verify` or skip hooks unless explicitly asked
