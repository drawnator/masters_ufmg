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

## proc2 (candidate host — not yet profiled)

```
Work on my masters project at /home/guilhermetoledo/projects/masters.
Read AGENTS.md and the docs/ it points to; continue from docs/plan.md.
Run host is proc2 (same shared /home, local /draft-hdd); use uv (not conda); data/results under
/draft-hdd/guilhermetoledo/masters. proc2 is NOT yet profiled — do NOT assume proc6's hardware.
First capture proc2's real facts (hostname, GPU/CUDA, CPU/AVX2, /draft-hdd free) with the command
in docs/environment.md, add a proc2 row there, and record anything that differs from proc6.
Follow AGENTS.md: keep docs/ updated and back claims with real command output. Launch long jobs
detached and report log path + PID. Tell me the next step and wait for my OK before any long/GPU job.
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
