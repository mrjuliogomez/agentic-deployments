# agentic-deployments

Agentic AI systems in production. Deployment write-ups, the skills that operate them, and a knowledge base of architecture analyses.

Everything here runs, or ran, on real work, publishing, CRM operations, email, document production. Each deployment folder carries the full write-up, what the agent does, why it exists, its architecture, its harness, its stack, its failure modes and its measured impact, plus decision records where a real fork existed. Agents marked Live run in production, with scheduled runs throttled or paused during low-activity periods so the token budget goes to builds in progress.

## Deployments

| Deployment | What it is | Status |
|---|---|---|
| [editorial-publishing](deployments/editorial-publishing/README.md) | RSS to four channels, researched, voiced and deduplicated editorial publishing | Live |
| [editorial-series](deployments/editorial-series/README.md) | Weekly long-form culture essay series to three channels from a curated queue | Live |
| [crm-discovery-followups](deployments/crm-discovery-followups/README.md) | Daily follow-up drafting and lead-state routing on the CRM discovery stage | Live |
| [crm-opportunity-logger](deployments/crm-opportunity-logger/README.md) | Nightly registration of engaged opportunities into the Partner network's portal | Live |
| [crm-record-sanitiser](deployments/crm-record-sanitiser/README.md) | Nightly card hygiene, registry research and dedup on the prospect-curation stage | Live |
| [crm-lead-screening](deployments/crm-lead-screening/README.md) | Multi-source opportunity discovery judged against a compiled ruleset and capability model | Live |
| [email-inbox-intelligence](deployments/email-inbox-intelligence/README.md) | Content-based inbox triage into meetings, tasks, project updates and filing | Live |
| [crm-kpi-dashboard](deployments/crm-kpi-dashboard/README.md) | Weekly top-ten owed tasks read across email and the CRM | Live |
| [context-mcp-server](deployments/context-mcp-server/README.md) | MCP server giving every agent session shared git-backed memory with guarded writes | Live |
| [partner-onboarding-form-filler](deployments/partner-onboarding-form-filler/README.md) | One email address in, complete client onboarding packet out | Live |

## Skills

| Skill | What it is |
|---|---|
| [calendar-management](skills/calendar-management/README.md) | Dates surfacing in conversation become verified calendar events and meeting briefings |
| [meeting-followups](skills/meeting-followups/README.md) | The per-card manual follow-up cycle for engaged leads, one card at a time |
| [campaign-building](skills/campaign-building/README.md) | Research-to-copy pipeline for outbound campaigns behind two blocking mastery gates |
| [crm-lead-card-create](skills/crm-lead-card-create/README.md) | Any input into a complete, deduplicated, registrable lead card |
| [crm-lead-card-standard](skills/crm-lead-card-standard/README.md) | The canonical card contract the whole agent fleet reads and writes |
| [browser-operation](skills/browser-operation/README.md) | The mechanics of driving a real browser on a real person's machine |
| [document-identity](skills/document-identity/README.md) | The locked brand canon every client-facing document build obeys |
| [example-curation](skills/example-curation/README.md) | Filing real good and bad communication cases so practice compounds |
| [linkedin-messaging](skills/linkedin-messaging/README.md) | Copy, register and send protocol for messages written as the principal |

## Knowledge base

A working library on applied AI, kept for builders. Anything that adds to how we build earns a folder, production architectures, conference talks, research papers, pilots, businesses built around AI, new paradigms worth stealing from. One folder per topic, each carrying the source material and my own analysis, because the point is to learn from each other and the analysis is where the learning shows. Current topics, an AI pocket analyst tool built inside a major investment firm, with transcript, slides and analysis, and a collection of sources on building agents that run for hours without losing the plot.

## Licence

Written content under CC BY 4.0, see [LICENSE.md](LICENSE.md). Third-party material remains its owners' and is quoted with attribution.
