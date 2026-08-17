# Build Log

Append-only. Every setup step, config choice, and gotcha goes here as we go — the point is to be able to rebuild this site from zero using only this file if the org gets lost again.

---

## 2026-08-17 — Project restart

- Previous Developer Edition org lost (login access gone, no recovery). All prior Experience Cloud build work lost with it.
- Decision: this time, document every step here as we go, and store org credentials somewhere durable outside this repo (password manager) — not relying on memory or a single login session.
- Next: Vina to confirm which pages the site needs, then create a fresh Developer Edition org and start rebuilding.

## 2026-08-17 — Repo setup + secrets handling

- Moved project out of the EGG EA repo into its own folder/repo: `/Users/vinaegg/Documents/AI Projects/Salesforce Experience Portfolio Site/`. Reason: this may become a public portfolio piece to show employers, and EGG EA contains personal job-search data — kept separate on purpose.
- Git repo initialized locally, first commit made. GitHub remote not yet created (need `gh` CLI or manual repo creation — pending Vina's choice).
- Credentials: using `.env` (gitignored) for real values + `.env.example` (tracked) as the template, matching the same pattern already used in the EGG EA repo for `CLICKUP_API_KEY`. Password itself still goes in a password manager, not in `.env`.
- `.gitignore` also blocks Salesforce CLI local auth dirs (`.sf/`, `.sfdx/`) so `sf org login` sessions never get committed either.

## 2026-08-17 — Site scope defined

- 4 pages: Home (with Contact Me button) → About Me → Certifications (carousel) → Contact Me (embeds a Flow; submitting creates a Lead in Salesforce CRM).
- Plus a site-wide Agentforce agent, grounded on Knowledge Articles / Data Cloud data built from Vina's CV + background info, with guardrails configured.
- Open question flagged: does a standard free Developer Edition support Data Cloud + Agentforce, or is an Agentforce-specific trial org needed? Need to confirm before creating the new org, since this decides which signup path to use. Researching this now.

## 2026-08-17 — Org type confirmed: Agentforce Developer Edition

- Answer: a standard free Developer Edition does **not** support Data Cloud or Agentforce. Must sign up for the **Agentforce Developer Edition** specifically — a plain Developer Edition can't be retrofitted.
- Signup: https://developer.salesforce.com/free-trials → select the Agentforce Developer Edition option. Free, non-expiring as long as you log in regularly.
- Data Cloud is a hard requirement for grounding an agent on Knowledge Articles — not optional.
- Setup order matters: **enable Data Cloud → Einstein Generative AI → Agentforce**, in that sequence.
- Free tier: 150 LLM requests/hour. Fine for a portfolio demo. No paid add-ons needed.
- Full research notes: `references/research/haiku/2026-08-17-salesforce-agentforce-free-org-requirements.md` (in the EGG EA repo).
- Next: Vina to sign up for the Agentforce Developer Edition, save credentials to `.env`, then start building — Home page first.
