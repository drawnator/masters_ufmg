# Decision Log

Each non-trivial choice in this project is recorded here as a short, numbered decision record
(ADR style), so the reasoning behind the project's state is visible and reviewable.

## How to use

1. Copy [`_template.md`](_template.md) to `NNNN-short-title.md` (next number, zero-padded).
2. Fill in context, the decision, alternatives considered, and consequences.
3. Add a row to the index below.
4. Decisions are append-only. To change one, add a new record that supersedes it and mark the
   old one `Superseded by NNNN` in the Status field.

## Index

| # | Title | Status | Date |
|---|-------|--------|------|
| [0001](0001-documentation-structure.md) | Documentation structure and repo-only-holds-code layout | Accepted | 2026-11-07 |
| [0002](0002-reference-repository-selection.md) | Reference repository selection and EgoSplat gap | Accepted | 2026-11-07 |
| [0003](0003-data-and-results-outside-repo.md) | Datasets and results stored outside the code repository | Accepted | 2026-11-07 |
| [0004](0004-extra-papers-and-two-machine-setup.md) | Extra papers, two-machine layout, and the AGENTS.md bootstrap | Accepted | 2026-11-07 |
| [0005](0005-uv-instead-of-conda.md) | Use uv (not conda) for Python environments on the run host | Accepted | 2026-11-07 |
| [0006](0006-concurrent-multi-host-worktrees.md) | Concurrent runs on proc2 + proc6: per-host isolated trees and local state | Accepted; mechanics refined by [0007] | 2026-11-07 |
| [0007](0007-git-fork-organization.md) | Git/fork organization: masters_ufmg for docs, per-project forks only when code changes | Accepted (policy) | 2026-11-07 |
