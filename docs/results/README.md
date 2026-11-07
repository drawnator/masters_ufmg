# Results & Experiment Log

One file per experiment/run documenting what was done, on which data, with which code, the exact
commands, and the measured results. Large outputs (checkpoints, videos, metric dumps) live under
`RESULTS_ROOT` (see [../environment.md](../environment.md)); this folder holds only the write-up.

## How to log an experiment

1. Copy [`_experiment-template.md`](_experiment-template.md) to
   `YYYY-MM-DD-short-slug.md` (e.g. `2027-01-15-egosplats-livingroom-rgb.md`).
2. Fill in every section. Paste **real commands and real output**; do not paraphrase results.
3. Record the `RESULTS_ROOT` path where the artifacts actually landed, and verify the artifact
   exists (file present, non-trivial size, metric moved) — a command exiting 0 is not proof.
4. Add a row to [`index.md`](index.md).
5. Label anything you did not actually run as **UNTESTED**.

## What counts as "changes applied to a dataset"

Any transformation done for an experiment — subsampling, filtering transient objects, relevance
annotation, train/val split, resolution/rectification changes, speed-up factor — is described in
that experiment's **Data & changes** section, with the command or config that produced it, so the
run is reproducible and the effect on the dataset is traceable.
