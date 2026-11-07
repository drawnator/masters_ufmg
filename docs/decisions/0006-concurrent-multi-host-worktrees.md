# 0006. Concurrent runs on proc2 + proc6: per-host worktrees and local state

- **Status:** Accepted (policy); working-copy mechanics refined by [0007](0007-git-fork-organization.md)
- **Date:** 2026-11-07
- **Deciders:** Guilherme (requested), Kiro (implemented)

> **Refinement ([0007](0007-git-fork-organization.md)):** two corrections to the mechanics below.
> (1) A repo is forked **only when it needs code changes**. (2) The per-host working copy is a
> **separate local clone of the fork in a per-host folder on durable `/home`** (not a worktree on
> `/draft-hdd`): `/draft-hdd` failed before and lost uncommitted changes, so code lives on `/home`
> and is pushed to GitHub. The venvs/caches/datasets split onto local `/draft-hdd` in this record
> still applies.

## Context

Guilherme runs the project on **proc2 and proc6 at the same time**, and both mount the same shared
`/home` where the code and `docs/` live. Editing a repo's tracked files in place, or building a
`.venv` inside a repo on `/home`, would make the two hosts clobber each other:

- compiled CUDA extensions and venvs differ per host (different GPU/CUDA);
- the torch JIT cache (`~/.cache/torch_extensions`, on `/home`) is built per-GPU;
- a code change one host needs would rewrite the tree the other host is actively running.

He asked that changes go into a worktree "or something."

## Decision

**Shared `/home`** holds only baselines: the code repos (not edited in place), `docs/`, PDFs,
manifests. **Each host's local `/draft-hdd`** holds everything host-specific:

- Python venvs under `ENV_ROOT=/draft-hdd/guilhermetoledo/masters/envs`, `UV_CACHE_DIR`, and
  `TORCH_EXTENSIONS_DIR=/draft-hdd/guilhermetoledo/masters/torch_ext` (see [../environment.md](../environment.md)).
- Datasets (`$DATA_ROOT`) and results (`$RESULTS_ROOT`) — already local.
- ~~A **git worktree per host**, on that host's local `/draft-hdd`, each on its own branch~~
  **(superseded — see the refinement note above and [0007](0007-git-fork-organization.md): a
  per-host clone on durable `/home`, not a worktree on `/draft-hdd`).** Common fixes live on a
  shared branch both merge from; host-specific tweaks stay on the host branch. Hosts exchange
  commits through Guilherme's GitHub fork (a remote), not by racing on the shared `.git`.

`docs/` stays shared and may be updated from either host; keep edits small and append-style to
avoid conflicts.

## Alternatives considered

- **Separate full clone per host on local disk.** Most isolated (no shared `.git`), but duplicates
  history and drifts easily. Worktrees + a fork remote give the same isolation of working trees
  with one shared history.
- **Worktrees on `/home`.** Keeps everything on the network mount, but build artifacts would sit on
  slow shared storage and two hosts would contend on the same `.git` locks. Local `/draft-hdd`
  worktrees are faster and better isolated.
- **Edit in place, coordinate by hand.** Rejected: guaranteed to clobber a live run eventually.

## Consequences

- No agent may edit a code repo's tracked files directly on `/home`; it must use the host's
  worktree/branch. This rule is in [../../AGENTS.md](../../AGENTS.md).
- Venvs/caches never go in the shared repo; they live on local `/draft-hdd` per host.
- **Pending:** Guilherme's fork URL (at least for `facebook_egocentric`) and confirmation of the
  worktree path before the worktrees/remotes are created. Until then, the baseline clone is
  untouched.
- Record the exact worktree/branch/remote commands in `facebook_egocentric/AGENTS.md` once set up.
