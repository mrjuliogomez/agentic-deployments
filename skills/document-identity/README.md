# Document Identity

**Type** Cowork skill, brand canon  ·  **Trigger** Every client-facing document build, before the document-type skill is read

## What it does

Holds the single source of truth for the visual and verbal identity of every client-facing document, proposals, analyses, intake packs, reports, anything carrying the logo. Locked colour tokens, one typeface, a fixed cover block, a fixed footer, heading and table styles, the rules for reference-logo strips, and the verbal lexicon. Document-type skills govern structure and content only, on identity elements this canon wins over every one of them.

## Flow

```
client-facing document request
   |
read the identity canon first
   |
then the document-type skill
   |
build from shared components
   |
identity checklist ── cover order, footer lines,
   |                  token colours, one entity
   |                  mention, strip composition
deviation found? ──yes──> regenerate whole
   |no
deliver
```

## How it runs

The precedence rule is the whole design. Identity is never re-derived, re-negotiated or improved inside a document session, if an element seems wrong it is flagged to the human, never forked. Every document session reads this canon first, then the document-type skill, then builds from shared components. The finished document is converted and checked against an identity checklist, cover order, footer lines, one entity mention, token colours, strip composition, and any deviation is regenerated whole. Reference strips regenerate from registry files that track which logos are cleared for use, the repo stores recipes, never loose binaries.

## Guardrails

The firm is described as the technology advisor, never a consultancy. Service providers are named only inside the one section that introduces the completed selection, everywhere else they are the service provider, never in a heading, never on a cover, never as a logo. Only the legal registered address appears on documents. Quantified claims come only from vetted reference registries, and client logo strips carry only signed, live clients with consent tracked.

## Lessons carried

Identity questions used to be re-answered in every document session, slightly differently each time, and the drift compounded across deliverables. Locking the canon in one file with a precedence rule converted an unwinnable consistency battle into a checklist, and the never-fork rule means the canon only improves through deliberate amendment, never through session-local taste.
