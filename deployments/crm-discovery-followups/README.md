# CRM Discovery Follow-ups

**Status** Live  ·  **Runtime** n8n Cloud  ·  **Trigger** Daily scheduled run

## What it does

Works the discovery stage of the CRM board every morning. For each lead card that is due, it reconstructs the email history, classifies the lead's state, and acts accordingly. Silent leads get a follow-up draft written in the house register and placed in Gmail drafts for human send. Replied leads and orphaned threads are moved forward to the engaged stage for human triage. Every action is written back to the card as a ledger comment plus a next due date, so the board itself carries the full touch history. A seasonal calendar suppresses drafting through holiday windows and converts the last pre-break touch into a bridge greeting.

## Why it exists

Follow-up discipline is where pipelines die. Dozens of discovery leads each need the right touch at the right interval in the right language, and doing that by hand means either a daily hour of thread archaeology or leads silently going cold. The agent does the archaeology and the drafting, the human keeps the judgement and the send button.

## Architecture

```
Schedule (daily)
   |
Config ── Fetch doctrine, banned copy, vetted references
   |
Fetch due cards (CRM) ──> Filter (due or undated) ──> Limit (10/day)
   |
Card Summary (light LLM) ──> Get Email Thread ──> Thread Summary (strong LLM)
   |
Route by state
   ├── SILENT ──> Touch Counter (cadence + calendar)
   |              ├── normal ──> Write draft ──> Gmail draft ──> log + due
   |              ├── bridge ──> Bridge greeting ──> Gmail draft ──> log + due
   |              ├── suppress ──> drop
   |              └── exhausted ──> retire to closed stage
   ├── REPLIED ──> log ──> move to engaged stage
   └── ORPHAN ──> log ──> move to engaged stage
```

## Harness

The loop is a daily batch capped at ten cards, ordered by due date, so a backlog can never flood the mailbox or the model budget.

Context assembly is layered by cost. A light model summarises the card, the full email thread is fetched only after that, and a stronger model summarises the thread and classifies the lead state. Doctrine, banned copy and the vetted reference roster are fetched at run start from the private context repo, so copy rules change without touching the workflow.

Tool bindings are Gmail for threads and drafts, the CRM REST API over raw HTTP for cards, comments, due dates and stage moves, and two LLM tiers over HTTP.

Stop conditions. The daily cap, cadence exhaustion after the final touch which retires the card, and the seasonal calendar which suppresses drafting inside blackout windows while leaving reply detection live.

Human gates. Every outbound email is a draft, the human reviews and sends by hand. Replied leads are never auto-classified into won or lost, they are moved to the engaged stage for human reading. Recorded in decisions 0001 and 0002.

## Stack

| Layer | Tool | Why this one |
|---|---|---|
| Orchestration | n8n Cloud | Shared platform across the agent fleet |
| Email | Gmail | Thread history and the human-gated drafts folder |
| CRM | Asana via REST over HTTP | The board is the operating surface the human already lives in |
| Light summarisation | Claude Haiku class | Card summaries are cheap and constant, cost tiering matters |
| Thread reasoning and drafting | Claude Sonnet class | State classification and register-true drafting need the stronger tier |
| Copy doctrine | Private context repo over HTTP | Copy rules versioned in git, changed without redeploying |

## Failure modes and handling

Follow-ups landing inside holiday windows. A draft written into a dead week burns the touch for nothing. Handled by a calendar fork with blackout windows, bridge greetings before each break and due dates pushed to the window resume.

Replies misread as silence. A missed reply followed by a chaser is the single worst outcome for a warm lead. Handled by routing on thread state before any drafting, and by keeping reply detection live even when drafting is suppressed.

False positives the other way, silence misread as a reply. Auto-closing on a misread would kill warm leads invisibly. Handled by never auto-disposing, everything ambiguous goes to a human at the engaged stage.

Cadence state corruption. The touch count lives in the card's own ledger comments, if a comment fails to write the count drifts. Handled by writing the ledger comment and the due date in the same pass and treating the card as the single source of truth, recorded in decisions 0003.

Language misfires. A greeting in the wrong language reads as automation. Handled by deriving language from the lead's mail domain and thread language, with the human catching outliers at draft review.

Dirty input cards. A malformed card or an unverified address upstream would have the agent drafting to the wrong person. Handled upstream by the [record sanitiser](../crm-record-sanitiser/README.md), only verified, canonically structured cards reach the discovery stage.

## Decisions

| # | Title |
|---|---|
| [0001](decisions/0001-drafts-behind-a-human-gate.md) | Drafts behind a human gate |
| [0002](decisions/0002-replies-route-to-a-human-never-auto-disposition.md) | Replies route to a human, never auto-disposition |
| [0003](decisions/0003-card-ledger-as-cadence-memory.md) | Card ledger as cadence memory |

## Impact

Follow-ups were manual before this, each due lead costing minutes of thread re-reading before a line could be written, and leads slipped through in silence when the day filled up. The first production run processed the due batch and shipped eight ready-to-send drafts with ledger comments and forward due dates, and moved two replied leads to the engaged stage without any human research, with the daily cap holding the run to ten cards. As an estimate, at ten to fifteen minutes of manual archaeology and drafting per touch a full daily batch returns up to two hours, assuming the touches would otherwise have been made at all, which the baseline says they often were not. Reply-rate uplift, revenue attribution and the effect of bridge greetings on post-holiday response are not measured.
