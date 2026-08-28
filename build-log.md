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
6. **Critical gotcha:** the site is public, so visitors hit it as **Guest User**. The Guest User profile needs Create access to Lead (and edit access on the fields being set), or the flow works fine when testing as yourself but silently fails for every real visitor — including any Send Email actions after it, since the flow errors out at the failed Create step and never reaches them.
   - **How to reach the Guest User profile (verified working path):** Setup → Quick Find → "Users" → find the Site Guest User for this site → open it → click through to its Profile. (Quick Find → "Profiles" directly does NOT reliably list guest profiles, and Digital Experiences → All Sites → Workspaces → Administration → Pages does NOT have a "Public Access Settings" button in this org/release, despite older docs referencing one — both dead ends, confirmed.)
   - On the profile: **Object Settings** → **Leads** → **Edit** → check **Create** + **Read** (add Edit too if fields aren't populating) → Save.
   - Status: Create + Read saved on Leads for Guest User. Next: retest the Contact Me form as an actual guest (incognito/logged out) to confirm the Lead is created and both emails send.
7. Experience Builder → Contact Me page → drag the **Flow** component onto the page → select this flow → Publish.

**Gotcha: Subject/Body fields won't mix literal text and merge fields.** The Subject field on the "Send Email" action only accepts one thing — plain text OR a single resource, not both combined. Fix: create a **Text Template** resource (Toolbox → New Resource → Text Template) and write the combined text with merge fields inline there (e.g. `New Contact Form Lead: {!FirstName} {!LastName}`), then set the Subject field to that Text Template resource instead of typing directly.

**Gotcha: emails arrive as plain text / raw HTML tags show up literally (e.g. `<p>...</p>` visible in the inbox).** The standard "Send Email" core Flow action only sends plain text — it does not render HTML at all, even if the Text Template feeding it was built in rich-text mode. Decision: fine to leave the "email to me" notification as plain text. If rich HTML is wanted later (e.g. for the guest confirmation email), the real fix is to swap "Send Email" for an **Email Alert** (Setup → Email Templates → HTML template with `{!Lead.FieldName}` merge fields, then Setup → Email Alerts → New, object = Lead, pick the template), triggered via a **"Send Email Alert"** action passing in the Lead's Id (requires "Store Record ID" enabled on the Create Records element).

**Design note: redirecting to the existing Thank You page after submission.** Site already has a separate Thank You page built in Experience Builder. Simple/declarative approach: on the flow's final screen, use a Display Text component with a hyperlink to the Thank You page's URL instead of plain confirmation text. (A fully automatic redirect with no click is possible but needs a custom Lightning component with `NavigationMixin` — deferred as a phase-2 polish item, not blocking.)

**Gotcha: Create Records fails with `DUPLICATES_DETECTED`, blocking the whole interview.** Salesforce's standard Lead Duplicate Rule blocks creation when a Lead with matching email/name already exists (easy to hit while testing repeatedly with the same info — but also a real risk in production if a visitor submits twice). Since Create Records faults, the flow interview stops right there and nothing downstream runs — neither Send Email action fires. Diagnosed via the automatic Flow Error email Salesforce sends to the flow owner on unhandled faults (check inbox for "Unhandled Fault" after a failed test — it shows the exact error and field values).
- Fix: Setup → Quick Find → "Duplicate Rules" → open the standard Lead duplicate rule → Edit → change **Action on Create** from Block to **Allow** → Save. Keeps duplicate flagging for internal users browsing Leads, but stops silently rejecting real contact form submissions.

**Decision: notification email stays plain text.** Classic HTML Email Templates (needed for the Email Alert route to get real HTML rendering) were too much friction for an internal-only email nobody but Vina reads. Reverted to the simple "Send Email" action, but with the Text Template body laid out with headers/line breaks/separators so it reads cleanly even as plain text, instead of a wall of unformatted text. The `newLeadID` text variable built for the Email Alert route was left in place unused — harmless.

## 2026-08-19 — Publishing and guest-access troubleshooting

- Published the site and tested as guest (incognito). First load showed Salesforce's generic **"Sorry to interrupt... CSS Error"** page — turned out to be transient; a plain incognito reload showed real content.
- **Tiles missing for guests → fixed.** Cause: Guest User profile didn't have Read access to Documents/Files, so the tile images (uploaded via Asset Library) weren't visible to anonymous visitors even though the component itself rendered fine. Fix: on the Guest User profile → Object Settings → **Files/Documents** → enable **Read**. Confirmed working — tiles now show.
- **Contact Me Flow not showing for guests → SOLVED.** Confirmed via a logged-in-vs-incognito split test that this was guest-specific (worked fine logged in, blank in incognito) — not a general LWR/Flow compatibility problem. Browser console showed no relevant error (only an unrelated harmless WebVitals telemetry warning). Ruled out along the way: Component Visibility (doesn't exist on this Flow component), "Manage User Access" action on the Flows list page (doesn't exist in this org/release), Public Access Settings button (doesn't exist, per earlier entry).
  - **Root cause:** by default, a flow isn't in a state where it can be individually assigned to any profile at all — "Enabled Flow Access" on a profile only shows flows that have been explicitly switched out of default access mode. This is why the list appeared completely empty (not just missing ContactForm — nothing would show until a flow is switched over).
  - **Fix, in order:**
    1. Setup → Quick Find → "Flows" (the list page) → find **ContactForm** → open its row-level actions dropdown → **Edit Access**
    2. Choose **"Override default behavior and restrict access to enabled profiles or permission sets"**
    3. Save
    4. Go to the Guest User Profile (reachable via Setup → Users → find the Site Guest User → open its Profile) → **Enabled Flow Access** → Edit — ContactForm now appears as available → add it → Save
  - Confirmed working after this — flow now renders for guests.

## 2026-08-19 — Email delivery saga: full resolution

With the flow now rendering and Lead creation working, both Send Email actions still weren't reliably delivering. Multiple layered issues, in the order they were found:

1. **"Insufficient privileges" when testing via Flow Builder's Run/Debug button.** Side effect of the "Edit Access → Override default behavior" fix above — once a flow is restricted to an explicit allow-list, only the profiles on that list can run it, including for admin testing. Fix: add your own profile (System Administrator) to the same Edit Access allow-list alongside the Guest Profile.

2. **Testing kept hitting `DUPLICATES_DETECTED` again.** Reusing the same test email address repeatedly re-triggers Salesforce's Lead duplicate rule (matches primarily on Email, regardless of name). Workaround for testing: use a Gmail `+alias`, e.g. `eggvina+test1@gmail.com` — Gmail ignores the `+part` so it still lands in the real inbox, but Salesforce sees it as a different address and won't flag a duplicate.

3. **Ruled out (not the cause):** Deliverability → Access Level was already "All Email," not restricted. This org's Deliverability page in this release doesn't show the classic layout at first glance — the Access Level dropdown is at the very top, easy to miss if you land mid-page.

4. **Root cause of silent (no-fault) delivery failure — traced via Salesforce CLI, not the UI.** Retrieved the flow's actual saved metadata directly (`sf project retrieve start -m "Flow:ContactForm" -o portfolio`) and inspected the XML rather than continuing to guess from screenshots. Found that **both** Send Email actions had a completely empty `recipientAddresses` parameter, and no `senderAddress` set at all — despite `senderType` being switched to `OrgWideEmailAddress` in the UI. The "Org-Wide Email provided is not valid" fault and the later "ran fine but nothing arrives" symptom were two different faces of the same underlying gap: sender and recipient are **separate fields** in the action's config panel ("Configure Sender Details" vs "Configure Recipient Details"), and it's easy to fill in one section and assume the other carried over.
   - **Fix:** in each Send Email action, fill in **both** sections:
     - Email_to_Guest → Recipient Addresses: `{!Email.value}` (or the equivalent "Contact Form > Email > Value" resource picker entry) · Sender Type: OrgWideEmailAddress · Sender Email Address: the verified org-wide address
     - Email_to_Me → Recipient Addresses: literal `eggvina@gmail.com` · same sender config
   - Org-Wide Email Address used: created via Setup → Organization-Wide Addresses, `eggvina@gmail.com`, "Allow All Profiles to Use this From Address" checked (needed since Guest User has no address of its own), then verified by clicking the confirmation link Salesforce emails to that address.

5. **Final gotcha: Save ≠ Activate.** Editing an already-Activated flow creates a new version on Save, but that new version only takes effect once separately **Activated** — otherwise guests keep running the old (broken) version indefinitely while Flow Builder's canvas shows your fixes looking correct. If a fix "doesn't seem to take," check the flow's version history for which version number is actually marked Active before troubleshooting further.

**CLI note for future debugging:** `sf project retrieve start -m "Flow:ContactForm"` does not reliably pull whichever version is currently Active — it kept returning stale/empty values even after the live site was confirmed working via real end-to-end testing. Trust actual behavior (real test submissions, real emails arriving) over this retrieve command when the two disagree. The CLI retrieve was still genuinely useful for the earlier diagnosis (finding the empty recipient/missing sender fields no screenshot had surfaced) — just don't rely on it as a final confirmation step.

**End state: Contact Me flow fully working** — guest can access the page, submit the form, a Lead is created, and both the guest confirmation email and the internal notification email arrive.

## 2026-08-19 — Domain rename: in progress

- Changed My Domain (Setup → My Domain) from `orgfarm-a6430190d2-dev-ed` to **`vinaegg-dev-ed`**. Updated in `.env` (`SF_MY_DOMAIN`). CLI needs re-auth against the new domain — run manually in Terminal (background/non-interactive attempts timed out, needs live browser interaction):
  ```
  cd "/Users/vinaegg/Documents/AI Projects/Salesforce Experience Portfolio Site"
  sf org login web --alias portfolio --instance-url https://vinaegg-dev-ed.develop.my.salesforce.com --set-default
  ```
- **Important distinction learned:** changing My Domain only updates the Salesforce *login* URL (`.my.salesforce.com`). The published Experience Cloud site's public URL (`.my.site.com`) is separate and does NOT update automatically — as of this session it was still showing the old `orgfarm-a6430190d2-dev-ed...my.site.com` domain after the My Domain rename.
- Next: check Setup → Quick Find → **"Domains"** (plural, different page from "My Domain") for a new entry matching the new name — may need manual **Activate**, and provisioning can reportedly take several hours. Not yet confirmed working — pick this up next session.

## 2026-08-19 — Certifications & Superbadges carousel: using CMS instead of the default Carousel

- **Gotcha:** the default "Carousel" component in Experience Builder's component panel is Einstein's *Recommendation Carousel* — not meant for static content like certification badges. Use **Salesforce CMS** instead: build a CMS Collection, then drop a CMS Collection component into Builder with Carousel as the display layout.
- Steps so far (in progress):
  1. System Administrator profile → enabled/unhid **CMS Workspaces** and **CMS Channels** so they're accessible (not visible by default)
  2. Also enabled the **Digital Experiences Home** tab
  3. In the CMS Workspace, created two separate content folders/sets — one for **Superbadges**, one for **Certifications** — and uploaded all the badge images into each, then **Published**
  4. Digital Experience → **Content Management** → **Collections** → **Create Collection** → named it (e.g. "Superbadges") → selected all the relevant CMS image content items to include in that collection
  - Repeat for a separate "Certifications" collection.
  5. Back in Experience Builder → **Certifications & Superbadges** page → laid out **two columns** → drag-and-dropped a **CMS Collection** component into each column, one pointed at the Superbadges collection, one at the Certifications collection (set to slider/carousel display) — no manual Channel-linking step needed, just direct drag-and-drop selection of the collection.
  - Content mapping per slide: image = Source, badge/cert name = Title (Headline field on the CMS content item). That's all that's needed for a slide to render — no other fields required.
  - Component has its own display settings in the Builder properties panel (independent of the CMS content itself): number of slides shown at once, autoplay/transition timing, etc. — adjust to taste.
- **Confirmed working end-to-end.**

## 2026-08-19 — Session wrap-up / status

**Pages done (3 of 5):** Home, Contact Me (Flow + email working end-to-end), Certifications & Superbadges (CMS carousel).
**Pages remaining:** About Me, Projects.
**Next session:** add Agentforce as a chat/omnichannel widget on the site, grounded on Vina's CV + basic info (Knowledge Articles / Data Cloud, per the earlier org-type research), with guardrails configured. This is the last major feature from the original scope — see README.md "Agentforce agent" section.
**Also still open:** domain rename propagation (see 2026-08-19 "Domain rename: in progress" entry above) — check Setup → Domains for activation status when picking this back up.

## 2026-08-24 — Agentforce build started: agent authoring vs. deployment are separate steps

Started building the site's Agentforce agent using Agentforce Studio's own conversational agent-builder (the "Agentforce development agent" you talk to inside Setup to draft an Agent Script, distinct from the portfolio-facing agent being built).

**Prompt given:** "I want to build an assistant for my portfolio site that can answer guest's question about me grounded by my CV as guardrails."

**Proposed agent structure (draft, not yet confirmed/implemented):**
- **Name:** Vina_s_assistant
- **Welcome message:** "Hi! I'm the assistant for this portfolio. Feel free to ask me about the owner's background, skills, experience, education, or projects — I'm here to help."
- **System instructions:** Act as a knowledgeable, friendly representative of the portfolio owner. Stay grounded in CV content; never invent details not present.
- **Variables:** 5 existing/default (EndUserId, RoutableId, ContactId, EndUserLanguage, VerifiedCustomerId), unchanged.
- **Subagents (4):**
  1. `agent_router` (start agent) — greets the user, routes by intent: skills/experience/work history/background/education/projects/contact-availability → `profile_qa`; unrelated questions → `off_topic`; unclear intent → `ambiguous_question`
  2. `profile_qa` — answers grounded in CV content (no actions; CV text goes into the instructions as a knowledge block placeholder — has to be pasted in manually). Guardrails: never fabricate details not in the CV; if a detail's missing, say so honestly; stay warm/professional as a representative of the owner.
  3. `off_topic` — polite redirect for questions unrelated to the owner.
  4. `ambiguous_question` — asks clarifying questions when intent is vague.
- **Actions:** 0 custom actions — CV grounding is instruction-embedded text, not a Knowledge Article/Data Cloud lookup (a simpler approach than the Data Cloud/Knowledge Article grounding originally scoped in README.md — worth deciding which approach to actually use before implementing).

**Important scope clarification (asked before implementing, confirmed by the Agentforce builder):** the Agent Script builder tool only authors/compiles the agent's logic — it does **not** deploy or publish, configure Omnichannel routing/queues, set up an Embedded Service Deployment (the chat widget), or wire the Messaging Channel. Those are separate manual steps in the org:
1. Deploy the agent (Agentforce CLI or Agent Builder)
2. Create a **Messaging for In-App and Web** channel
3. Set up an **Embedded Service Deployment** and embed the generated snippet in the portfolio site's HTML
4. Configure **Omnichannel** to route conversations from that channel to the agent

**Status:** structure proposed, not yet confirmed/implemented. CV content not yet pasted in. Deployment/channel/embed steps not started.

## 2026-08-24 — Knowledge Article content drafted + full build plan

**CV updated.** Vina supplied a newer CV export (`VinaEggCore2CV.pdf`, saved in the EGG EA repo's `projects/CVs/`). Content is materially the same as the existing `V_Egg_CV_core.md` (already includes this exact project as a listed project — see its Projects section), with one correction applied: career break is **11 years, Jan 2014 – Sep 2025** (was previously logged as 12 years / Dec 2013). `V_Egg_CV_core.md` updated to match.

**Knowledge Article content drafted.** 13 articles written and saved to this repo's `knowledge/` folder (`01-summary.md` through `13-contact.md` — see `knowledge/README.md` for the full index). Each file is ready to copy title+body directly into a Knowledge Article in Setup. Deliberately excludes phone number and birthdate (on the CV, but no reason for a public chatbot to recite them) — keeps email + LinkedIn since those are meant to be public contact points.

**Decision: grounding via Data Cloud Data Library + Knowledge Articles**, not the pasted-CV-into-instructions approach the Agentforce Studio builder first proposed on 2026-08-24 above. This matches the original README scope and the 2026-08-17 org-type research (Agentforce Developer Edition was specifically chosen to support Data Cloud). The `profile_qa` subagent from the earlier draft will need a knowledge-search action wired to the Data Library, instead of (or in addition to) instruction text.

**Full build plan (not yet executed — org session (`portfolio` alias) needs re-auth first, see 2026-08-19 domain rename entry for the login command pattern, re-point at current My Domain):**

1. **Create the Knowledge Articles**
   - Setup → Quick Find → "Knowledge" → enable Knowledge if not already (this org may need Salesforce Knowledge turned on — check first, it's a separate feature from Data Cloud/Agentforce)
   - Create 13 Knowledge Articles, one per file in `knowledge/`, using the article's `#` heading as Title and the rest as Body
   - Publish each article (draft articles won't be indexed for grounding)

2. **Create/confirm a Data Cloud Data Library and ingest the Knowledge Articles**
   - Setup → Quick Find → "Data Cloud" → set up a Data Library (or confirm one already exists from earlier Data Cloud enablement)
   - Connect Knowledge as a data source into that Data Library, map it to index the 13 published articles
   - Exact path may differ from expectation here — this is the first time this org's Data Cloud config is being touched since the 2026-08-17 enablement; document the actual working path once found, same as every other gotcha in this log

3. **Ground the agent on that Data Library**
   - In Agentforce Studio / Agent Builder, add a **Knowledge/Data Library retrieval action** to the `profile_qa` subagent (from the 2026-08-24 draft above), pointing at the new Data Library — this replaces the placeholder "paste CV text into instructions" approach
   - Keep the guardrails already drafted (never fabricate, admit when info isn't in the CV, stay warm/professional)
   - Confirm/finalize the rest of the draft structure (`agent_router`, `off_topic`, `ambiguous_question`) — was proposed but not yet confirmed with "Yes"/"Proceed"

4. **Deploy the agent**
   - Via Agentforce Studio's deploy/activate action once the structure above is confirmed

5. **Create a Messaging for In-App and Web channel**
   - Setup → Quick Find → "Messaging Channels" (or similar — verify exact label live) → New → Messaging for In-App and Web
   - Attach the deployed agent as the channel's routing target (this is the Omnichannel wiring — routes conversations from this channel to the agent instead of a human queue, or blends both)

6. **Set up the Embedded Service Deployment + embed on the site**
   - Setup → Quick Find → "Embedded Service" → New Deployment, linked to the Messaging channel above
   - Salesforce generates a JS snippet
   - Embed that snippet on the Experience Cloud site — likely via Experience Builder's page-level "Custom Head Markup" / Settings → Advanced, or a Theme-level script include; confirm the actual working option live (LWR sites have historically been fussier about custom script injection than Aura sites — flag if this becomes a gotcha)

7. **Test end-to-end as a guest** (same discipline as the Contact Me flow build: test logged out/incognito, not just as System Administrator — guest-access gotchas have bitten this project before, see 2026-08-19 entries)

**Next session:** start at step 1 once the `portfolio` org alias is re-authenticated.

## 2026-08-24 — Agent built and activated; researched the embed process

**Agent done.** Vina built and activated the agent directly in Agentforce Studio's own agent-builder (not via the manual topic/action drafting planned above), grounded on her CV. Live under the name **"Vina's assistant."** Knowledge Article/Data Cloud plan above was superseded by whatever grounding method Agentforce Studio's builder used internally — worth checking later which one it actually picked (Data Library vs. instruction text) if it matters for the "demonstrates Data Cloud skill" portfolio narrative, since her CV/Projects bullet specifically claims Data Cloud Data Library grounding.

**Researched current (Aug 2026) process for embedding an agent into an LWR Experience Cloud site.**

**Correction:** initially thought there was a "simple path" — enable "Agentforce (Beta)" in Digital Experiences Settings, drag a native Agent component onto the page. Verified against Salesforce Help (`experience.exp_cloud_agent_exp_builder.htm`) that this is wrong: **"Agentforce for Experience Builder (Beta)" is an internal authoring assistant that helps draft Text Block copy while building the site — not a visitor-facing chat widget.** There is no shortcut around the Omni-Channel/Messaging Channel/Embedded Service route below — it's the only way to get a visitor-facing agent onto an Experience Cloud site. Good news: it also happens to match the Omni-Channel/Enhanced Messaging claim already on Vina's CV and this project's README, so no tradeoff to weigh here.

**The only path:**

1. **Prerequisite:** Setup → Omni-Channel Settings → confirm Omni-Channel is enabled (should already be, from earlier org setup).
2. **Routing Configuration:** Setup → Routing Configurations → New. Name it, set Routing Model = Priority, set a capacity unit. Must exist *before* the queue.
3. **Queue:** Setup → Queues → New. Under Supported Objects, add **Messaging Sessions** (required for agent chat — easy to miss). Add yourself as a queue member. Attach the Routing Configuration from step 2.
4. **Omni-Channel Flow:** Setup → Flows → New → type = **Omni-Channel Flow**. Add a **Route to Work** element:
   - Record ID: new Text resource `recordId`, available for input
   - Service Channel: **Messaging for In-App and Web**
   - Route To: **Agent**
   - Agent: Vina's assistant
   - Fallback Queue: the queue from step 3
   Save and **Activate**.
5. **Messaging Channel:** Setup → Messaging Settings → New Channel:
   - Channel Type: **Enhanced Chat**
   - Deployment: **Web**
   - Domain: current My Domain URL (Setup → "My Domain")
   - Routing Type: **Agent** → select Vina's assistant
   - Fallback Queue: from step 3
6. **Embedded Service Deployment:** auto-created when the Messaging Channel is saved. Setup → Embedded Service Deployments → open it → **Switch to V2** (modern branded chat UI, not the old basic text widget) → publish.
7. **Add to the site:** Experience Builder → search components for **"Embedded Messaging"** → drag onto the page (likely a global/header region so it shows site-wide, matching the README's "site-wide feature" scope) → select the Messaging Channel from step 5 → Publish.
8. **Test as guest** (incognito) — this org has a track record of guest-permission gotchas (see the Contact Me flow saga, 2026-08-19 entries), so don't trust a logged-in-as-admin test alone.

**Not yet executed** — pending: (a) confirm what grounding the Agentforce Studio builder actually used, (b) re-auth the `portfolio` CLI alias if any of this needs CLI verification, (c) walk through steps 1–8 live and log any gotchas the same way as every other feature in this file.

## 2026-08-24 — Steps 1–4 walked through live

- **Routing Configuration** created: "Vina Portfolio Routing", Priority... actually **Routing Model = Most Available** (not Priority as the source article said — this org's UI offered Least Active / Most Available / External Routing, no "Priority" option existed). Overflow Assignee = Vina Egg (the human fallback, not the `EinsteinServiceAgent User` system user).
- **Queue** created: "Vina Portfolio Fallback Queue". Supported Objects = Messaging Session. Member = Vina Egg only (excluded EinsteinServiceAgent User, Integration User, Security User, OrgFarm EPIC — all system/unrelated users).
- **Omni-Channel Flow** "Portfolio Agent Routing" built. Gotcha: the `recordId` variable couldn't be created inline from the Route to Work element's "Record ID Variable" field — that inline "New Resource" only offers Text Template, not Variable. Had to create it via the Toolbox panel's own "New Resource" button instead (Variable, Text, Available for input), then select it from the element field afterward.
  - Route to Work config: Service Channel = "Messaging" (this org's picker just shows the umbrella channel category, not a literal "Messaging for In-App and Web" string — that specificity comes later in the actual Messaging Channel record). Route To = **Agentforce Service Agent** (not plain "Agent" — that dropdown has 6 options: Queue, Agent, Skills, Enhanced Bot, Agentforce Service Agent, Agentforce Employee Agent; Service Agent is correct for public/external site visitors, Employee Agent is for internal-only use). Agentforce Service Agent = Vina's assistant. Fallback Queue = Vina Portfolio Fallback Queue.
- **Messaging Channel:** first attempt hit a generic **"Messaging Channel Creation Failed. Something went wrong."** modal. Retried and it went through — transient, same pattern as the 2026-08-19 "CSS Error" gotcha on first publish. Channel Type = Enhanced Chat, sub-tab "In-App and Web". Channel Name = "Vina's Assistant". Omni-Channel Routing section: Routing Type = Agentforce Service Agent, Agentforce Service Agent = Vina's assistant, Fallback Queue = Vina Portfolio Fallback Queue. Left all optional sections (User Verification, Chat Button Visibility, Session-Based Chat, Attachments, Transcript Downloads, Fallback Message, Abandoned Chats, reCAPTCHA, Queue Wait Time, Automated Responses, Opt-Out settings) at defaults.

**Next:** Step 5, Embedded Service Deployment — should auto-generate from the Messaging Channel above; open it, switch to V2, publish.

## 2026-08-24 — Step 5 done, then a real gotcha found before step 6: site is Aura, not LWR

- **Step 5 done.** Messaging Channel creation initially failed with a generic error, retried successfully. Deployment auto-created; clicked Switch to V2, then Switch & Publish. Confirmed via research this converts the existing deployment in place rather than creating a duplicate — don't run V1 and V2 live on the same domain simultaneously, they conflict.
- **Digital Experiences → All Sites now shows two entries.** `ESW_Vina_s_Assistant_...` (Framework: Lightning Web Runtime, tagged "Enhanced") is expected — Salesforce auto-generates this hidden site to host the Embedded Service widget's runtime whenever a Messaging deployment is created. Not a duplicate of the real site, nothing to configure on it.
- **Real finding: the actual "portfolio site" shows Framework = Aura**, not LWR. Root cause: the **"Customer Portal" template** picked on 2026-08-17 (build log's very first Digital Experiences setup entry) is an Aura-based template, not one of the LWR templates ("Build Your Own (LWR)" / "Microsite (LWR)"). This went unnoticed for the whole build so far because none of the earlier features (Flow, CMS carousel, header/footer nav) required LWR — Agentforce is the first thing that does.
- **Resolution — no site rebuild needed.** The LWR-only drag-and-drop "Embedded Messaging" component (the step 6 plan from earlier) isn't the only embed method. The classic Embedded Service JS snippet ("Get Code" from the deployment) is framework-agnostic — designed to embed on any website — and should work on this Aura site via Experience Builder → Settings → Advanced → Head Markup/Header Script. Confirmed legacy Chat (retired Feb 2026) is a different, unrelated product from Enhanced Chat/Messaging — no conflict there.
- **Revised step 6 (not yet executed):** Get Code from the V2 deployment → paste into the Aura site's head markup via Experience Builder Settings → Advanced → publish → test as guest/incognito. Exact field name not yet confirmed live in this org — will document once found, per this file's usual practice.

## 2026-08-24 — Correction: found a native embed component after all; Domain field was wrong

- **Correction to the "Aura needs manual snippet" assumption above:** turns out there IS a component in Experience Builder that lets the messaging widget be embedded directly (no manual snippet/head-markup needed) — worked in Builder preview. So the Aura-vs-LWR distinction may not have blocked the native component route after all; needs a clearer look at exactly which component this was next session.
- **Bug found: widget worked in Experience Builder preview but didn't appear after Publish.** Root cause (two-part fix, not yet confirmed working):
  1. **Wrong value in the Messaging Channel's "Domain" field.** Earlier guidance in this log (step 4, "Domain: your current My Domain URL") was **wrong** — confirmed via research the field should be the Experience Cloud **site's own public domain** (from Setup → Digital Experiences → Settings), not the org's My Domain login URL. These are different URLs and only one is what public visitors' browsers actually load the page from.
  2. **Missing CORS entry.** The site's domain also needs to be explicitly added at Setup → CORS → New (with `https://` prefix) — likely the actual reason the widget silently no-ops for real visitors even after the Domain field is fixed, since the browser blocks the cross-origin request without it.
- **Also still tangled up with the unresolved 2026-08-19 domain rename** — need to confirm which URL (`orgfarm-a6430190d2-dev-ed...` or `vinaegg-dev-ed...`) is actually the live published site's domain before setting the Domain field and CORS entry, otherwise this fix targets the wrong URL too.
- **Not yet confirmed fixed** — pending: update Domain field, add CORS entry, republish, retest on the actual live published URL (not Builder preview).

## 2026-08-24 — Domain field on an existing Messaging Channel is not editable, and channels can't be deleted either

- **No "Edit" option found on the "Vina's Assistant" channel to fix the Domain field.** Checked whether it was a missing Permission Set License (a Salesforce KB article — id 000380773 — describes exactly this symptom as caused by a missing "Messaging User" PSL) — ruled out, "Enhanced Chat User" PSL (this release's likely renamed equivalent) was already assigned to Vina Egg's user.
- **Tried to delete the channel to recreate it correctly instead — also not possible.** Confirmed via research this is a known, current Salesforce platform limitation: Messaging Channels cannot be deleted through the UI, and per Salesforce's own IdeaExchange (an open idea requesting this capability), not reliably even via Salesforce Support. Not specific to this org.
- **Workaround (standard practice for this Salesforce limitation): leave the broken channel in place (deactivated if possible) and create a second new channel with the correct Domain from the start.** Plan: deactivate "Vina's Assistant" if a Deactivate option exists (separate from Delete), then Setup → Messaging Settings → New Channel again, same config as the original (Enhanced Chat, Deployment = Web, Routing Type = Agentforce Service Agent → Vina's assistant, Fallback Queue = Vina Portfolio Fallback Queue), named distinctly (e.g. "Vina's Assistant Web") to avoid confusion with the dead first one. Domain field this time = the actual Experience Cloud site domain from Digital Experiences → Settings (not My Domain — see correction above).
- Step 3 (Omni-Channel Flow) should not need rework — it routes by Service Channel category ("Messaging") + Agent + Queue, not by a reference to the specific broken channel record.
- Step 5 (Embedded Service Deployment, Switch to V2) and the Experience Builder embed component will need to be redone pointing at the new channel once it exists.
- **Not yet executed** — pending the exact site domain value from Digital Experiences → Settings.

## 2026-08-24 — Actual root cause found: CSP frame-ancestors, not Domain/CORS

- Turned out the channel's Domain field was already correct all along (matched the live portfolio site URL) — the earlier "wrong domain" theory was a red herring. Added `https://vinaegg-dev-ed.develop.my.site.com` to CORS anyway (harmless, possibly still needed for other API calls), but the widget still didn't render on the live published site or in incognito after doing so.
- **Real root cause, found via browser console:** a Content Security Policy `frame-ancestors` violation. The chat widget loads inside an iframe pointing at the auto-generated `ESW_Vina_s_Assistant_...` site (the hidden site Salesforce provisions to host the widget — see the 2026-08-24 "two sites" entry above). That ESW site's CSP only allowlists Salesforce's own domains as valid frame-ancestors (`*.vinaegg-dev-ed.develop.my.salesforce.com`, `vinaegg-dev-ed--c.develop.vf.force.com`) — the actual portfolio site domain wasn't on that list, so the browser blocked the iframe outright. This is a completely different mechanism from CORS (CORS governs XHR/fetch; frame-ancestors governs iframe embedding) — worth remembering next time an "embed doesn't show up" bug appears, check both.
- **Fix (not yet confirmed working):** Setup → Sites (classic, under User Interface — not Digital Experiences) → open `ESW_Vina_s_Assistant_...` → set Clickjack Protection = "Allow framing by trusted sites only" → add the portfolio site's domain under "Trusted Domains for Inline Frames" → Save.
- **Lesson for next time:** "works in Builder/preview but not on the live published site" is now a 3-for-3 pattern in this project (Contact Me flow guest access, tile images guest access, now this CSP issue) — always suspect a security/access layer that only applies outside the authenticated admin context, and go straight to incognito + browser console rather than iterating on the feature config itself.

Sources: [SFDC Developers — Publish Agentforce to Experience Cloud](https://sfdcdevelopers.com/2025/09/17/how-to-publish-an-agentforce-agent-to-experience-cloud-step-by-step-guide/), [JetBI — Agentforce in Experience Cloud](https://jetbi.com/blog/integrating-agentforce-experience-cloud-portals), [SalesforceBolt — Deploy Agentforce Agent + Experience Cloud (Aug 2026)](https://www.salesforcebolt.com/2026/08/deploy-agentforce-agent-react-slack-experience-cloud-salesforce.html)
