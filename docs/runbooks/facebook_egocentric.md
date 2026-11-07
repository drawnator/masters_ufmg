# Runbook — egocentric_splats (facebook_egocentric) per-host setup

How to get the reconstruction code running on a run host (proc6, proc2, …). Code lives on durable
`/home`; venv + build + data + results live on local `/draft-hdd`. Fork =
`github.com/drawnator/egocentric_splats`. See [../environment.md](../environment.md) for the full
machine profile and the AVX2/CUDA/gsplat caveats, and [../decisions/0007](../decisions/0007-git-fork-organization.md)
for why it's laid out this way.

> Run long steps detached (`setsid nohup … > log 2>&1 < /dev/null &`), report log + PID, and check
> `nvidia-smi`/disk first. On proc6 pin the healthy GPU: `export CUDA_DEVICE_ORDER=PCI_BUS_ID CUDA_VISIBLE_DEVICES=0`.

## 1. Per-host working clone of the fork (on `/home`)

```bash
HOST=$(hostname)                      # or set explicitly: HOST=proc6
RUN=/home/guilhermetoledo/projects/masters/run/$HOST
mkdir -p "$RUN" && cd "$RUN"
git clone https://github.com/drawnator/egocentric_splats.git facebook_egocentric
cd facebook_egocentric
git remote add upstream https://github.com/facebookresearch/egocentric_splats.git
git checkout -b ufmg                  # make all run-fixes here
```

## 2. Python env with uv (on local `/draft-hdd`, not in the repo)

```bash
export ENV_ROOT=/draft-hdd/guilhermetoledo/masters/envs
export UV_CACHE_DIR=/draft-hdd/guilhermetoledo/.uv-cache
export TORCH_EXTENSIONS_DIR=/draft-hdd/guilhermetoledo/masters/torch_ext   # gsplat JIT is per-GPU
export TMPDIR=/draft-hdd/guilhermetoledo/tmp
mkdir -p "$ENV_ROOT" "$TORCH_EXTENSIONS_DIR" "$TMPDIR"

uv venv --python 3.10 "$ENV_ROOT/ego_splats" && source "$ENV_ROOT/ego_splats/bin/activate"
# torch: match the host driver. proc6 driver = CUDA 12.0 -> cu121:
uv pip install torch torchvision --index-url https://download.pytorch.org/whl/cu121
uv pip install -r requirements.txt
```

Caveats to verify on the host (details in [../environment.md](../environment.md)):
- **CPU AVX2:** proc6 *has* avx2, so the proc3 `projectaria-tools==1.6.0` pin is likely unneeded —
  verify; on a pre-AVX2 CPU, AVX2 wheels crash (SIGILL / exit 132).
- **CUDA build (no conda):** if a CUDA build is needed (gsplat JIT), get `nvcc` from NVIDIA pip
  wheels in the venv (`uv pip install nvidia-cuda-nvcc-cu12 nvidia-cuda-runtime-cu12`) and set
  `CUDA_HOME` to them — UNTESTED path wiring.
- **gsplat** JIT-compiles on first use (~4.5 min — looks hung).

## 3. Record fixes, run, log

- As you change code to make it run, commit on `ufmg`. **Push to the fork only when there are real
  code changes** (`git push -u fork ufmg` after `git remote rename origin fork` or add a `fork`
  remote). Capture every fix + real error text here under a "Fixes applied" section.
- Follow the repo README order: preprocess Aria VRS → train → render (commands in
  [../../facebook_egocentric/README.md](../../facebook_egocentric/README.md) and
  [../datasets/datasets.md](../datasets/datasets.md)).
- Log each run in [../results/](../results/) with real output and the `$RESULTS_ROOT` path.

## Fixes applied (fill in on first run)

_None yet — the previous working setup was lost with proc3's `/draft-hdd`. Record each change here._

## Pushing `masters_ufmg` (docs) — first push, needs credentials

The masters root is a local git repo with remote `origin = github.com/drawnator/masters_ufmg`, one
commit ready. proc3 had no GitHub auth at setup, so push from a host that does:

```bash
cd /home/guilhermetoledo/projects/masters
git push -u origin main
# if HTTPS prompts: use a GitHub Personal Access Token as the password, or set up gh/SSH first.
```
