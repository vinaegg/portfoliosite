# Salesforce Experience Portfolio Site

**Status:** live — site built, Agentforce agent working end-to-end, source retrieved into this repo as a backup
**Purpose:** A Salesforce Experience Cloud site to showcase Salesforce skills as a portfolio piece for job applications.
**Live site:** https://vinaegg-dev-ed.develop.my.site.com/portfolio/s/
**Started:** 2026-08-17

## Why this repo exists
Original build was in a Salesforce Developer Free Edition org. Lost login access once already and lost all the work with it (2026-08-17). This repo is the fix: real SFDX source pulled from the org via `sf project retrieve`, committed to git, so the whole build — site config, Flows, the Agentforce agent — can be reconstructed or redeployed even if org access is lost again. `build-log.md` has the full step-by-step history; the detailed Agentforce debugging narrative (CSP issue, permissions, routing bugs) lives in the sibling project doc at `../EGG EA/projects/salesforce-portfolio-agent/README.md`.

## Pages
1. **Home** — landing page with a "Contact Me" button that links to the Contact page
2. **About Me** — bio/background
3. **Certifications & Superbadges** — carousel component showing all certifications and Trailhead superbadges
4. **Projects** — showcase of built projects
5. **Contact Me** — embeds a Flow; submitting the Flow creates a Lead record in Salesforce CRM

## Agentforce agent — "Ask Vina" (not a page — a site-wide chat feature)
- Agent name: `Ask_Vina` (Bot metadata), Agent Script version `Ask_Vina_v2` (GenAiPlannerBundle) — see `force-app/main/default/genAiPlannerBundles/Ask_Vina_v2/agentScript/Ask_Vina_v2_definition.readable.txt` for the full, human-readable Agent Script (the `.agent` file next to it is the same content base64-encoded, which is the actual Salesforce metadata format).
- Multi-topic architecture: an `agent_router` routes each question to a focused subagent — `cv_qa` (answers from real CV content), `off_topic` (redirects), `ambiguous_question` (asks for clarification), `escalation` (live-agent handoff).
- Grounded on Salesforce Knowledge via a **custom Flow-backed action** (`force-app/main/default/flows/Get_Vina_CV_Content.flow-meta.xml`) — not Data Cloud. The standard `answerQuestionsWithKnowledge` action requires a Data Cloud RAG Feature Configuration, unavailable in a free Developer Edition org, so this Flow was built as a workaround.
- Deployed via Enhanced (Embedded) Messaging (`MessagingChannel` + `EmbeddedServiceConfig` metadata) with direct agent routing (Routing Type: "Agentforce Service Agent") — no Omni-Channel dependency.
- Runs under its own dedicated integration user, permissioned via `Ask_Vina1618387231_Permissions` (PermissionSet metadata) — needs Read access on the Knowledge object and the custom `CV_Content__c` field for the grounding Flow to work.

## Org details
Credentials are **not** stored in this file. They live in `.env` (gitignored, local-only, never pushed). `.env.example` is tracked and shows which keys are needed — copy it to `.env` and fill in real values.

## Files
- [build-log.md](build-log.md) — step-by-step build log, updated as we go
- `.env.example` — template of required keys (tracked, no real values)
- `.env` — actual org credentials, local-only (gitignored)
