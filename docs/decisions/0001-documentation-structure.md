# 0001. Documentation structure and repo-only-holds-code layout

- **Status:** Accepted
- **Date:** 2026-11-07
- **Deciders:** Guilherme (requested), Kiro (implemented)

## Context

The project integrates several external codebases and will produce many experiments on large
egocentric datasets. Guilherme asked for an organized way to (a) see the rationale behind
choices, (b) list the datasets in use, and (c) document algorithm results and the changes
applied to datasets. The working directory should contain only code and documentation; datasets
and results belong elsewhere.

## Decision

Create a top-level `docs/` hub with:

- `project-overview.md`, `plan.md`, `repositories.md`, `environment.md` for orientation;
- `decisions/` for append-only decision records (this log);
- `datasets/` for a dataset registry;
- `results/` for per-experiment logs plus an index.

Keep cloned reference repositories as siblings of `docs/` at the top level, matching the existing
`facebook_egocentric` clone. Datasets and experiment outputs stay under separate roots (see
[0003](0003-data-and-results-outside-repo.md)).

## Alternatives considered

- **Put docs inside each code repo.** Rejected: the project spans ~18 repos; cross-cutting plans
  and decisions would be scattered and lost on re-clone.
- **A single README.** Rejected: does not scale to a dataset registry plus a growing experiment
  log, and mixes stable plans with fast-changing results.
- **Group clones under an `external/` subfolder.** Not done now: the existing `facebook_egocentric`
  clone is already at top level and a worktree to a personal fork is planned on it; moving clones
  risks breaking that. Revisit if the top level gets crowded.

## Consequences

- One place to answer "why is it like this" (decisions), "what data" (datasets), "what happened"
  (results).
- Contributors must record decisions and log experiments for the structure to stay useful.
- The top level mixes ~18 code folders with `docs/`; acceptable given the existing layout.
