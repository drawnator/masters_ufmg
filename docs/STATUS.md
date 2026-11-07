# STATUS — current state / handoff

Living snapshot so a new agent (or host) can continue with a one-line prompt. **Keep this current**:
when you start/finish a run or hit a blocker, update the rows below. Prefer real values (log path,
PID, commit) over prose; mark anything unverified as UNKNOWN.

**Updated:** 2026-11-07 (from proc3 setup session)

## Git
- Docs repo `masters_ufmg` = `github.com/drawnator/masters_ufmg`, pushed (`main`).
- Fork `github.com/drawnator/egocentric_splats` = plain mirror of upstream; **no `ufmg` branch yet**
  (no code fixes made).
- Baseline `./facebook_egocentric` = pristine upstream `516dfe2`, clean. Default is to **run from
  it**; clone the fork only when a code fix is needed.

## Active runs
| Host | What | State | Log path / PID | Notes |
|------|------|-------|----------------|-------|
| proc6 | Aria dataset download → `$DATA_ROOT/aria_scenes` | in progress | UNKNOWN — fill from the proc6 session | dataset is per-host on local `/draft-hdd` |
| proc2 | not started | — | — | run from shared baseline; see [prompts.md](prompts.md) |
| proc3 | setup/docs only | n/a | — | its `/draft-hdd` is **not a real mount** (empty dir on `/`) — don't run here |

## Code fixes
- None yet (2026-11-07). proc6 has not changed the code. First fix → create `run/<host>` clone of
  the fork on branch `ufmg` and push (see [runbooks/facebook_egocentric.md](runbooks/facebook_egocentric.md)).

## Next steps
1. proc6: finish dataset download, then preprocess → train (log in [results/](results/)).
2. proc2: profile the host, build a local uv venv, run from the baseline (see [prompts.md](prompts.md)).
3. Record each run in [results/](results/) and update this file.

## Watch-outs
- Dataset is downloaded per host (local `/draft-hdd`); no shared copy unless set up.
- Aria manifest URLs expire ~2026-11-08; re-export if a download 403s.
- Verify `findmnt /draft-hdd` shows a real device before writing on any host.
