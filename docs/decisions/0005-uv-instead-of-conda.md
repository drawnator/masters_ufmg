# 0005. Use uv (not conda) for Python environments on the run host

- **Status:** Accepted
- **Date:** 2026-11-07
- **Deciders:** Guilherme Silva Toledo

## Context

The `facebook_egocentric` README sets up its env with `conda create -n ego_splats python=3.10`, and
the proc3 notes fixed the old CUDA toolchain by installing `cuda-nvcc` from the `nvidia` conda
channel. On proc6, the current run host, conda is not installed (`conda: command not found`). There
is no system CUDA toolkit either (no `/usr/local/cuda*`, no `nvcc`). `uv 0.12.17` is available at
`~/.local/bin/uv` (verified 2026-11-07). Guilherme asked to use uv instead of conda where possible.

## Decision

Create per-repo Python environments with **uv**, e.g. `uv venv --python 3.10` placed on the run
host's local disk, then `uv pip install ...`, following each repo's pip requirements. Get CUDA
build tools, if needed (e.g. for a `gsplat` JIT build), from NVIDIA's pip wheels
(`nvidia-cuda-nvcc-cu12`, etc.) inside the venv, not from conda.

## Alternatives considered

- **Install Miniconda/Miniforge in `$HOME`.** This follows the upstream README literally, but it
  adds a second package manager, and Guilherme prefers uv.
- **System CUDA toolkit.** Not installed, and installing it needs admin rights.

## Consequences

- The conda-based toolchain fix in [environment.md](../environment.md) needs a uv/pip equivalent.
  The pip `nvcc` wheels' layout differs from a conda `CUDA_HOME`, so expect some path wiring. Record
  it in `facebook_egocentric/AGENTS.md` once done.
- uv can download a managed Python 3.10 (system Python is 3.8.10).
- Revisit if a dependency is only available through conda.
