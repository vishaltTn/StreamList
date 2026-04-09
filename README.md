# StreamListApp

**Purpose:** Build the StreamListApp product behind a shared engineering baseline—modular code, env-based config, and recorded decisions.

## Repository contract first

Rules, environment templates, and ADRs are defined **before** feature work so the codebase stays consistent as it grows.

## Getting started

1. Copy `.env.example` to `.env` and fill in values locally (never commit `.env`).
2. Read `docs/ADR.md` for how we record technical decisions.
3. Use `prompt-logs/` for important AI prompts or workflow notes you want to preserve for the team.

## Project principles

- **No hardcoded secrets** — credentials and tokens stay out of the repo.
- **Configuration via environment variables** — use `.env` locally; document keys in `.env.example` only as placeholders.
- **Modular architecture** — separate concerns so features can evolve independently.
- **No inline data fetching in UI** — keep presentation separate from networking and side effects.
- **Document significant decisions** — add or update entries in `docs/ADR.md` when architecture or stack choices change.
- **Log important AI workflows** — store notable prompts or agent workflows under `prompt-logs/` when they matter for repeatability or review.
