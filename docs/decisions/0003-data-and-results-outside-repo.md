# 0003. Datasets and results stored outside the code repository

- **Status:** Accepted
- **Date:** 2026-11-07
- **Deciders:** Guilherme (requested), Kiro (implemented)

## Context

The datasets (Aria ~114 GB, ADT, Ego-Exo4D) and experiment outputs (Gaussian scenes, rendered
videos, metric dumps) are large and change constantly. The working directory should stay a clean
code + documentation tree.

## Decision

Store raw/preprocessed datasets under `DATA_ROOT` and all experiment outputs under
`RESULTS_ROOT`, both **outside** this repository. The repository references them via environment
variables or symlinks, never hard-coded absolute paths. Candidate roots and the open questions
are in [../environment.md](../environment.md). What exists and where is tracked in
[../datasets/datasets.md](../datasets/datasets.md); what each run produced is tracked in
[../results/](../results/).

## Alternatives considered

- **Keep data/results in-repo (gitignored).** Rejected: hundreds of GB risk accidental commits,
  bloat backups, and break the "code + docs only" rule.
- **Git LFS / DVC for data.** Deferred: useful later for versioning *derived* artifacts and
  metrics, but overkill for raw public datasets that are re-downloadable. Revisit if experiment
  provenance needs stronger tracking.

## Consequences

- The repo stays small and clonable; worktrees/forks carry no data.
- `DATA_ROOT` / `RESULTS_ROOT` must be set in the environment before running anything, and their
  real paths confirmed (currently proposed, not created — see [../environment.md](../environment.md)).
- Experiment logs must record absolute output paths so results remain traceable to data.
