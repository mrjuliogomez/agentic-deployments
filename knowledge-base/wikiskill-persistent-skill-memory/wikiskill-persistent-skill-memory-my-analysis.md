# Analysis: WikiSkill — persistent skill memory

Date read: 2026-08-28

## Problem it addresses

Agent skill-evolution systems collapse three distinct things into one: raw execution traces, accumulated knowledge, and executable skills. When insights get scattered across optimization histories instead of consolidated, they can't be systematically reused across iterations — skills improve during a run but don't compound afterward.

## Method

Three-layer separation:
1. Raw execution experience (traces) — direct agent interactions and failures.
2. Persistent wiki — a consolidated, addressable knowledge repository, continuously updated between skill updates.
3. Executable skills — reusable workflows built from the wiki, not straight from traces.

Skills and the wiki co-evolve: the wiki informs skill refinement, skill runs feed the wiki.

## Results

- Beats prior skill-evolution methods across benchmarks and model families.
- Skill-scale complementarity: a smaller model with evolved skills can outperform a larger model without them. Larger models benefit even more from skills — skills don't substitute for scale, they compound with it.
- Cross-model transfer: skills evolved by one model transfer to another, sometimes outperforming that model's own self-evolved skills.
- Ablation: removing the persistent-wiki layer (going back to raw traces or optimization history) loses most of the gain. The wiki is the load-bearing piece, not the skills themselves.

## Read on our own architecture

The `context-mcp-server` deployment's three-level structure (dated session files → slug master → `skills/*/SKILL.md`) is the same separation this paper validates independently: dated files are the traces, `master.md` is the wiki, skills are the executable layer. No structural change indicated — this is confirmation, not a new requirement.

One testable implication: cross-model transfer suggests a skill or master file written under one model should perform as well when read by a different one. If a skill underperforms after a model switch, this points at the skill's wording as the first thing to check, not the model.
