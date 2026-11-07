# Dataset Registry

One row per dataset. Update location/size/status as data is downloaded and processed. Paths are
under `DATA_ROOT` (see [../environment.md](../environment.md)); `TBD` = not yet confirmed/placed.

Status legend: `planned` · `downloading` · `available` · `preprocessed` · `reconstructed`.

| Ref | Dataset | Used for | Access / tooling | Location (`$DATA_ROOT/…`) | Size | License / notes | Status |
|-----|---------|----------|------------------|---------------------------|------|-----------------|--------|
| [1] | **Project Aria — Aria Scenes** (v0.2) | Core: egocentric 3DGS reconstruction + hyperlapse input | Manifest [`AriaScenes_download_urls.json`](AriaScenes_download_urls.json) (already provided) + `facebook_egocentric/scripts/downloader.py`; read via `projectaria_tools` (verify any AVX2 pin on the run host) | `aria_scenes/` on proc6 (`/draft-hdd/guilhermetoledo/masters/data/aria_scenes`) | **133.2 GB** manifest; ≈111.8 GB actually downloaded (VRS + SLAM); 12 sequences | Research license, consent + anonymization applied. Per-seq on disk: `recording.vrs`, `mps/slam/{closed,open}_loop_trajectory.csv, online_calibration.jsonl, semidense_{points,observations}.csv.gz, summary.json` | **downloading** (2026-11-07, proc6) |
| [10] | **Aria Digital Twin (ADT)** | Semantic/eval benchmark (GT for localization/segmentation) | `projectaria_tools` (ADT loader) | `adt/` (TBD) | TBD | Research license | planned |
| [2] | **Ego-Exo4D** | Additional egocentric data for evaluation | `ego-exo4d` (Ego4D CLI; requires license agreement) | `ego_exo4d/` (TBD) | TBD (large; download subset) | Ego4D license agreement required | planned |

### Aria Scenes — 12 sequences

`bike_shop, garden, library, livingroom, microkitchen, multifloor, open_hallway, plant_hallway,
pop_up_shop, restaurant_patio, steakhouse_patio, sunroom`

Download command (run detached on the run host — currently `proc6`; see
[../environment.md](../environment.md) for etiquette and any CPU/CUDA wheel caveats to verify on
that host):

```bash
export DATA_ROOT=/draft-hdd/guilhermetoledo/masters/data
CDN=/home/guilhermetoledo/projects/masters/docs/datasets/AriaScenes_download_urls.json
cd /home/guilhermetoledo/projects/masters/facebook_egocentric
PY=/draft-hdd/guilhermetoledo/masters/envs/ego_splats/bin/python   # uv venv, projectaria-tools 1.7.1
# -d all -l all avoids the interactive y/n prompts (they hang/fail when detached):
$PY scripts/downloader.py --cdn_file "$CDN" -o "$DATA_ROOT/aria_scenes" -d all -l all
```

Notes (verified 2026-11-07, proc6): needs **projectaria-tools < 2**. 2.x moved the module and
fails with `No module named 'projectaria_tools.utils.datasets'`. The downloader skips
`video_main_rgb` (preview MP4, 18 GB) and `mps_artifacts` (3.4 GB), which the reconstruction code
doesn't use, so the real download is ≈111.8 GB. Full run log:
[../results/2026-11-07-aria-scenes-download.md](../results/2026-11-07-aria-scenes-download.md).

Per-sequence size (summed `file_size_bytes` from the manifest, computed 2026-11-07):

| Sequence | GB | Sequence | GB | Sequence | GB |
|---|---|---|---|---|---|
| bike_shop | 7.5 | microkitchen | 13.0 | pop_up_shop | 5.8 |
| garden | 9.3 | multifloor | 18.7 | restaurant_patio | 7.4 |
| library | 19.3 | open_hallway | 22.9 | steakhouse_patio | 8.3 |
| livingroom | 9.5 | plant_hallway | 7.7 | sunroom | 3.9 |

> **Manifest expiry: 2026-11-08, 16:13–20:07 UTC.** These times come from decoding the URLs'
> `oe=` hex timestamps (checked 2026-11-07). Downloads started after that will 403.
> On proc6 only 230 GiB is free on `/draft-hdd` (see [../environment.md](../environment.md)).

> The CDN URLs in the manifest are time-limited and expire. If a download 403s, re-export a fresh
> manifest from the Photoreal Reconstruction project page and replace the JSON. Confirm a disk
> budget before pulling all 133 GB (floor: stop if `/draft-hdd` free drops below 100 GiB).

## Reconstructed scenes — to be (re)produced on the run host

The proposal (§1.1, §1.3, §1.5) states the Lv et al. reconstruction was already run on the whole
Aria dataset "for this project." Those outputs are **not currently available**: that work used
`proc3`'s `/draft-hdd`, which was removed after a failure (2026-11-06). Plan to (re)produce the
reconstruction on the run host (download → preprocess → reconstruct). If a copy of the earlier
outputs is found on shared storage, record its path here and reuse it instead of recomputing.

| Artifact | Produced by | Location | Size | Status |
|----------|-------------|----------|------|--------|
| Gaussian scenes + trajectories (full Aria) | `facebook_egocentric` (egocentric_splats) | `$RESULTS_ROOT/...` (not yet produced) | — | **to do** (proposal claimed done; outputs lost with proc3 `/draft-hdd`) |

## Processing / derived data

Record reproducible preprocessing steps here (or link the script). Per-experiment transformations
go in [../results/](../results/), not here.

| Derived artifact | From | Command / script | Location | Status |
|------------------|------|------------------|----------|--------|
| _(none yet)_ | | | | |
