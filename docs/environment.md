# Environment: Machines, Data Root, Results Root

This repository (`/home/guilhermetoledo/projects/masters`) holds **code and documentation
only**, on a shared `/home` network mount visible from every machine. Datasets and experiment
outputs are stored on the **run host's local disk**, never committed here.

## Model: one shared home, a swappable run host

| | Setup/docs host | **Run host** |
|---|---|---|
| Role | Where the repos + docs were set up (2026-11-07) | Where download, preprocessing, and reconstruction actually run |
| Which machine | the host used on 2026-11-07 (has `/draft-nvme`) | **currently `proc6`**; may change later to another machine |
| `/home` | shared (same on every host) | shared (same on every host) |
| Local disk for data/results | `/draft-nvme` | `/draft-hdd` on the run host |

The run host is **not fixed**. Any machine that mounts the same `/home` and has a local
`/draft-hdd` can be the run host; the **profiled** one is `proc6` (facts below, verified
2026-11-07). Because `/home` is shared, the code and this `docs/` tree are already present on the
run host at the same path — only the data/results roots are local to it.

> **`proc2` is a candidate, not yet profiled.** It may be used as a run host too, but its hardware
> has **not** been measured — do not copy proc6's facts onto it. Capture proc2's own facts (command
> under "Run-host setup gotchas" below) and add a `proc2` row before relying on them.

> **Not proc3, and verify `/draft-hdd` on any host.** An earlier attempt used `proc3`; its
> `/draft-hdd` failed (2026-11-06) and as of 2026-11-07 `/draft-hdd` there is **just an empty
> directory on `/`** (`findmnt /draft-hdd` → not a mountpoint), so writing to it fills the root disk
> and nothing persists. Before using `/draft-hdd` on *any* run host, confirm it is a real mount:
> `findmnt /draft-hdd` must show its own device. If it doesn't, stop — do not write there.

## Concurrent runs on proc2 + proc6 (shared `/home`)

Both hosts run at the same time off the same `/home`. **`/draft-hdd` is not durable** — it failed
before and took uncommitted code changes with it (proc3, 2026-11-06). So anything you can't afford
to lose lives on `/home` (shared, survived) and is pushed to GitHub; only rebuildable things live
on `/draft-hdd`.

| Lives on **durable `/home`** (+ pushed to GitHub) | Lives on **local `/draft-hdd`** (rebuildable, may be lost) |
|---|---|
| **Your code changes** — a per-host working clone per modified repo (see below) | Python venvs (`$ENV_ROOT`), uv cache, `TORCH_EXTENSIONS_DIR` |
| `docs/` + `AGENTS.md` (the `masters_ufmg` repo) | Datasets (`$DATA_ROOT`) — re-downloadable |
| Baseline reference clones, paper PDFs, manifests | Results (`$RESULTS_ROOT`), incl. trained scenes — **not** copied to `/home`; Guilherme backs them up to his own server via SFTP later |

**Default: run unmodified code from the shared baseline clone** (`./facebook_egocentric`) —
read-only, so both hosts can run from it at once, each with its own local venv. **Fork/clone a repo
only when a host must change code** ([decisions/0007](decisions/0007-git-fork-organization.md)); the
commit it ran at is recorded in the results log. For a repo you *do* modify:

- Fork it on GitHub, add the fork as a remote (`fork`), make changes on a branch (e.g. `ufmg`), and
  **`git push` often** — GitHub is the durable source of truth, not any local disk.
- Give **each host its own clone of the fork in a per-host folder on `/home`** (e.g.
  `/home/guilhermetoledo/projects/masters/run/proc2/facebook_egocentric` and `.../run/proc6/...`),
  both on branch `ufmg`, synced by `git push`/`pull`. Separate folders → the hosts never share a
  working tree or `.git` (no clobber, no network-`.git` race); on `/home` → a `/draft-hdd` failure
  can't lose your edits.
- Build artifacts stay off `/home`: point the venv at `$ENV_ROOT`, set `TORCH_EXTENSIONS_DIR` and
  `TMPDIR` to `/draft-hdd`. The source tree on `/home` is only read at runtime (fine over the mount).
- Host-specific differences belong in env/config (local), not code, so one branch usually serves
  both; add `proc2` / `proc6` branches only if the code genuinely must differ per host.

**Repos (created 2026-11-07):** docs = `github.com/drawnator/masters_ufmg`; fork =
`github.com/drawnator/egocentric_splats` (of `facebookresearch/egocentric_splats`). Per-host
working clones go under `/home/.../masters/run/<host>/`. The exact clone + uv-venv + build commands
are in [runbooks/facebook_egocentric.md](runbooks/facebook_egocentric.md). Push `ufmg` to the fork
**only when there are real code changes**.

## Data root / results root

Reference these via env vars (or symlinks), never hard-coded absolute paths. The paths are
host-agnostic as long as the run host has a local `/draft-hdd`.

```bash
# on the run host (currently proc6)
export DATA_ROOT=/draft-hdd/guilhermetoledo/masters/data
export RESULTS_ROOT=/draft-hdd/guilhermetoledo/masters/results
mkdir -p "$DATA_ROOT" "$RESULTS_ROOT"
```

| Host | `DATA_ROOT` | `RESULTS_ROOT` |
|------|-------------|----------------|
| run host (proc6, or next one) | `/draft-hdd/guilhermetoledo/masters/data` | `/draft-hdd/guilhermetoledo/masters/results` |
| setup/docs host | `/draft-nvme/guilhermetoledo/masters/data` | `/draft-nvme/guilhermetoledo/masters/results` |

## Pipeline on the run host (order)

Python environments use **`uv`, not conda** ([decisions/0005](decisions/0005-uv-instead-of-conda.md)).
**proc2 and proc6 run concurrently off the same shared `/home`**, so everything host-specific must
live on the host's **local `/draft-hdd`**, never inside the shared repo
([decisions/0006](decisions/0006-concurrent-multi-host-worktrees.md)):

```bash
# per-host roots on LOCAL /draft-hdd (so proc2 and proc6 never collide)
export ENV_ROOT=/draft-hdd/guilhermetoledo/masters/envs
export UV_CACHE_DIR=/draft-hdd/guilhermetoledo/.uv-cache
export TORCH_EXTENSIONS_DIR=/draft-hdd/guilhermetoledo/masters/torch_ext   # gsplat JIT is per-GPU
mkdir -p "$ENV_ROOT" "$TORCH_EXTENSIONS_DIR"

# venv on local disk, NOT in the repo on /home:
uv venv --python 3.10 "$ENV_ROOT/ego_splats" && source "$ENV_ROOT/ego_splats/bin/activate"
uv pip install torch torchvision --index-url https://download.pytorch.org/whl/cu121  # match host driver
uv pip install -r /home/guilhermetoledo/projects/masters/facebook_egocentric/requirements.txt
```

Do **not** run `uv venv .venv` inside a repo on `/home`: the two hosts would overwrite each other's
venv and their compiled CUDA extensions differ. Code edits also go through a per-host worktree, not
the shared tree — see [decisions/0006](decisions/0006-concurrent-multi-host-worktrees.md).

Then:

1. **Download** the Aria Scenes dataset (12 sequences, 133.2 GB) into `$DATA_ROOT/aria_scenes`
   using the manifest at [datasets/AriaScenes_download_urls.json](datasets/AriaScenes_download_urls.json).
   Command in [datasets/datasets.md](datasets/datasets.md).
2. **Preprocess** the Aria VRS recordings (MPS: trajectories, points, calibration) with
   `facebook_egocentric` + `projectaria_tools`.
3. **Reconstruct** the Gaussian scenes with `facebook_egocentric` (egocentric_splats).

### Reconstruction status — not currently available

The proposal (§1.1, §1.3, §1.5) states the Lv et al. method was already run on the whole Aria
dataset "for this project." Those outputs are **not currently available**: the earlier work used
`proc3`'s `/draft-hdd`, which was removed after a failure (2026-11-06). Treat reconstruction as
**to be (re)done on the run host**. If a copy of earlier outputs turns up on shared storage,
record its path in [datasets/datasets.md](datasets/datasets.md) and reuse it instead of recomputing.

---

## Run-host setup gotchas — verify on the actual host

The following traps were **observed on `proc3`** (a previous run host) and cost significant
debugging. `proc6` (or whatever host is used) may have **different hardware**, so treat these as a
checklist to verify on first login, not as established facts about the current host. They are kept
because, if the hardware is similar, they recur and are hard to diagnose.

**First, capture the actual run host's facts** and fill the table below:

```bash
hostname; nproc; free -g | awk '/Mem/{print $2" GB RAM"}'
grep -o 'avx2' /proc/cpuinfo | head -1 || echo "no avx2"
nvidia-smi --query-gpu=name,memory.total,driver_version --format=csv
nvcc --version 2>/dev/null | tail -1; df -h /draft-hdd /home /tmp
```

| Item | Value (fill in for the run host) |
|------|----------------------------------|
| Hostname | proc6 (verified 2026-11-07) |
| GPU(s), VRAM, driver, CUDA | 2× RTX 4090 24 GiB, driver 525.147.05, CUDA 12.0 (driver max). **GPU `b1:00.0` is dead** (see below); only `31:00.0` usable |
| CPU, AVX2? | Intel Xeon Silver 4314 @ 2.40GHz — **has `avx2` and `fma`** (verified 2026-11-07). The proc3 `projectaria-tools==1.6.0` pin should be unnecessary here (UNTESTED until installed) |
| cores / RAM / swap | 64 threads / 125 GiB RAM / 7 GiB swap (verified 2026-11-07; load avg ~5, 12 users logged in) |
| `/draft-hdd` free | **230 GiB free of 3.6 TiB (94 % used)** on `/dev/sdc1` (2026-11-07). Shared disk — see the budget note below |
| OS / compiler | Ubuntu 20.04.6 LTS, gcc 9.4.0, system `python3` 3.8.10 |
| conda / nvcc location + version | **No conda, no system CUDA toolkit** (`conda` not found, no `/usr/local/cuda*`, no `nvcc` on PATH). Python envs via **uv 0.12.17** (`~/.local/bin/uv`) — see [decisions/0005](decisions/0005-uv-instead-of-conda.md) |

Raw evidence (2026-11-07, proc6):
```
$ hostname; nproc; grep -o -m1 avx2 /proc/cpuinfo; grep -m1 'model name' /proc/cpuinfo
proc6
64
avx2
model name	: Intel(R) Xeon(R) Silver 4314 CPU @ 2.40GHz
$ df -h /draft-hdd /home /tmp
/dev/sdc1               3.6T  3.2T  230G  94% /draft-hdd
mfs#150.164.212.2:9421   22T   19T  3.0T  87% /home
/dev/sda2               492G   57G  410G  13% /
$ nvidia-smi -i 0000:31:00.0 --query-gpu=name,memory.total,memory.used,utilization.gpu,driver_version --format=csv
NVIDIA GeForce RTX 4090, 24564 MiB, 6 MiB, 0 %, 525.147.05
$ which conda nvcc; ls -d /usr/local/cuda*      # -> nothing / "No such file or directory"
$ uv --version; gcc --version | head -1
uv 0.12.17 (x86_64-unknown-linux-gnu)
gcc (Ubuntu 9.4.0-1ubuntu1~20.04.2) 9.4.0
```

**Disk budget on proc6.** The full Aria set is 133.2 GB (≈124 GiB). Downloading it all leaves
≈106 GiB, barely above the 100 GiB floor, with no room for preprocessed frames or reconstruction
outputs. Download a subset first, or get more space, before pulling the full set.

**CUDA without conda (plan, UNTESTED).** Driver 525.147.05 supports CUDA 12.0 natively, and
CUDA 12.x minor-version compatibility (driver ≥ 525.60.13) should let cu12x torch wheels run,
because they ship SASS for `sm_89`. If `gsplat` needs a JIT build, get `nvcc` from pip wheels
inside the uv venv (`nvidia-cuda-nvcc-cu12` + `nvidia-cuda-runtime-cu12` headers) and point
`CUDA_HOME` at them. This is an alternative to the conda toolchain fix below.

### proc6: second GPU has fallen off the bus (observed 2026-11-07)
Plain `nvidia-smi` aborts with `Unable to determine the device handle for GPU0000:B1:00.0: Unknown
Error`. `lspci` shows `b1:00.0 ... (rev ff)` and `dmesg` has
`NVRM: Xid (PCI:0000:b1:00): 79 ... GPU has fallen off the bus.` (at ~46 h uptime). This is a
hardware/PCIe fault; only a reboot (admin) or physical fix recovers it. Workaround — query the
healthy GPU explicitly:
```bash
nvidia-smi -i 0000:31:00.0
nvidia-smi -i 0000:31:00.0 --query-gpu=name,memory.total,memory.used,utilization.gpu --format=csv
```
CUDA jobs: UNTESTED whether CUDA init tolerates the dead device; if it hangs/errors, pin with
`CUDA_DEVICE_ORDER=PCI_BUS_ID CUDA_VISIBLE_DEVICES=0`. Note the driver (525, CUDA 12.0) is older
than the cu128 stack used on proc3 — torch cu12x wheels may need a CUDA ≤12.0-compatible build or
rely on minor-version compatibility; verify.

### Gotchas to check (observed on proc3 — re-verify)
- **Pre-AVX2 CPU → SIGILL (exit 132, often no traceback).** proc3's Xeon E5-2620 lacked
  avx2/fma. If the run host also lacks avx2, AVX2-built wheels crash. Vet a wheel before installing:
  ```bash
  pip download --no-deps --only-binary :all: <pkg>   # unzip, then scan the .so:
  objdump -d --no-show-raw-insn <lib>.so | grep -cE '\b(vperm2i128|vextracti128|vinserti128|vpbroadcast[bwdq]|vfmadd[0-9a-z]+|shlx|mulx)\b'
  ```
  Nonzero = won't run on a no-AVX2 CPU. On proc3 this forced `projectaria-tools==1.6.0` (>=1.7.1
  was AVX2-only). On a modern-CPU host this pin may be unnecessary — verify.
- **CUDA build toolchain — the uv way** ([decisions/0005](decisions/0005-uv-instead-of-conda.md)).
  Source/JIT CUDA builds (gsplat, diff-gaussian-rasterization) need `nvcc` matching the GPU. With
  uv there is no conda toolchain; provide it by one of:
  1. **System CUDA** matching the GPU if present: `export CUDA_HOME=/usr/local/cuda-12.x`. (proc6
     has none.)
  2. **NVIDIA pip wheels inside the venv** (UNTESTED — path wiring differs from conda):
     ```bash
     uv pip install nvidia-cuda-nvcc-cu12 nvidia-cuda-runtime-cu12
     # point CUDA_HOME at the nvcc wheel dir, add its include/ to CPATH, lib/ to LIBRARY_PATH:
     NV=$(python -c "import nvidia.cuda_nvcc, os; print(os.path.dirname(nvidia.cuda_nvcc.__file__))")
     export CUDA_HOME="$NV"
     export CPATH="$NV/include${CPATH:+:$CPATH}"
     ```
  Match the CUDA major/minor to the host driver (proc6: driver 525 / CUDA 12.0 — use a cu121 or
  minor-version-compatible build; the proc3 cu128 stack is newer than proc6's driver allows).

  <details><summary>proc3 conda fallback (only if the uv path fails and conda is available)</summary>

  proc3's base nvcc was CUDA 11.6 and couldn't target `sm_89`
  (`nvcc fatal : Unsupported gpu architecture 'compute_89'`). The conda fix there was:
  ```bash
  conda install -p <env> -c nvidia/label/cuda-12.8.1 \
    cuda-nvcc cuda-cudart-dev cuda-cccl libcublas-dev libcusparse-dev libcusolver-dev cuda-driver-dev
  # $CONDA_PREFIX/etc/conda/activate.d/cuda.sh:
  export CUDA_HOME="$CONDA_PREFIX"
  export CPATH="$CONDA_PREFIX/targets/x86_64-linux/include${CPATH:+:$CPATH}"
  export LIBRARY_PATH="$CONDA_PREFIX/targets/x86_64-linux/lib${LIBRARY_PATH:+:$LIBRARY_PATH}"
  ```
  </details>
- **`gsplat`** JIT-compiles on first use if no matching prebuilt wheel exists (~4.5 min on proc3 —
  a detached job looks hung meanwhile). Cache in `~/.cache/torch_extensions`. Red herring: gsplat
  internally raises & catches `TypeError: _jit_compile() missing 2 required positional arguments`
  (torch >= 2.7); the real error is further down.
- **Reference stack (proc3, via conda):** python 3.10 + torch 2.11.0+cu128 + gsplat 1.5.3 +
  projectaria-tools 1.6.0 (numpy 2.2.6). On proc6, install the equivalent with `uv pip` but on a
  **cu121** torch build (proc6 driver is CUDA 12.0, older than cu128); proc6's CPU has avx2 so the
  `projectaria-tools` pin is likely unneeded — verify.

### Shared-resource etiquette (applies to any shared host)
- **GPU may be shared:** check `nvidia-smi` for free VRAM before a CUDA job; never force onto an
  occupied GPU (can OOM-kill another user's run). For unattended runs, poll and wait for a free GPU.
- **Disk budget:** confirm a budget before large downloads; add a free-space floor to long scripts
  (e.g. abort if `/draft-hdd` free < 100 GiB). Poll `df`/`du` between phases. Keep data, frames,
  and envs under `/draft-hdd`; pip/uv caches under `$HOME` (large network mount) are fine.
- **Jobs must survive disconnects** (a dropped SSH already killed a multi-hour run):
  ```bash
  setsid nohup <cmd> > /path/to/log 2>&1 < /dev/null &
  # live output from block-buffered Lightning/rich:
  setsid script -q -f -c "env PYTHONUNBUFFERED=1 python -u <args>" /path/to/log < /dev/null &
  ```
  Report the log path + PID; kill anything you start when done.
- **Web UIs / ports:** a service on `0.0.0.0:<port>` is reachable at `http://localhost:<port>` via
  Kiro/VS Code port forwarding. One process per port.
