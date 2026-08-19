# Editorial Publishing

**Status** Live  ·  **Runtime** n8n Cloud  ·  **Trigger** Daily scheduled run

## What it does

Discovers Iberian-related news and social content from RSS feeds, deduplicates it semantically against recent output, researches each surviving item, rewrites it in one of five editorial voices and publishes it with imagery across four channels. The channels are X, two LinkedIn personal profiles, a LinkedIn company page and a Wix blog. Every run logs its output, its dedup vector and its publishing status to an audit sheet.

## Why it exists

Maintaining a multi-channel editorial presence by hand costs an hour or more per researched post, multiplied by four channels. The pipeline removes drafting, voice matching, image handling and cross-posting from the human loop entirely. The human role is monitoring the log and the live surfaces.

## Architecture

```
RSS feeds
   |
Extract (HTML to JSON)
   |
Embedding (per article)
   |
Fetch Logs ── Calculate Similarity ── duplicate? ──> drop
   |
Round Robin (channel rotation)
   |
Loop Over Items (batch size 1)
   |
Prep Research ──> Research (LLM, sourced brief)
   |
Prep Write (voice menu per channel) ──> Write (LLM)
   |
Prep Media ── image valid? ──no──> log MEDIA_FAIL, drop
   |yes
   ├──> Post to X ────────────────> Log
   ├──> Post to LinkedIn (x3) ────> Log
   └──> Post to Wix ──────────────> Log
```

## Harness

**The loop** is a linear per-item pipeline. A splitting node feeds items through one at a time so every downstream reference resolves to the current item.

**Context assembly** happens in two stages. A research stage builds a sourced brief for the article, then a write stage receives that brief plus a voice library. The model selects the voice from a menu gated by channel, so each channel only ever receives voices approved for it. Personas bias the choice but cannot force it, which keeps output varied without hard routing code.

**Tool bindings** are raw HTTP calls to each provider REST API, with the platform's native integration nodes retired from every branch. Recorded in decisions 0001, after three production failures on three different channels.

**Stop conditions.** A semantic dedup gate drops any item whose embedding scores at or above the similarity threshold against the trailing window of published output. An image validity gate drops items whose media fails, logging them under a failure status rather than publishing without imagery. Every publish node carries retry with backoff and continue-on-error wiring so one channel failing cannot cascade into the others.

**Human gates.** None inside the loop. The pipeline publishes autonomously and the human reviews the audit log and the live surfaces. A run reporting success is never trusted on its own, because continue-on-error can turn a total failure green. The live surface is the only proof of a post.

## Stack

| Layer | Tool | Why this one |
|---|---|---|
| Orchestration | n8n Cloud | Visual debugging of a long pipeline, no infrastructure to run |
| Scraping | Apify | Managed scraping without maintaining browser infrastructure |
| HTML extraction | r.jina.ai | Clean article text from arbitrary pages with one call |
| Structured extraction | Gemini | Cheap and fast for HTML to JSON conversion |
| Embeddings | OpenAI | Small embedding model, cheap enough to embed every candidate |
| Research and writing | Claude | Strongest long-form writing and instruction following of the options tried |
| Dedup and logging | Google Sheets | Zero setup, human-inspectable, adequate at current volume |
| Dedup successor | Supabase pgvector | Sheets degrades past a few hundred rows, migration scoped |
| Publishing | Provider REST APIs via HTTP | Native integration nodes mask provider errors, see decisions 0001 |

## Failure modes and handling

Native integration nodes returning generic errors while hiding the real provider response. Three separate production failures traced to this. Handled by migrating every integration to raw HTTP calls, which surface the actual provider error on the first failure.

Silent post truncation on LinkedIn. The publishing API treats body text as a lightweight markup format in which unescaped reserved characters cut the post at the first occurrence. Looked intermittent because most posts carry none. Handled by escaping the body on every LinkedIn publish node.

Duplicate publishing from a corrupted dedup store. A data lineage subtlety in the platform caused every logged row to carry the first item's dedup vector, so the similarity gate compared against poisoned data. Handled by pinning the accessor pattern on the logging nodes and verifying distinct vectors per row in the live log.

Oversized audit cells. The logging backend enforces a hard character limit per cell and the native node masked the rejection behind a generic error. Handled by redesigning the audit field to store a compact brief instead of the raw research payload, see decisions 0003.

False success runs. Continue-on-error wiring lets a failing node report green while every item routes to a dead branch. Handled procedurally, a run is verified against the live channel surfaces and the audit log, never against run status alone.

## Decisions

| # | Title |
|---|---|
| [0001](decisions/0001-http-over-native-integration-nodes.md) | HTTP requests over native integration nodes |
| [0002](decisions/0002-trailing-window-dedup-before-vector-store.md) | Trailing-window dedup before a vector store |
| [0003](decisions/0003-audit-field-redesign-over-log-migration.md) | Audit field redesign over log migration |

## Impact

All channel publishing was manual before this, each researched post costing source reading, context research, drafting, per-channel adaptation and posting by hand. Since going live the audit log has passed two thousand rows of publishing activity across the four channels, voice variety is confirmed on the live surfaces, and duplicate publishing has measured zero since the dedup store fix, verified against distinct per-row vectors in the log. As an estimate, at thirty to sixty minutes per researched multi-channel post by hand the pipeline returns several hours per week, assuming the manual alternative would have sustained the same cadence, which it would not have. Engagement and reach per voice and per channel are not yet measured, the performance review of the voice mix is pending.
