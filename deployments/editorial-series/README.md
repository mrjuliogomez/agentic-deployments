# Editorial Series

**Status** Live  ·  **Runtime** n8n Cloud  ·  **Trigger** Weekly scheduled run

## What it does

Publishes a proprietary long-form culture essay series across three channels, a Wix blog, a LinkedIn personal profile and X. Topics come from a curated, pre-written queue rather than feeds. Each run picks the next topic, checks it against recent output, researches it, writes three channel-fitted versions in a single bespoke essay voice, generates an abstract image with a deterministic text overlay, and publishes to all three channels from one trigger.

## Why it exists

The series is original thought-leadership content, not news rewriting, so it needed its own pipeline with a locked voice and a curated queue. Writing and distributing one researched essay across three channels by hand costs a working morning. The pipeline reduces the human role to curating the topic queue and reading the live posts.

## Architecture

```
Schedule (weekly)
   |
Fetch Topic (curated queue)
   |
Embedding ── Fetch Posts ── Calculate Similarity ── duplicate? ──> stop
   |
Prep Research ──> Research (LLM)
   |
Write Wix ──> Write LinkedIn ──> Write X     (chained in series)
   |
Prep Essay (convergence, three channel-tagged items)
   |
Prep Draw ──> Generate Image ──> Overlay (deterministic text)
   |
Attach and Fan (binary onto all three items)
   |
channel gate ──┬──> Wix    upload ──> post ──> log
               ├──> LinkedIn register ──> upload ──> post ──> log
               └──> X      upload ──> post ──> log
```

## Harness

The pipeline is a single series line with no parallel branches. The platform cannot resolve sibling branches by node name across a parallel convergence, so the three write stages are chained one after another and a single convergence node reads all three by name from the one walked branch. That constraint shaped the whole topology, recorded in decisions 0001.

Context assembly. The research stage produces a sourced brief from the topic seed. Each write stage receives the brief plus the channel it must serve, hardcoded per stage, and applies the same locked essay voice with channel-specific caps and furniture.

Tool bindings are raw HTTP calls to the provider REST APIs, inheriting the standing rule from the [editorial-publishing](../editorial-publishing/README.md) deployment. Two-step media uploads carry a rebind node between the calls because an HTTP node emits only the API response and drops both the incoming binary and any custom fields.

Stop conditions. A semantic dedup gate stops the run if the topic scores too close to recent output. Channel gates route each tagged item to exactly one publishing branch.

Human gates. None inside the loop. The human curates the topic queue upstream and verifies the live surfaces downstream. Run status is never trusted, a run can report success while every item routed to a dead gate, so verification is against the published posts.

## Stack

| Layer | Tool | Why this one |
|---|---|---|
| Orchestration | n8n Cloud | Same platform as the sibling pipelines, shared debugging craft |
| Topic queue and logging | Google Sheets | Human-editable queue, zero setup |
| Embeddings | OpenAI | Cheap dedup vectors per topic |
| Research | Claude | Deep sourced briefs for essay-grade writing |
| Writing | Claude | Holds a locked voice across three channel formats |
| Image generation | Vertex AI Imagen | Abstract art on demand, text kept out of the prompt |
| Text overlay | In-pipeline image edit | Image models garble text, deterministic overlay never does |
| Publishing | Provider REST APIs via HTTP | Native integration nodes mask provider errors |

## Failure modes and handling

Parallel convergence breaking node references. The platform cannot read a sibling branch by name across a parallel gap. Handled structurally, everything runs in series, recorded in decisions 0001.

HTTP nodes dropping the binary mid-upload. Two-step uploads lose the image at the first call because an HTTP node outputs only the API response. Handled with rebind nodes that re-read the source binary and pass lineage through.

Signed upload URLs rejecting mismatched media types. The first upload call mints a URL for a declared media type and the second call must match it exactly. Handled by pinning type and filename on both calls.

Green runs that publish nothing. Continue-on-error let a broken data lineage fail every item quietly while the run reported success. Handled by verifying the live surfaces after every run and treating run status as noise.

Image models garbling text. Any wordmark or title rendered by the image model comes out mangled. Handled by keeping all text out of the generation prompt and stamping it deterministically in a separate overlay step.

Hero images cropped by the destination container. The blog cover box crops any aspect ratio it is not built for. Handled by generating natively in the container's ratio instead of chasing crops downstream.

## Decisions

| # | Title |
|---|---|
| [0001](decisions/0001-series-chain-over-parallel-convergence.md) | Series chain over parallel convergence |
| [0002](decisions/0002-per-channel-write-calls-over-shared-write.md) | Per-channel write calls over a shared write |
| [0003](decisions/0003-bespoke-voice-over-shared-voice-library.md) | Bespoke voice over the shared voice library |

## Impact

One essay across three channels by hand took a working morning, research, drafting, three channel adaptations, image work and posting. All three channels now publish from a single trigger run, visually confirmed on the live surfaces, and the media path is hardened, both two-step uploads survive the binary drop and the hero renders uncropped. As an estimate, three to four hours return per essay week, assuming the manual alternative would have held the weekly cadence, which competing priorities make unlikely. Engagement per channel, follower growth attributable to the series and the relative performance of essay topics are not measured.
