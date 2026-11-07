# Runbook — egocentric_splats (facebook_egocentric)

How to run the reconstruction code on a run host (proc6, proc2, …). See
[../environment.md](../environment.md) for the machine profiles and AVX2/CUDA/gsplat caveats, and
[../decisions/0007](../decisions/0007-git-fork-organization.md) for the git layout.

**Default: run from the shared baseline — no clone, no branch.** The code is run in place (it's not
pip-installed as a package; you run `python train_lightning.py` etc. from the repo). Running only
reads the source and writes outputs to `$RESULTS_ROOT` on local disk, so **both hosts can run from
the same `/home/.../masters/facebook_egocentric` at once** — each with its own venv on its own
`/draft-hdd`. A fork/clone/branch is needed **only when a host must change code**.

> **Never edit `./facebook_egocentric` in place while it's shared.** The instant you need a code
> change, switch to a per-host clone (section 3). Editing the baseline would disturb the other
> host's run.

> Run long steps detached (`setsid nohup … > log 2>&1 < /dev/null &`), report log + PID, and check
> `nvidia-smi`/disk first. On a multi-GPU host with a dead card, pin the healthy one, e.g.
> `export CUDA_DEVICE_ORDER=PCI_BUS_ID CUDA_VISIBLE_DEVICES=0`.

## 1. Verify the host and its local disk

```bash
hostname; nproc; grep -o -m1 avx2 /proc/cpuinfo || echo "no avx2"
nvidia-smi --query-gpu=name,memory.total,driver_version --format=csv
findmnt /draft-hdd          # MUST show its own device; if not a real mount, STOP (don't write to /)
df -h /draft-hdd
```
If the host isn't profiled in [../environment.md](../environment.md) yet, add a row for it.

## 2. Python env with uv (on local `/draft-hdd`, not in the repo)

```bash
export ENV_ROOT=/draft-hdd/guilhermetoledo/masters/envs
export UV_CACHE_DIR=/draft-hdd/guilhermetoledo/.uv-cache
export TORCH_EXTENSIONS_DIR=/draft-hdd/guilhermetoledo/masters/torch_ext   # gsplat JIT is per-GPU
export TMPDIR=/draft-hdd/guilhermetoledo/tmp
mkdir -p "$ENV_ROOT" "$TORCH_EXTENSIONS_DIR" "$TMPDIR"

uv venv --python 3.10 "$ENV_ROOT/ego_splats" && source "$ENV_ROOT/ego_splats/bin/activate"
# torch: match the host driver (proc6 driver = CUDA 12.0 -> cu121):
uv pip install torch torchvision --index-url https://download.pytorch.org/whl/cu121
uv pip install -r /home/guilhermetoledo/projects/masters/facebook_egocentric/requirements.txt
```

Caveats to verify (details in [../environment.md](../environment.md)): CPU AVX2 (proc6 has it, so
the proc3 `projectaria-tools==1.6.0` pin is likely unneeded); CUDA build without conda (gsplat JIT
`nvcc` from NVIDIA pip wheels — UNTESTED); gsplat JIT ~4.5 min on first use.

## 3. Download / preprocess / reconstruct (from the shared baseline)

Dataset lives on the **host's local `/draft-hdd`** (`$DATA_ROOT/aria_scenes`), so each host
downloads its own copy. Command + manifest in [../datasets/datasets.md](../datasets/datasets.md).
Then follow the repo README order (preprocess Aria VRS → train → render), writing outputs to
`$RESULTS_ROOT`. Log each run in [../results/](../results/) with real output and the path.

```bash
cd /home/guilhermetoledo/projects/masters/facebook_egocentric   # shared baseline, read-only
# ... run download/preprocess/train/render here, outputs -> $RESULTS_ROOT on local /draft-hdd
```

## 4. ONLY IF you must change code: per-host clone of the fork

Fork = `github.com/drawnator/egocentric_splats`. Do this **only** when a fix is required; otherwise
stay on the shared baseline above.

```bash
HOST=$(hostname)
RUN=/home/guilhermetoledo/projects/masters/run/$HOST
git clone https://github.com/drawnator/egocentric_splats.git "$RUN/facebook_egocentric"
cd "$RUN/facebook_egocentric"
git remote add upstream https://github.com/facebookresearch/egocentric_splats.git
# reuse fixes another host already pushed, or start the branch:
git fetch origin ufmg && git checkout ufmg  ||  git checkout -b ufmg
# now point your run at THIS tree; commit fixes here; push so other hosts can pull:
git push -u origin ufmg
```
Keep host-only differences (CUDA, GPU index) in env/config, not code — add a `proc<N>` branch only
if the code must genuinely differ per host. Record each fix + real error text below.

## Fixes applied

_None yet (2026-11-07) — proc6 has not changed the code (still downloading the dataset). Record any
future change here, with the error it fixes._

## Pushing `masters_ufmg` (docs)

The masters root is a git repo (`origin = github.com/drawnator/masters_ufmg`); `git push` works
from a host with GitHub credentials.
