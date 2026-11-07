# Project Documentation — Semantics-Aware Gaussian Splatting for Egocentric Hyperlapse

This folder is the documentation and planning hub for the master's project. It holds
**only writing**: plans, decisions, dataset registry, and result logs. Code lives in the
sibling repository folders; datasets and experiment outputs live outside this repository
(see [environment.md](environment.md)).

> New here? Read [`../AGENTS.md`](../AGENTS.md) first — it is the project-wide bootstrap and the
> standing rules (including keeping this documentation updated).

## How this is organized

| Path | Purpose |
|------|---------|
| [project-overview.md](project-overview.md) | What the project is: goal, pipeline, references. Distilled from the thesis proposal. |
| [plan.md](plan.md) | Implementation plan: workstreams, milestones, current status. |
| [repositories.md](repositories.md) | Every paper → its code repository, clone status, role in the project, and build notes. |
| [environment.md](environment.md) | Run-host model (currently proc6) + proc3-observed gotchas to verify; data root and results root locations. |
| [decisions/](decisions/) | Decision log (ADR style): every non-trivial choice, with context and rationale. |
| [datasets/](datasets/) | Dataset registry + the Aria download manifest and command. |
| [results/](results/) | Experiment logs: one file per run/experiment, plus an index table. |
| [papers/](papers/) | PDFs of all cited + extra papers (VeRLab lineage under `papers/verlab/`). |
| [thesis-template/](thesis-template/) | UFMG `ppgccufmg` LaTeX template + PDF/A scripts; rendered proposal at `thesis-template/exemplo/exemplo.pdf`. |
| [prompts.md](prompts.md) | Ready-to-paste prompts to start a new chat on the run host with minimal typing. |
| [runbooks/](runbooks/) | Per-repo setup runbooks (clone + uv venv + build + run), e.g. `facebook_egocentric.md`. |

## Conventions

- **Where things live.** This directory (`/home/guilhermetoledo/projects/masters`) contains
  code (the cloned repositories) and this `docs/` folder. Raw datasets and all experiment
  outputs are stored under separate roots defined in [environment.md](environment.md), never
  committed here.
- **Record decisions as you make them.** When you choose an approach, add an entry under
  [decisions/](decisions/) using the template. This is the record of *why*, not just *what*.
- **Log every experiment.** Copy [results/_experiment-template.md](results/_experiment-template.md),
  fill it in with real commands and real output, and add a row to
  [results/index.md](results/index.md).
- **Evidence over assertion.** Back claims with commands actually run and their real output.
  Label anything not actually executed as **UNTESTED**.

Last updated: 2026-11-07
