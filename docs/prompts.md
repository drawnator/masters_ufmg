# Ready-to-paste chat prompts

Copy one of these to start a new chat with minimal typing. They all rely on
[`../AGENTS.md`](../AGENTS.md) carrying the project context and standing rules.

> Current profiled run host is **proc6** (facts verified 2026-11-07 in
> [environment.md](environment.md)). **proc2** is a separate candidate host that has **not** been
> profiled yet — don't assume it has proc6's hardware.

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

## proc2 (run egocentric_splats; proc6 already running it)

```
Work on my masters project at /home/guilhermetoledo/projects/masters.
Read AGENTS.md and docs/ (start with docs/runbooks/facebook_egocentric.md). I'm on proc2 and want
to run egocentric_splats. It is ALREADY running on proc6 — do NOT touch proc6's clone or the
baseline clone.

1. proc2 isn't profiled yet: capture its facts (hostname, GPU/CUDA, CPU avx2, disk) per
   docs/environment.md and add a proc2 row. Verify /draft-hdd is a REAL mounted disk with free
   space: `findmnt /draft-hdd` must show its own device (on proc3 it is only an empty dir on / —
   never trust that). If proc2's /draft-hdd is not a real disk, STOP and tell me.
2. Make proc2's own working clone at /home/guilhermetoledo/projects/masters/run/proc2/facebook_egocentric
   from the fork github.com/drawnator/egocentric_splats, and check out branch `ufmg` to reuse the
   fixes already made on proc6 (if `ufmg` is not on the fork yet, fetch it from proc6's clone at
   ../run/proc6/facebook_egocentric).
3. Build the venv + CUDA extensions on proc2's LOCAL /draft-hdd (ENV_ROOT, UV_CACHE_DIR,
   TORCH_EXTENSIONS_DIR, TMPDIR as in the runbook); match torch to proc2's CUDA.
4. Running does NOT need a new branch. Only commit to `ufmg` and push to the fork if you actually
   change code; put host differences (CUDA, GPU index) in env/config, not code — make a proc2
   branch only if the code must truly differ.

Follow AGENTS.md: keep docs/ updated (log the run in docs/results/, record proc2 facts + any
fixes), back claims with real command output, run long jobs detached (report log + PID), check
nvidia-smi and a disk floor first. Tell me the next step and wait for my OK before any long/GPU job.
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
