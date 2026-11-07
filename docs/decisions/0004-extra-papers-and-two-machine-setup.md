# 0004. Extra papers, two-machine layout, and the AGENTS.md bootstrap

- **Status:** Accepted
- **Date:** 2026-11-07
- **Deciders:** Guilherme (requested), Kiro (implemented)

## Context

Guilherme provided: (a) a zip of all paper PDFs (cited + extra), (b) the UFMG thesis LaTeX
template used to render the proposal, and (c) the Aria download manifest. He also clarified the
deployment model: code + docs live on a shared `/home`; the code runs on a **run host** that
mounts the same `/home` but stores data on its local `/draft-hdd`. The run host is **not fixed** —
currently `proc6`, may change to another same-`/home` machine later (an earlier attempt on `proc3`
is abandoned; its `/draft-hdd` was removed after a failure on 2026-11-06). He wants to start new
chats on the run host with minimal typing and have the agent keep the docs current.

## Decision

1. **Papers organized** under `docs/papers/` (VeRLab lineage under `docs/papers/verlab/`); thesis
   template unzipped to `docs/thesis-template/`; Aria manifest placed at
   `docs/datasets/AriaScenes_download_urls.json`. Source zips deleted after extraction.
2. **Extra papers with public code cloned**: FaVoR (`utiasSTARS/FaVoR`), Utonia
   (`Pointcept/Utonia`), VIGS-SLAM (`cvg/VIGS-SLAM`), and JVCI'18
   (`verlab/SemanticFastForward_JVCI_2018`). Extras without usable code documented in
   [../repositories.md](../repositories.md): KISS-GS (project-page repo only), and the VeRLab
   gaze-driven / listening-and-watching / personalizing papers (no dedicated repos).
3. **Run-host data layout** recorded in [../environment.md](../environment.md): on the run host
   `DATA_ROOT` / `RESULTS_ROOT` are under `/draft-hdd/guilhermetoledo/masters/{data,results}`
   (host-agnostic, needs a local `/draft-hdd`); the setup/docs host uses `/draft-nvme`. The
   proc3-observed build traps (pre-AVX2 CPU SIGILL, old base `nvcc`, gsplat JIT) are written into
   the docs **as a checklist to verify on the actual run host**, not as facts about it, since the
   hardware may differ. Reconstruction is treated as to-be-(re)done, not already available.
4. **Single bootstrap**: `AGENTS.md` at the repo root is the one file a fresh agent reads; it
   points to `docs/` and states the standing rule to keep decisions/datasets/results updated.

## Alternatives considered

- **Re-implement the `proc3` profile from scratch each session.** Rejected: those facts cost
  significant debugging; writing them once into `docs/environment.md` is the point of the request.
- **Clone KISS-GS anyway.** Rejected: its repo carries no source (gh-pages project page only).
- **A Kiro steering file instead of `AGENTS.md`.** Not done: `AGENTS.md` + an explicit paste
  prompt is portable across machines/clients and does not touch workspace steering config. Can be
  revisited if auto-loading is wanted.

## Consequences

- Starting on the run host needs only a short prompt pointing at `AGENTS.md` (kept with this project).
- 18 reference repos now sit at the top level; the inventory in `docs/repositories.md` is the map.
- Docs must be kept current by every session — enforced by the standing rule in `AGENTS.md`.
