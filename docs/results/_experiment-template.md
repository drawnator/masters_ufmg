# <Experiment title>

- **Date:** YYYY-MM-DD
- **Author:** <who>
- **Workstream:** WS0 / WS1 / WS2 / WS3 / WS4 (see [../plan.md](../plan.md))
- **Goal / hypothesis:** what this run is meant to show or measure.
- **Status:** planned | running | done | failed

## Setup

| Item | Value |
|------|-------|
| Code repo + commit | `<repo>` @ `<short-sha>` |
| Local edits? | none / list uncommitted changes (and why) |
| Host / GPU | <hostname>, <GPU>, CUDA <x> |
| Environment | conda env / container, key package versions |

## Data & changes

- **Input dataset(s):** which rows from [../datasets/datasets.md](../datasets/datasets.md); sequence IDs.
- **Changes applied to the data:** subsample / filter / annotate / split / speed-up factor, etc.
- **Command/config that produced the processed data:**

```bash
# exact command(s)
```

## Commands run

Copy-pasteable, in execution order.

```bash
# exact command(s) actually run
```

## Output location

- Artifacts under `RESULTS_ROOT`: `<absolute path>`
- Verified the artifact exists (size / content): <how you checked>

## Results

| Metric | Value | Notes |
|--------|-------|-------|
| PSNR | | |
| SSIM | | |
| LPIPS | | |
| Semantic content retained | | |
| Instability index | | |
| Speed-up accuracy | | |
| Segment-level F1 | | |
| Wall-clock | | |
| Peak VRAM | | |

## Observations / failures

- Real error text and the fix, if any.
- Anything surprising; comparison to expectation or baseline.

## Follow-ups

- Next experiments or decisions this suggests (link to [../decisions/](../decisions/) if one results).
