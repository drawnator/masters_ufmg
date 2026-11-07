# 0002. Reference repository selection and the EgoSplat gap

- **Status:** Accepted
- **Date:** 2026-11-07
- **Deciders:** Guilherme (requested), Kiro (implemented)

## Context

The proposal cites 19 references. We wanted a local clone of each that has public code, to study
and reuse. Not every cited work ships code, and some repository names differ from the paper
titles/years.

## Decision

Clone the 12 papers with public code plus 2 dataset-tooling repos; document the 4 without code.
Full mapping and clone status: [../repositories.md](../repositories.md).

Resolved naming mismatches worth recording:

- Ref [17] "Sparse-sampling framework, TPAMI **2021**" → repo `verlab/SemanticFastForward_TPAMI_**2020**`.
- Ref [13] → `verlab/StraightToThePoint_CVPR_2020` (not `.../StraightToThePoint`).
- Ref [14] → `verlab/TextDrivenVideoAcceleration_TPAMI_2022` (underscore before year).
- Ref [15] "EPIC@ECCV 2016" → `verlab/SemanticFastForward_ECCVW_2016`.
- Ref [1]+[10] (Project Aria, Aria Digital Twin) are both served by one repo: `projectaria_tools`.
- Ref [2] Ego-Exo4D is accessed through `facebookresearch/Ego4d`.

Clones were made **without submodules** to save disk and time; submodules are initialized per
repo at build time (list in [../repositories.md](../repositories.md)).

## Alternatives considered

- **Clone with `--recurse-submodules`.** Rejected for now: pulls large CUDA/C++ submodules (e.g.
  SIBR viewers) and GLM copies that are only needed when actually building a given repo.
- **Skip dataset-tooling repos.** Rejected: `projectaria_tools` is a direct dependency of the
  core reconstruction repo and of ADT access; keeping it local is useful. It is clearly labelled
  as tooling, and the datasets stay outside the repo.
- **Shallow (`--depth 1`) clones.** Rejected: full history keeps the option open to fork/worktree
  a repo later (as planned for `facebook_egocentric`).

## Consequences

- **EgoSplat [8] has no public code** (checked 2026-11-07). It is central to objective (i)
  (per-Gaussian open-vocabulary relevance for egocentric scenes). Plan: build semantic lifting on
  LangSplat + EgoLifter (both cloned) and re-implement EgoSplat's multi-view instance aggregation
  and transient filtering from the paper. Re-check for a code release periodically.
- NVS-side baselines Kopf [7] and Joshi [5] (Microsoft, no code) must be reproduced from their
  papers or compared qualitatively.
- If any missing code is released, add the clone and supersede this record.
