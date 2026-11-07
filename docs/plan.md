# Implementation Plan

Derived from the proposal's objectives and methodology. Status values: `done`, `in-progress`,
`todo`, `blocked`. Update the status column as work proceeds and link experiments to
[results/index.md](results/index.md).

## Objectives → workstreams

| # | Objective (from proposal) | Workstream | Primary code |
|---|---------------------------|-----------|--------------|
| — | Egocentric 3DGS reconstruction of Aria scenes | **WS0 Reconstruction** | `facebook_egocentric` |
| (i) | Lift open-vocabulary semantics onto reconstructions; per-Gaussian relevance queried in 3D | **WS1 Semantic lifting** | `langsplat`, `egolifter`, `semantic-gaussians`, (EgoSplat: re-impl) |
| (ii) | Formulate semantic virtual camera path planning (smooth trajectory maximizing relevance at target speed-up) | **WS2 Path planning** | new code (this project) |
| (iii) | Render the planned trajectory from the Gaussians; fall back to source views on low confidence | **WS3 Rendering** | `facebook_egocentric`, `gaussian-splatting` |
| (iv) | Compare against 2D and geometric families on smoothness, semantics, motion fluidity | **WS4 Baselines + evaluation** | VeRLab repos, Kopf/Joshi (re-impl), `perceptual-similarity-lpips` |

## Status

| Workstream | Status | Notes |
|-----------|--------|-------|
| WS0 Reconstruction | `todo` | Proposal claims it was done on the full Aria set, but those outputs used proc3's `/draft-hdd` (removed 2026-11-06) and are not available. (Re)do on the run host: download → preprocess → reconstruct. The repo needed several fixes to run previously — capture them in its `AGENTS.md`. |
| WS1 Semantic lifting | `todo` | Decide LangSplat vs EgoLifter vs Semantic Gaussians as the base; EgoSplat has no code (see [decisions/0002](decisions/0002-reference-repository-selection.md)). |
| WS2 Path planning | `todo` | New solver. Cost terms listed in [project-overview.md](project-overview.md). |
| WS3 Rendering | `todo` | Reuse egocentric_splats rendering; add confidence-based fallback. |
| WS4 Baselines + evaluation | `in-progress` | Replication under way (sparse-sampling FF, First-person Hyperlapse). |

## Near-term tasks

1. ~~**Capture the run host's facts**~~ — **done 2026-11-07** ([environment.md](environment.md)):
   proc6 has AVX2, one usable RTX 4090 (driver 525 / CUDA 12.0), no conda/nvcc (uv instead,
   [decisions/0005](decisions/0005-uv-instead-of-conda.md)), and **only 230 GiB free on `/draft-hdd`**.
   The manifest URLs **expire 2026-11-08 ~16:13 UTC**.
2. **Download the Aria dataset** into `$DATA_ROOT/aria_scenes` on the run host (12 seqs, 133 GB;
   detached, with a disk-budget floor) — see [datasets/datasets.md](datasets/datasets.md).
3. **Git set up (2026-11-07):** `masters_ufmg` repo initialized at the masters root (push pending
   Guilherme's credentials — see [runbooks/facebook_egocentric.md](runbooks/facebook_egocentric.md));
   fork `github.com/drawnator/egocentric_splats` ready. **Next:** on each run host, clone the fork
   into `…/masters/run/<host>/`, build with uv on `/draft-hdd`, get it running, and push `ufmg`
   only when code changes. Other repos stay plain clones, pinned by commit in results logs.
4. **Get `egocentric_splats` running** in the host worktree and record every fix needed (the
   previous attempt required many). Capture as a runbook in that repo (`AGENTS.md` at its root).
5. **Preprocess + reconstruct** the Aria sequences; log each run in [results/](results/) and record
   output paths in [datasets/datasets.md](datasets/datasets.md).
6. **Pick the semantic-lifting base** (WS1) and record the choice as a decision.
7. **Stand up baselines** (WS4): get at least one VeRLab semantic fast-forward repo running on
   an Aria sequence.

## Schedule (from proposal, 2027)

- Jan–Feb: baselines on Aria + first path-planning results.
- Feb–May: implement path planning + splat rendering; initial experiments.
- May–Jun: experiments, ablations, user study; write + submit article.
- Jul–Dec: write + defend dissertation.

Target venues: SIBGRAPI 2027 (primary), ICCV 2027 (stretch), CVPR 2028 / ICLR 2028 (final).
