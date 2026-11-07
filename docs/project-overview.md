# Project Overview

**Title:** Semantics-Aware Gaussian Splatting for Egocentric Hyperlapse
**Author:** Guilherme Silva Toledo · **Advisor:** Erickson Rangel do Nascimento
**Program:** M.Sc. in Computer Science, UFMG · Draft proposal, 2026.

> This page summarizes the thesis proposal so the documentation is self-contained. The
> proposal PDF remains the authoritative source.

## Problem

Wearable cameras (e.g. Project Aria) record hours of footage. Naively speeding it up
(temporal subsampling) amplifies head motion and can drop the moments that explain an event.

Two prior families address this:

- **Semantic hyperlapse (2D / frame space):** assign relevance to frames and vary speed-up,
  then repair smoothness with stabilization. Limitation: these methods only emit *recorded*
  frames, reasoning in image space, so they cannot place the output camera at an unrecorded
  viewpoint, and smoothness stays coupled to the original shaky path.
- **Geometric hyperlapse:** reconstruct the trajectory, optimize a smoother virtual path, and
  synthesize novel views (First-person Hyperlapse).

## Proposed idea

Define relevance **in 3D** on a rendered representation. Reconstruct the egocentric scene with
Gaussian Splatting, attach open-vocabulary semantics per-Gaussian, and plan a smooth, fast
hyperlapse camera path over that spatial-semantic model, rendering novel views directly.

## Pipeline (as described in the proposal methodology)

1. **Reconstruction (per proposal; to be re-done — see status below):** Lv et al.
   (`egocentric_splats`) run on the whole Aria dataset → Gaussian scenes and trajectories as input.
2. **Replication (baselines):** reproduce sparse-sampling fast-forward (Silva et al.) and
   First-person Hyperlapse (Kopf et al.) on the same Aria sequences.
3. **Semantic lifting:** train LangSplat / EgoSplat-style open-vocabulary features on the
   produced splats; each Gaussian gets a relevance weight from language-feature ↔ text-query
   similarity.
4. **Path planning:** sample candidate output poses around a smoothed recorded trajectory; a
   solver minimizes a weighted cost (semantic visibility, deviation from source trajectory,
   low render confidence, occlusion, rotation, translational acceleration, jerk, speed-up
   error).
5. **Rendering:** render selected poses from the Gaussians; low-confidence frames fall back to
   nearby source views.

## Evaluation

- **Baselines:** Kopf et al. and Joshi et al. (NVS side); Silva et al. and Ramos et al.
  (semantic side).
- **Metrics:** semantic content retained, instability index, speed-up accuracy, segment-level
  F1, and PSNR / SSIM / LPIPS.
- **Data:** Project Aria, Aria Digital Twin (ADT), Ego-Exo4D.

## Status snapshot

- Reconstruction code: cloned (`facebook_egocentric` = upstream `egocentric_splats`). The proposal
  states it was already run on the full Aria set, but those outputs lived on proc3's `/draft-hdd`
  (removed 2026-11-06) and are not available — reconstruction is to be (re)done on the run host.
- Replication: under way.
- Remaining work scheduled Jan–Dec 2027. Primary venue SIBGRAPI 2027; stretch ICCV 2027.

See [repositories.md](repositories.md) for the code backing each component and
[plan.md](plan.md) for the implementation plan.
