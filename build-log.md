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

## 2026-08-17 — Org created

- New Agentforce Developer Edition org is live. Login domain saved to local `.env` (not in this file — gitignored on purpose).
- Next: enable Data Cloud on this org, then Einstein Generative AI, then Agentforce, per the setup order confirmed above. Then start on the Home page.

## 2026-08-17 — Digital Experiences setup steps

1. **Enable Digital Experiences**
   - Setup → Quick Find → type "Digital Experiences"
   - Open Digital Experiences settings
   - Check the "Enable Digital Experiences" checkbox

2. **Create the site**
   - Go to All Sites
   - Click "New"
   - Choose the Customer Portal template

3. **Upload images**
   - App Launcher → Files
   - Go to the Asset Library
   - Click "Upload Files" — supports multi-select, so upload all images in one batch instead of one at a time
   - Do this before building out pages, so images are already available when adding them to components later.

4. **Open Experience Builder and set the theme**
   - Setup → Digital Experiences → All Sites
   - Click "Builder" on the portfolio site (goes directly into Experience Builder since the site already exists)
   - In Builder, go to the Theme panel
   - Select the **Cypress** theme

## 2026-08-18 — Added Projects page, clarified Certifications page

- Page 3 clarified: "Certifications" also includes Trailhead superbadges, not just certifications.
- New page added: **Projects** — showcase of built projects. Sits between Certifications and Contact Me. Site is now 5 pages total (Home, About Me, Certifications & Superbadges, Projects, Contact Me).

## 2026-08-18 — Creating pages in Experience Builder

1. In Builder, left-hand sidebar → **Add new page**
2. Choose **New Blank Page**
3. Choose **Flexible layout**
4. Repeat for each page

- All 5 pages created this way: Home, About Me, Certifications & Superbadges, Projects, Contact Me.

## 2026-08-18 — Header navigation

- Added all 5 pages to the default header navigation menu so they show across the top of the site: Home, About Me, Certifications & Superbadges, Projects, Contact Me.
- Confirmed: no 6th page — the earlier "6" was a slip, 5 is correct.

## 2026-08-18 — Footer navigation tile

- Tile navigation lives on the Home/landing page itself, linking out to the other 4 pages: About Me, Certifications & Superbadges, Projects, Contact Me. Home isn't shown as a tile since you're already on it.
- Added an image to each of the 4 tiles, pulled from the images already uploaded to the Asset Library.

## 2026-08-18 — Gotcha: header overlay

- By default, the Cypress theme header has a solid white background, so it pushes page content down instead of floating over it.
- Fix: select the header region in Builder → change the header background opacity (lower it / make transparent) → this turns the header into an overlay that sits on top of the page content instead of a solid bar above it.

## 2026-08-18 — Salesforce CLI authenticated

- `sf org login web --alias portfolio --instance-url <SF_MY_DOMAIN>` — browser-based login, no credentials typed into the terminal.
- First attempt showed "site can't be reached" in the browser (likely a redirect/localhost-callback hiccup); re-running the exact same command succeeded on the next try.
- Org now saved locally under alias **portfolio**, status Connected. Verify anytime with `sf org list`.
- This unlocks pulling real site metadata into this repo with `sf project retrieve` — actual config files, not just narrated notes. Still need to scaffold a minimal SFDX project (`sfdx-project.json` + manifest) before that command will work — next step.

## 2026-08-18 — Contact Me Flow (decision + how-to)

**Decision: Flow over Web-to-Lead.** Web-to-Lead is legacy, spam-prone without reCAPTCHA, and sits awkwardly as a bolted-on HTML form inside Experience Builder. Flow is native to the Builder page, fully stylable, extensible later (e.g. toward Agentforce), and demonstrates actual Flow Builder skill — more relevant for a portfolio than a legacy form.

**How to build it:**
1. Setup → Quick Find → "Flows" → New Flow → **Screen Flow**
2. Build a Screen with input components: First Name (Text), Last Name (Text, required), Email (Email component, required), Company (Text, required — Lead object requires Company on every record), Phone (Text, optional), Message (Long Text Area, optional → maps to Lead Description)
3. Add a **Create Records** element after the screen: Object = Lead, manually map each field to the matching screen component value
4. Add a second Screen after that as a confirmation ("Thanks for reaching out — I'll get back to you soon.")
5. Save (e.g. "Portfolio Site - Contact Me") and **Activate** — inactive flows don't show up in Experience Builder's component list
6. **Critical gotcha:** the site is public, so visitors hit it as **Guest User**. The Guest User profile needs Create access to Lead (and edit access on the fields being set), or the flow works fine when testing as yourself but silently fails for every real visitor. Setup → Digital Experiences → All Sites → site → Public Access Settings → Object Settings → Leads → enable Create.
7. Experience Builder → Contact Me page → drag the **Flow** component onto the page → select this flow → Publish.
