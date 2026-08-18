# Salesforce Experience Portfolio Site

**Status:** rebuilding from scratch (previous Developer Edition org was lost — couldn't log back in)
**Purpose:** A Salesforce Experience Cloud site to showcase Salesforce skills as a portfolio piece for job applications.
**Started:** 2026-08-17

## Why rebuilding
Original build was in a Salesforce Developer Free Edition org. Lost login access and lost all the work with it. This time: document every step in [build-log.md](build-log.md) as we go, so it can be reconstructed quickly if access is lost again.

## Pages
1. **Home** — landing page with a "Contact Me" button that links to the Contact page
2. **About Me** — bio/background
3. **Certifications & Superbadges** — carousel component showing all certifications and Trailhead superbadges
4. **Projects** — showcase of built projects
5. **Contact Me** — embeds a Flow; submitting the Flow creates a Lead record in Salesforce CRM

## Agentforce agent (not a page — a site-wide feature)
- An Agentforce agent grounded on Knowledge Articles / Data Cloud data built from Vina's CV and background info
- Guardrails configured on the agent (topics/scope it should stay within)
- Org confirmed: needs the Agentforce Developer Edition specifically (see build-log.md, 2026-08-17)

## Org details
Credentials are **not** stored in this file. They live in `.env` (gitignored, local-only, never pushed). `.env.example` is tracked and shows which keys are needed — copy it to `.env` and fill in real values.

## Files
- [build-log.md](build-log.md) — step-by-step build log, updated as we go
- `.env.example` — template of required keys (tracked, no real values)
- `.env` — actual org credentials, local-only (gitignored)
