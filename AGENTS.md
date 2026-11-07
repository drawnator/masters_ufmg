# AGENTS.md — start here

Master's project: **Semantics-Aware Gaussian Splatting for Egocentric Hyperlapse**
(Guilherme Silva Toledo, UFMG). This file is the single entry point for any AI agent working in
this repository. Read it, then read `docs/README.md`. You then have everything needed to work
without further explanation.

## What this repo is

- `/home/guilhermetoledo/projects/masters` holds **code + documentation only**, on a shared
  `/home` (identical on every machine). Datasets and results live on each machine's local disk —
  see `docs/environment.md`.
- Top-level folders are cloned reference repositories (one per cited/relevant paper) plus `docs/`.
  Inventory with roles, commits, and build notes: **`docs/repositories.md`**.
- Paper PDFs: `docs/papers/`. Thesis LaTeX template (UFMG `ppgccufmg`): `docs/thesis-template/`.

## Your standing rules in this repo

1. **Keep the documentation updated as you work** — this is a hard requirement:
   - A real choice → add a record in `docs/decisions/` (copy `_template.md`, add to the index).
   - Download/preprocess data → update `docs/datasets/datasets.md`.
   - Any run/experiment → copy `docs/results/_experiment-template.md`, fill it with **real
     commands and real output**, add a row to `docs/results/index.md`.
   - Change status/scope → update `docs/plan.md`.
2. **Evidence over assertion.** Back every claim with a command actually run and its output. A
   command exiting 0 is not proof — verify the artifact (file exists, non-trivial size, metric
   moved, GPU actually used). Label anything you did not run as **UNTESTED**.
3. When setting up/running a specific repo, follow *its* README/configs first; put the
   reproducible runbook (machine facts, every command, failures + real error text + fixes, results
   table, "known issues") in an `AGENTS.md` at **that repo's** root. This top-level file stays the
   project-wide index; per-repo runbooks stay in each repo.
4. Datasets/results go under `$DATA_ROOT` / `$RESULTS_ROOT` (see below), never inside this repo.
5. **Concurrent hosts (proc2 + proc6) share `/home`** ([decisions/0006](docs/decisions/0006-concurrent-multi-host-worktrees.md),
   [0007](docs/decisions/0007-git-fork-organization.md)). `/draft-hdd` is **not durable** (it failed
   before and lost uncommitted changes), so **code changes live on `/home` and are pushed to
   GitHub**, not only on `/draft-hdd`. **Fork a repo only when it needs code changes** (unmodified
   repos stay plain clones, pinned by commit in results logs). **Default: run from the shared
   baseline `./facebook_egocentric` read-only** (both hosts can, each with its own local venv) —
   create a per-host clone of the fork (`…/masters/run/<host>/<repo>`, branch `ufmg`) **only when a
   host must change code**, and never edit the baseline in place. Keep only rebuildable things on local
   `/draft-hdd` — venvs (`$ENV_ROOT`), uv cache, `TORCH_EXTENSIONS_DIR`, datasets, results.
   Trained scenes/results are **not** copied to `/home`; Guilherme backs them up to his own server
   via SFTP. Your writing (`docs/` + this file) is the `masters_ufmg` repo
   (`github.com/drawnator/masters_ufmg`). Per-repo setup runbooks live in `docs/runbooks/`.

## Where the code runs: a swappable run host (currently `proc6`)

Download, preprocessing, and reconstruction run on a **run host** that mounts this same `/home`
and stores data on its **local `/draft-hdd`**. The run host is **currently `proc6`** and may
change later to another same-`/home` machine. It is **not `proc3`** — proc3's `/draft-hdd` was
removed after a failure (2026-11-06), so nothing persists there.

**Python environments use `uv`, not conda** ([decisions/0005](docs/decisions/0005-uv-instead-of-conda.md)),
built on the host's **local `/draft-hdd`**, not in the repo:
`uv venv --python 3.10 $ENV_ROOT/ego_splats && source $ENV_ROOT/ego_splats/bin/activate && uv pip install -r <repo>/requirements.txt`
(torch from the index matching the host's CUDA; `ENV_ROOT`/caches defined in `docs/environment.md`).
Read **`docs/environment.md`** before installing
native/CUDA wheels or launching GPU jobs — **proc6 is already profiled there** (verified
2026-11-07: has avx2, 2× RTX 4090 but one is dead, driver CUDA 12.0, no conda/system CUDA, ~230 GiB
`/draft-hdd`). For any *other* host, capture its facts first. Key points:

- **CUDA toolchain without conda:** if a CUDA build is needed (e.g. gsplat JIT), get `nvcc` from
  pip wheels (`nvidia-cuda-nvcc-cu12` + runtime) in the venv and set `CUDA_HOME` to them — UNTESTED,
  path wiring differs from conda (details + the proc3 conda fallback in `docs/environment.md`).
- **CPU AVX2:** AVX2 wheels crash on a pre-AVX2 CPU (SIGILL, exit 132). proc6 *has* avx2, so the
  proc3 `projectaria-tools==1.6.0` pin is likely unneeded here — verify on other hosts.
- **`gsplat`** may JIT-compile on first use (~4.5 min — looks hung). Reference stack (proc3):
  python 3.10 + torch 2.11.0+cu128 + gsplat 1.5.3; on proc6 use a **cu121** torch build (driver is
  CUDA 12.0).
- **Shared-resource etiquette:** check `nvidia-smi` before the GPU (on proc6 pin the healthy card:
  `CUDA_DEVICE_ORDER=PCI_BUS_ID CUDA_VISIBLE_DEVICES=0`); confirm a disk budget + a free-space floor
  (proc6 has only ~230 GiB free vs a 124 GiB dataset); launch long jobs detached
  (`setsid nohup … > log 2>&1 < /dev/null &`) and report log path + PID; clean up what you start.

```bash
# run host data/results roots (host-agnostic; needs a local /draft-hdd)
export DATA_ROOT=/draft-hdd/guilhermetoledo/masters/data
export RESULTS_ROOT=/draft-hdd/guilhermetoledo/masters/results
```

## Current state (2026-11-07)

- 18 reference repos present (17 cloned + the pre-existing `facebook_egocentric`), no submodules
  (init per build). Core: `facebook_egocentric` (egocentric_splats, reconstruction code),
  `langsplat`, `egolifter`.
- Documentation hub built under `docs/` (overview, plan, repositories, datasets, decisions, results).
- Aria download manifest present (`docs/datasets/AriaScenes_download_urls.json`, 12 seqs, 133 GB).
- **Reconstruction is NOT currently available.** The proposal says Lv et al. was already run on the
  full Aria set, but that work used proc3's `/draft-hdd` (now removed), so plan to (re)do it on the
  run host: download → preprocess → reconstruct.
- **proc6 facts captured (2026-11-07):** AVX2 present, 1 usable RTX 4090 (driver 525/CUDA 12.0),
  no conda/nvcc, so use **uv** envs (decision 0005), and only 230 GiB free on `/draft-hdd`.
  Details are in `docs/environment.md`.
- **Not done yet:** setting up `facebook_egocentric` on the run host (it needed several fixes
  previously — capture them in that repo's `AGENTS.md`), downloading the dataset, reconstruction,
  semantic lifting, path planning. See `docs/plan.md` for the task list.

## Quick map

| Want to… | Go to |
|----------|-------|
| Understand the project | `docs/project-overview.md` |
| See the plan / what's next | `docs/plan.md` |
| Find the code for a paper | `docs/repositories.md` |
| Machine facts & traps | `docs/environment.md` |
| Know which datasets / download | `docs/datasets/datasets.md` |
| See why a choice was made | `docs/decisions/` |
| Log or read an experiment | `docs/results/` |
