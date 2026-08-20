# CRM Lead Screening

**Status** Live  ·  **Runtime** Cowork and n8n Cloud  ·  **Trigger** Daily and on-demand runs

**Relationship.** Shares its record pool and compiled ruleset with [email-inbox-intelligence](../email-inbox-intelligence/README.md), which feeds the same pipeline from the email channel.

## What it does

Discovers opportunity listings across public portals, sixteen families of structured feeds and scheduled web sweeps, kills obvious mismatches at the headline, deduplicates survivors into a shared record pool, resolves each survivor's full body exactly once, judges it against a compiled ruleset and a capability model, sweeps every candidate against a forbidden-counterparty register, and writes a scored card into the CRM for human action. Every run closes with a dated report whose kill tallies are dimensioned by reason and source.

## Why it exists

Manual screening does not scale past a handful of sources, and worse, it is inconsistent, the same listing gets a different read on a tired day. The pipeline makes the screening criteria explicit, versioned and auditable, argues a case for every survivor the way a headhunter would, and leaves the human only the decisions that need judgement.

## Architecture

```
Portal APIs        Structured feeds       Web sweeps       Email channel
(discovery)        (16 source families)   (scheduled)      (sibling agent)
     \                    |                   |                /
      └────────── headline kills (no record written) ────────┘
                          |
                Shared record pool (Airtable)
                dedup keys, status, audit trail
                          |
                Resolve (full body, fetched once,
                written back to the record)
                          |
                Judge (compiled ruleset + capability model,
                one judge, runs once per record)
                          |
                Forbidden-counterparty sweep (every card, logged)
                          |
                Scored card in CRM ──> human review and action
                          |
                Dated run report, kill tallies by reason x source
```

## Harness

**The loop** is feeders in, one judge out. Discovery agents only retrieve and kill at the headline, they never evaluate substance. Everything that survives is written to the pool at ingest status, and a single judging pass runs once per record before the human ever sees it. Recorded in decisions 0003.

**Context assembly.** The judge reads the compiled ruleset, a generated artefact with a version and a hash, never the scattered source rules, and matches substance against a capability model built from an interview-corrected inventory of what the principal can actually defend. Where live law is missing from the artefact the agent stops and reports, the run waits for compiled law. Recorded in decisions 0001 and 0002.

**Tool bindings.** Portal and feed retrieval over raw HTTP, the record pool in Airtable, cards in Asana, judging by Claude agents running as scheduled tasks.

**Stop conditions.** Headline kills write no record, only a tally. A resolution that fails twice on independent surfaces parks the record with the blocker named. A daily card cap and an auto-expiry window keep the queue honest. Negative results follow a surface-authority doctrine, a claim about a listing is only trusted from the surface that owns it.

**Human gates.** The pipeline cards, it never applies, contacts or submits. Every card carries exactly one action comment for the human. Rulings made mid-run that are not yet compiled into the artefact are flagged as unshipped and block the next deployment.

## Stack

| Layer | Tool | Why this one |
|---|---|---|
| Discovery and feeds | Raw HTTP per source family | Sixteen feed dialects, one thin adapter each |
| Record pool | [Airtable](https://airtable.com) | Dedup keys, status machine and human-inspectable audit in one place |
| Ruleset | Compiled markdown artefact in git | Versioned, hashed, generated from source rules, never hand-edited |
| Capability model | Versioned markdown in git | Interview-corrected ground truth the judge matches against |
| Judgement | Claude agents on scheduled tasks | Body-level reasoning that rules cannot express |
| Cards | Asana | The surface where the human already works the pipeline |
| Web sweeps | Chrome-driven browsing | Sources with no feed still get covered |

## Failure modes and handling

Silent zero-yield runs. A run whose tally sums perfectly can still be a dead agent. Handled with an external yield floor, a run below it is investigated regardless of how clean its report reads.

Judgement drift between runs. The same listing read twice must get the same verdict. Handled by compiling all law into one versioned artefact and forbidding execution on uncompiled chat rulings, plus a golden set of trap cases for regression.

Text gates passing what judgement must kill. Evidence priced on the wrong side of a role survives every keyword gate. Handled by the capability model's direction axis and an honest-expansion probe at judging time.

Boolean exclusion lists acting as silent gates. A NOT clause in a retrieval query is a denylist running on unclassified input. Handled by keeping kill logic in code where it is logged, and retrieval queries permissive.

Re-doing work after a crash. A run that writes no rows forces the next run to repeat everything. Handled by writing pool rows at ingest before any expensive step, the pool is the checkpoint.

Tooling defects misread as market facts. A broken search widget looks identical to zero matching listings. Handled by positive controls, a surface is only declared empty after a broad query proves the surface itself answers.

## Decisions

| # | Title |
|---|---|
| [0001](decisions/0001-capability-model-over-keyword-matching.md) | Capability model over keyword matching |
| [0002](decisions/0002-compiled-ruleset-as-the-single-law-artifact.md) | Compiled ruleset as the single law artefact |
| [0003](decisions/0003-feeders-never-judge-one-shared-judge.md) | Feeders never judge, one shared judge |

## Impact

Screening was manual browsing across a handful of portals before this, unrecorded, inconsistent between sittings and blind to most of the structured-feed universe. Thirteen production runs have built a shared pool of roughly one hundred and fifty records with full audit trails and delivered nineteen scored cards for human review, every negative disposition carrying a reason code and an evidence trail, and every run report dimensioning its kill tallies by reason and source. As an estimate, manual coverage of the same source universe would cost upwards of ten hours a week, assuming the manual alternative would attempt full coverage, which it never did. Outcome conversion of carded opportunities is not measured yet, the funnel has an entrance and no exit, and an outcomes table is the next build item, stated here in the open.
