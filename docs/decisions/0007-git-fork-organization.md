# 0007. Git/fork organization: `masters_ufmg` for docs, per-project forks only when code changes

- **Status:** Accepted (policy); repo/fork creation pending GitHub URLs
- **Date:** 2026-11-07
- **Deciders:** Guilherme (asked + set the rule), Kiro (recommended)

## Context

Guilherme asked whether to track everything in one umbrella repo (`masters_ufmg`, with per-host run
dirs/branches like `facebook_egocentric/proc2`) or one repo per project. He set the rule: **only
publish a branch when the code is actually changed.** Most of the 18 cloned repos will run
unmodified, so they need no fork at all.

## Decision

Split by ownership, not by host:

1. **One documentation repo `masters_ufmg`** (Guilherme's GitHub) tracks only *his own* content —
   `AGENTS.md` + `docs/` at the masters root — with the code-repo folders gitignored. Pushed freely;
   this is the project's home repo.
2. **Code repos stay independent upstream clones.** **Fork a repo only when it needs code changes**
   (expected first: `facebook_egocentric`). Add the fork as a remote (`fork`), make changes on a
   branch (e.g. `ufmg`), publish that branch. Repos that run unmodified are **not** forked or
   published — reproducibility comes from pinning their upstream commit hash in the results log
   (the experiment template already records repo + commit).
3. **Default: run from the shared baseline read-only.** Running the code only reads source and
   writes to `$RESULTS_ROOT` on local disk, so both hosts can run from `./facebook_egocentric` at
   once (each with its own local venv). A clone/branch is created **only when a host must change
   code**; never edit the shared baseline in place. **Per-host working copies for a modified repo**
   go in a **per-host folder on durable `/home`**
   (e.g. `/home/.../masters/run/proc2/<repo>`, `.../run/proc6/<repo>`), both on branch `ufmg`,
   synced by `git push`/`pull`, and **pushed to GitHub often**. Separate folders mean the two hosts
   never share a working tree or `.git` (no clobber, no network-`.git` race). Build artifacts stay
   off `/home` — venv at `$ENV_ROOT`, `TORCH_EXTENSIONS_DIR` and `TMPDIR` on `/draft-hdd`. Prefer
   separate clones over git worktrees (worktrees share one `.git` and need a distinct branch per
   tree — awkward and race-prone across two machines). Host-specific differences belong in
   env/config (local), not code, so one branch usually suffices; add `proc2` / `proc6` branches
   only if the code must genuinely differ per host.

### Why code lives on `/home`, not `/draft-hdd` (updated 2026-11-07)

An earlier run kept its working folder on `/draft-hdd`; that disk failed and the uncommitted code
changes were lost. `/draft-hdd` is fast but not durable, while `/home` is the shared 22 TB network
mount that survived. So the copy holding edits lives on `/home` and is pushed to the fork
frequently; `/draft-hdd` holds only rebuildable things (venvs, caches, datasets) and results.
Trained scenes/results are **not** copied back to `/home` — Guilherme backs them up to his own
server via SFTP. This supersedes the earlier draft that placed the per-host clone on `/draft-hdd`.

## Alternatives considered

- **Monorepo vendoring all code** into `masters_ufmg` (subdirs like `facebook_egocentric/`).
  Rejected: loses each upstream's history, blocks pulling upstream fixes, and mixes 18 third-party
  codebases into one tree.
- **Submodules** in `masters_ufmg` pointing at the forks. Possible later for a single pinned
  snapshot, but adds ceremony; not needed now.
- **Git worktrees from the `/home` clone** (per [0006](0006-concurrent-multi-host-worktrees.md)).
  Fine on one machine; for two machines, separate local clones of the fork are cleaner.

## Consequences

- Few moving parts: one docs repo + a small number of forks (only modified repos).
- Refines [0006](0006-concurrent-multi-host-worktrees.md): its "per-host worktree" becomes
  "per-host local clone of the fork"; 0006's local-state split (venvs/caches/data/results on
  `/draft-hdd`) still holds.
- **Repos created 2026-11-07:** `github.com/drawnator/masters_ufmg` (docs) and
  `github.com/drawnator/egocentric_splats` (fork of `facebookresearch/egocentric_splats`). The
  masters root is now a local git repo for `masters_ufmg` (code dirs + `run/` gitignored). Per-host
  clone + venv + build commands live in [../runbooks/facebook_egocentric.md](../runbooks/facebook_egocentric.md).
- **Push needs credentials:** the setup host (proc3) has no GitHub auth (no SSH key, helper, token,
  or `gh`), so the first `masters_ufmg` push is left for Guilherme (command in the runbook). `ufmg`
  on the fork is pushed only when there are real code changes.
