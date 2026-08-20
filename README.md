# agentic-deployments

Agentic AI systems in production. Deployment write-ups, the skills that operate them, and a working knowledge base on applied AI.

Everything here runs, or ran, on real work, publishing, CRM operations, email, document production. Each deployment folder carries the full write-up, what the agent does, why it exists, its architecture, its harness, its stack, its failure modes and its measured impact, plus decision records where a real fork existed. Agents marked Live run in production, with scheduled runs throttled or paused during low-activity periods so the token budget goes to builds in progress.

## What it returns

The numbers below are aggregated from the per-agent Impact sections, each one measured on the live system. A standing inbox of four hundred to six hundred emails now holds single digits. The publishing log passed two thousand rows across four channels with duplicate publishing at zero since the dedup fix. Opportunity registration went from ten to twenty minutes of hand-typing per deal to zero human time, with over one hundred and thirty registrations in the ledger. Daily follow-ups, nightly record hygiene and weekly reporting run without a human in the loop, and every outbound email still crosses a human's send button.

## How the fleet fits together

```
                        context-mcp-server
              shared git-backed memory with guarded writes,
                 read and written by every agent below
                                |
     ┌──────────────────┬──────┴───────────┬─────────────────────┐
     |                  |                  |                     |
 EDITORIAL          CRM BOARD         OPPORTUNITIES            INBOX
     |                  |                  |                     |
 editorial-         crm-record-       crm-lead-screening    email-inbox-
 publishing         sanitiser         portals, feeds and    intelligence
 editorial-         nightly clean     sweeps into scored    daily triage,
 series                 |             cards                 four lanes
     |                  v                  |                     |
     v              crm-discovery-         └───── shared ────────┘
 X, LinkedIn,       followups                pool and ruleset
 Wix                daily drafts
                        |
                        v
                    crm-opportunity-logger
                    nightly registration
                        |
                    partner-onboarding-form-filler
                    on demand, intake packet

          crm-kpi-dashboard reads email and board weekly
```

## Built on

The fleet runs on a deliberately small toolchain. [Claude](https://claude.com) Cowork hosts the reasoning agents as scheduled tasks and skills. [n8n Cloud](https://n8n.io) orchestrates the deterministic pipelines. A custom [Model Context Protocol](https://modelcontextprotocol.io) server on [Railway](https://railway.com) gives every agent shared git-backed memory over [GitHub](https://github.com). The CRM is [Asana](https://asana.com) on the free tier, the record pool is [Airtable](https://airtable.com), logging and dedup ride on [Google Sheets](https://workspace.google.com), and mail and calendar are [Gmail](https://workspace.google.com) and Google Calendar. Publishing goes through the [X](https://developer.x.com), [LinkedIn](https://developer.linkedin.com) and [Wix](https://dev.wix.com) APIs over raw HTTP, scraping through [Apify](https://apify.com) and [r.jina.ai](https://jina.ai), headless form work through [Browserless](https://browserless.io), embeddings through [OpenAI](https://openai.com), image generation through [Vertex AI](https://cloud.google.com/vertex-ai), and the dedup successor is [Supabase](https://supabase.com) pgvector. Reproducing the pattern needs those accounts and nothing exotic, the expensive part is the operating discipline documented in these pages, and the write-ups carry that in full.

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
| [public-portfolio-publishing](skills/public-portfolio-publishing/README.md) | The publishing law this portfolio itself obeys, from private build to public page |

## Knowledge base

A working library on applied AI. Anything that adds to how we build earns a folder, production architectures, conference talks, research papers, pilots, businesses built around AI, new paradigms worth stealing from, each with its source material and an analysis.

| Topic | What it holds |
|---|---|
| [building-pat-the-ai-pocket-analyst-tool](knowledge-base/building-pat-the-ai-pocket-analyst-tool/) | An AI pocket analyst built inside a major investment firm, transcript, slides and analysis |
| [build-agents-that-run-for-hours-without-losing-the-plot](knowledge-base/build-agents-that-run-for-hours-without-losing-the-plot/) | Source collection on long-running agent design, analysis in progress |

## Disclosure

This repo is written, maintained and curated by an agent I built for that one job. It checks for newly launched agents in production, reads my private working notes, drafts every page, and commits only what I have signed off.

The crazy part?

The style and voice are mine. I trained it on my own writing and voice so the content reads the way I would've written it myself.

If you find something useful here, steal it and share it with people you care about. Thanks for reading.

## Questions and maintainer

Maintained by [Julio Gómez](https://github.com/mrjuliogomez), who runs these agents on his own advisory business. Questions, corrections and conversations are welcome through [issues](../../issues), and [CONTRIBUTING.md](CONTRIBUTING.md) explains what this repo accepts and why.

## Licence

Written content under CC BY 4.0, see [LICENSE.md](LICENSE.md). Third-party material remains its owners' and is quoted with attribution.
