# AGENTS.md — Tax Monitor Pro (taxmonitor.pro-site)

## Purpose
This repository powers:
- https://taxmonitor.pro (Cloudflare Pages UI)
- https://api.taxmonitor.pro (Cloudflare Worker API)

This is a revenue-critical system.

Agents must prioritize correctness, contracts, and minimal diffs over refactoring or aesthetic improvements.

---

# Core Architectural Rules

## R2 Is Authority
All state flows must follow:

receipt append → canonical R2 upsert → ClickUp projection

Never:
- Write directly to ClickUp before R2 update
- Mutate canonical state outside Worker logic

---

## Domain & Routing Contract

Pages:
- Served from https://taxmonitor.pro

API:
- Served from https://api.taxmonitor.pro

All forms must POST to:
https://api.taxmonitor.pro/forms/*

Never:
- Use relative form actions (e.g. /forms/...)
- Use localhost endpoints
- Introduce alternate API domains

---

## Worker Rules

Worker file:
workers/api/src/index.js

Rules:
- Validate payloads against contracts
- enumStrict = true
- normalizeCheckboxToBoolean = true
- rejectUnknownValues = true
- Append receipt before canonical mutation
- Deduplicate by eventId
- Stripe dedupe key = Stripe Session ID

Never:
- Bypass receipt append
- Modify R2 key structure
- Invent new lifecycle steps
- Add new ClickUp custom fields

---

## Contracts

Source of truth:
app/contracts/

Contract registry:
app/contracts/contract-registry.json

Agents must:
- Never remove contracts without justification
- Never rename endpoints casually
- Ensure page POSTs match contract endpoints exactly

If a page posts to a Worker endpoint:
- A contract must exist
- The Worker must implement it
- The endpoint must be absolute

---

## Build System

Build file:
build.mjs

Dist directory:
dist/

Rules:
- Do not change output structure without explicit instruction
- Ensure site/*, app/*, assets/*, legal/*, styles/*, _redirects are correctly emitted
- Partial injection must not modify unrelated HTML

---

## Forms

All form submissions must include:
- eventId
- sessionToken (if required by lifecycle phase)

Never:
- Remove eventId usage
- Change stepBoolean names
- Introduce new lifecycle steps

---

## Compliance + 2848

Section 3 values are locked:
- Tax type
- Forms list
- Years 2016 - 2025

These values must:
- Be read-only
- Be re-applied on page load
- Not be modified without explicit instruction

---

# Modification Rules

Agents must:
- Prefer smallest possible diff
- Avoid refactoring unless required to fix a blocker
- Avoid renaming files
- Avoid reorganizing directories
- Avoid adding dependencies

When fixing:
- Fix only the identified issue
- Do not improve unrelated code
- Do not reformat entire files

---

# Launch Readiness Criteria

A launch blocker includes:
- Missing dist assets/pages
- Broken internal links
- Form posting to incorrect endpoint
- Worker endpoint missing for referenced route
- Contract mismatch

Warnings:
- Unused assets
- Minor redundancy
- Cosmetic inconsistencies

---

# Explicit Non-Goals

Do NOT:
- Migrate frameworks
- Introduce TypeScript
- Introduce new build tooling
- Redesign UI
- Change lifecycle model
- Modify R2 key structure

---

# Tone of Changes

This is production software tied to revenue.
Stability > creativity.
Precision > cleverness.
Minimalism > refactor enthusiasm.
