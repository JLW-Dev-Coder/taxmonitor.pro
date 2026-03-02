# AGENTS.md

## Project: Tax Monitor Pro — Pages Layer

This repository builds the static site and app shell for:

https://taxmonitor.pro

It deploys via Cloudflare Pages.

Worker API lives separately at:
https://api.taxmonitor.pro


## Build Contract

Build command:
node build.mjs

Output directory:
dist

dist/ must never be committed.


## Repository Scope

This repo:
- Serves static pages
- Injects partials via build.mjs
- Copies assets/styles/legal into dist
- Serves app under /app/*

This repo does NOT:
- Run the Worker
- Require Wrangler
- Require npm
- Require package.json
- Require node_modules

Node built-ins only.


## Working Directory Rule (CRITICAL)

Agents must NOT assume a fixed path such as:

/workspace/taxmonitor.pro-site

Agents must:
1. Locate build.mjs
2. cd to that directory
3. Execute build from that directory

Hardcoded paths are forbidden.


## Domain & Routing Contract

Pages:
https://taxmonitor.pro

Worker API:
https://api.taxmonitor.pro

Forms must POST absolute to:
https://api.taxmonitor.pro/forms/*

Relative form actions are forbidden.


## Redirect Safety

_redirects must not contain catch-all rewrites that swallow valid pages.

Examples of forbidden rules:

/* /index.html 200
/app/* /app/index.html 200

Unless explicitly documented.


## Audit Enforcement

Audit scripts must:
- Use Node built-ins only (fs, path, url)
- Not install dependencies
- Not modify existing HTML/CSS
- Not require environment variables

Missing referenced files = Blocker.
Missing dist pages = Blocker.
Incorrect form POST endpoints = Blocker.


## Commit Scope Rules

When performing audit tasks:

Allowed:
- /audit/**
- LAUNCH_READINESS.md

Forbidden:
- Editing app HTML
- Editing site HTML
- Editing CSS
- Editing Worker files
- Editing build.mjs (unless explicitly authorized)


## CI / Codex Safety

Never run:
npm install
npm ci
npm run build

Always run:
node build.mjs


## Stability Rule

If build behavior changes, update:
- AGENTS.md
- LAUNCH_READINESS.md
- audit scripts

Together.
