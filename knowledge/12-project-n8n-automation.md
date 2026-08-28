# Project — X (Twitter) Account Daily Digest Automation

Date: June 2026 | Geneva, Switzerland
Link: https://www.vinaegg.dev/projects

Vina built an end-to-end n8n automation that monitors a list of X (Twitter) accounts and delivers an AI-summarized daily digest to a Gmail inbox, running on an automated schedule with zero manual intervention.

She designed a multi-step pipeline: parsed and split batch account handles via JavaScript, chained two API calls to resolve user IDs and fetch tweets, then used Claude (Anthropic Chat Model) to summarize the content into a digestible format.

She aggregated the multi-account summaries into a single consolidated digest and automated email delivery via Gmail integration.
