# Aria Scenes v0.2 — full download on proc6

- **Date:** 2026-11-07
- **Author:** Guilherme Silva Toledo (run by Kiro)
- **Workstream:** WS0 (see [../plan.md](../plan.md))
- **Goal / hypothesis:** Get all 12 Aria Scenes sequences (VRS + MPS SLAM) onto proc6's `/draft-hdd`
  before the manifest URLs expire on 2026-11-08 at ~16:13 UTC.
- **Status:** running

## Setup

| Item | Value |
|------|-------|
| Code repo + commit | `facebook_egocentric` @ `516dfe2` |
| Local edits? | none (`git status --short` empty) |
| Host / GPU | proc6. No GPU used (network/disk only) |
| Environment | uv venv `/draft-hdd/guilhermetoledo/masters/envs/ego_splats` (CPython 3.10.21), **projectaria-tools 1.7.1** |

### Env creation (real output)

```bash
cd /draft-hdd/guilhermetoledo/masters/envs
uv venv --python 3.10 ego_splats            # -> Using CPython 3.10.21
VIRTUAL_ENV=$PWD/ego_splats uv pip install projectaria-tools
```
This first try installed **projectaria-tools 2.3.0**, which breaks the repo's downloader:
```
ModuleNotFoundError: No module named 'projectaria_tools.utils.datasets'
```
In 2.x the downloader moved to `projectaria_tools/tools/dataset_downloader/`. The repo's code
(`scripts/downloader.py`, `scripts/extract_aria_vrs.py`, `scene/*.py`) uses the 1.x API, so I
pinned the latest 1.x rather than patching upstream:
```bash
VIRTUAL_ENV=$PWD/ego_splats uv pip install 'projectaria-tools<2'
#  - projectaria-tools==2.3.0
#  + projectaria-tools==1.7.1
ego_splats/bin/python -c "from projectaria_tools.utils.datasets.dataset_downloader import DatasetDownloader; from projectaria_tools.core import data_provider, mps, calibration; print('imports OK')"
# imports OK
```
1.7.1 is the AVX2-only build that crashed proc3 with SIGILL. It imports cleanly on proc6, which
has AVX2.

## Commands run

The README form (`--sequence_names` omitted, no `-d`) prompts interactively for input, which hangs
or fails with stdin closed. So I passed `-d all -l all`. The job runs detached, with a watchdog
that kills the download if `/draft-hdd` free space drops below 100 GiB:

```bash
cd /home/guilhermetoledo/projects/masters/facebook_egocentric
M=/draft-hdd/guilhermetoledo/masters; LOG=$M/logs/aria_download_$(date +%Y%m%d_%H%M%S).log
setsid nohup bash -c '
M=/draft-hdd/guilhermetoledo/masters
PYTHONUNBUFFERED=1 $M/envs/ego_splats/bin/python scripts/downloader.py \
  --cdn_file /home/guilhermetoledo/projects/masters/docs/datasets/AriaScenes_download_urls.json \
  -o $M/data/aria_scenes -d all -l all &
DL=$!; echo "downloader pid=$DL start=$(date -u)"
while kill -0 $DL 2>/dev/null; do
  AV=$(df --output=avail -BG /draft-hdd | tail -1 | tr -dc 0-9)
  if [ "$AV" -lt 100 ]; then echo "DISK FLOOR HIT: ${AV}G free, killing $DL $(date -u)"; kill $DL; fi
  sleep 60
done
wait $DL; echo "downloader exit=$? end=$(date -u)"' > "$LOG" 2>&1 < /dev/null &
```
- Log: `/draft-hdd/guilhermetoledo/masters/logs/aria_download_20261107_145235.log`
- PIDs: wrapper 1234268, downloader 1234272. Started 2026-11-07 17:52:35 UTC.

## Output location

- `/draft-hdd/guilhermetoledo/masters/data/aria_scenes/<sequence>/{recording.vrs, mps/slam/*}`
- Verified so far (2026-11-07 ~14:56 local): `sunroom/recording.vrs` = 3,321,602,142 bytes, which
  matches the manifest's 3.32 GB. `mps/slam/` holds `closed_loop_trajectory.csv`,
  `open_loop_trajectory.csv`, `online_calibration.jsonl`, `semidense_points.csv.gz`,
  `semidense_observations.csv.gz`, `summary.json`. `.download_status.json` shows all types `true`.

## Results

| Metric | Value | Notes |
|--------|-------|-------|
| Throughput | ~45–50 MiB/s | from tqdm in the log |
| Expected on-disk size | ~111.8 GB (VRS 108.4 + SLAM 3.4) | see below |
| Wall-clock | _pending_ | |

## Observations / failures

- **Only part of the manifest is downloaded.** projectaria-tools 1.7.1's `DatasetDownloader` fetches
  `main_vrs`, `mps_slam_{trajectories,calibration,points,summary}` (and eye-gaze/hand-tracking,
  which these sequences lack). It **skips `video_main_rgb`** (preview MP4, 18.0 GB total) and
  **`mps_artifacts`** (3.4 GB). Neither is referenced anywhere in `facebook_egocentric`, so they are
  not needed for reconstruction. Real total ≈ 111.8 GB, not 133.2 GB.
- The downloader stages each file in `/tmp/tmpXXXX/` (on `/`, 410 GiB free), then extracts or moves
  it into place. A `du` taken mid-file shows a near-empty sequence folder.
- `Data type mps_eye_gaze / mps_hand_tracking is not available for sequence: …` is informational.

## Follow-ups

- When the job ends: check every sequence's `recording.vrs` size against the manifest, check
  `.download_status.json`, and fill in wall-clock and final `du`.
- Then: install the full `facebook_egocentric` stack into the same venv (torch/gsplat for driver
  525 / CUDA 12.0) and write `facebook_egocentric/AGENTS.md`.
