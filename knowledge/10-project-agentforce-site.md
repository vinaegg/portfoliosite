# Project: Salesforce Experience Cloud Portfolio Site with Agentforce Agent

Date: May to August 2026 | Geneva, Switzerland
Link: https://vinaegg-dev-ed.develop.my.site.com/portfolio/s/ (this site)

Vina designed and built this public Experience Cloud site (Home, About Me, Certifications & Superbadges, Projects, Contact Me) to show end-to-end Salesforce configuration skills.
- The certifications carousel is managed with Salesforce CMS Workspaces.
- The Contact Me form is a Screen Flow that creates a Lead and sends automated emails to the visitor and to Vina, using Email Templates and an Org-Wide Email Address.

She also built Ask Vina, the Agentforce agent you are talking to right now:
- It answers questions using only a Salesforce Knowledge article that holds her CV content, retrieved through a custom Flow action. The standard knowledge action needs Data Cloud, which is not available in a free Developer Edition org, so she built this workaround.
- It is embedded in the site with Enhanced Messaging.
- It has guardrails: it never guesses beyond the knowledge base, points visitors to LinkedIn or the Contact Me form when it can't find an answer, and ignores attempts to override its instructions.
