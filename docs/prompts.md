# Ready-to-paste chat prompts

Copy one of these to start a new chat with minimal typing. They all rely on
[`../AGENTS.md`](../AGENTS.md) carrying the project context and standing rules.

> Current profiled run host is **proc6** (facts verified 2026-11-07 in
> [environment.md](environment.md)). **proc2** is a separate candidate host that has **not** been
> profiled yet — don't assume it has proc6's hardware.

## Handoff (short) — to continue an in-progress run

Smallest prompt; the agent self-orients from `AGENTS.md` + [`STATUS.md`](STATUS.md). Replace
`<HOST>` and append any extra steering lines you want below it.

```
Continue my masters project at /home/guilhermetoledo/projects/masters.
Read AGENTS.md and docs/STATUS.md, then continue. I'm on <HOST>.
Keep docs/STATUS.md and results updated. Ask before any long/GPU job.
```

One-liner (when you trust the agent to self-orient):

```
Continue my masters project: read /home/guilhermetoledo/projects/masters/AGENTS.md + docs/STATUS.md; I'm on <HOST>. What's next?
```

---

The prompts below are the longer **first-time setup on a host** versions.

## proc6 (current, profiled run host)

```
Work on my masters project at /home/guilhermetoledo/projects/masters.
Read AGENTS.md and the docs/ it points to; continue from docs/plan.md.
We're on proc6: use uv (not conda), data/results under /draft-hdd/guilhermetoledo/masters.
proc6 is already profiled in docs/environment.md (has avx2; 2x RTX 4090 but b1:00.0 is dead,
so pin CUDA_DEVICE_ORDER=PCI_BUS_ID CUDA_VISIBLE_DEVICES=0; driver is CUDA 12.0 -> use cu121
torch; only ~230 GiB free on /draft-hdd vs a 124 GiB dataset, so set a disk floor).
Follow AGENTS.md: keep docs/ updated (decisions, datasets, results) and back claims with real
command output. Launch long jobs detached and report log path + PID. Tell me the next step and
wait for my OK before any long/GPU job.
```

## proc2 (run egocentric_splats; proc6 is using the project too)

```
Work on my masters project at /home/guilhermetoledo/projects/masters.
Read AGENTS.md and docs/runbooks/facebook_egocentric.md. I'm on proc2; proc6 is also using this
project (currently downloading the dataset) — don't disturb it.

No code fixes have been needed yet, so RUN FROM THE SHARED baseline ./facebook_egocentric as-is —
do NOT create a clone, branch, or fork unless you actually need to change code. If a fix becomes
necessary, make a per-host clone of the fork under run/proc2 on branch `ufmg` (per the runbook);
never edit ./facebook_egocentric in place.

Setup on proc2:
- proc2 isn't profiled: capture its facts (GPU/CUDA, CPU avx2, disk) per docs/environment.md and
  add a proc2 row. Verify /draft-hdd is a REAL mount: `findmnt /draft-hdd` must show its own device
  (on proc3 it's just an empty dir on / — don't trust it). If it's not real, STOP and tell me.
- Build a venv on proc2's LOCAL /draft-hdd (ENV_ROOT/UV_CACHE_DIR/TORCH_EXTENSIONS_DIR/TMPDIR as in
  the runbook); match torch to proc2's CUDA; install from ./facebook_egocentric/requirements.txt.
- The dataset lives on each host's local /draft-hdd; if it's not on proc2 yet, download it there
  (manifest in docs/datasets) — confirm a disk budget/floor first. proc6 has its own copy.

Follow AGENTS.md: keep docs updated (record proc2 facts, log runs in docs/results/), back claims
with real command output, run long jobs detached (report log + PID), check nvidia-smi + disk first.
Tell me the next step and wait for my OK before any long/GPU job.
```

## Generic run host (any other machine)

```
Work on my masters project at /home/guilhermetoledo/projects/masters.
Read AGENTS.md and the docs/ it points to; continue from docs/plan.md.
Run host is <HOSTNAME> (same shared /home, local /draft-hdd); use uv (not conda); data/results
under /draft-hdd/guilhermetoledo/masters. First capture this host's real facts (GPU/CUDA, CPU/AVX2,
disk) per docs/environment.md and fill a row in the table — don't assume proc6/proc3 hardware.
Follow AGENTS.md: keep docs/ updated and back claims with real command output. Tell me the next
step and wait for my OK before any long/GPU job.
```
