# Datasets

This folder lists the datasets used by the project and how they are managed. The actual data is
stored under `DATA_ROOT` (see [../environment.md](../environment.md)), never inside this repo.

- [datasets.md](datasets.md) — the registry: one row per dataset, with source, location, size,
  license, and status.

## Conventions

- **One entry per dataset** in [datasets.md](datasets.md). When you download or preprocess a
  dataset, update its row (location, size, status, date).
- **Derived data** (e.g. preprocessed Aria VRS, MPS outputs, extracted frames) is recorded as its
  own row or in the "Processing / derived" column, with the command/script that produced it so it
  is reproducible.
- **Changes applied to a dataset for a specific experiment** (filtering, subsampling, relevance
  annotation, train/val split) are logged in the experiment's entry under [../results/](../results/),
  and the dataset row links to the experiments that use it. Keep the registry about *what the
  dataset is and where it lives*; keep per-run transformations in results.
- **Licensing:** these are public egocentric datasets under research licenses with consent and
  anonymization already applied. Record the license and access requirements (some need
  registration) in each row.
