# Reference Repositories

Mapping from each cited paper to its code repository, the local clone, its role in this
project, and notes needed to build/run it. All clones live as siblings of this `docs/` folder
under `/home/guilhermetoledo/projects/masters`.

Clone status verified on 2026-11-07 (`git clone` of each, `git fsck` clean on the main repo).
Clones were made **without submodules** to save disk; the "Submodules" column lists what must
be initialized before building.

## Cloned (have public code)

| Ref | Paper (short) | Upstream | Local folder | HEAD | Role |
|-----|---------------|----------|--------------|------|------|
| [9] | Lv et al., Photoreal Scene Reconstruction (SIGGRAPH'25) | facebookresearch/egocentric_splats | `facebook_egocentric` | 516dfe2 | **Core.** Egocentric 3DGS reconstruction; produces the Gaussian scenes used as input. Already run on Aria. |
| [6] | Kerbl et al., 3D Gaussian Splatting (TOG'23) | graphdeco-inria/gaussian-splatting | `gaussian-splatting` | 54c035f | Reference 3DGS implementation (baseline / rasterization reference). |
| [11] | Qin et al., LangSplat (CVPR'24) | minghanqin/LangSplat | `langsplat` | d70edb8 | **Core.** Open-vocabulary language features on splats (semantic lifting). |
| [4] | Guo et al., Semantic Gaussians | sharinka0715/semantic-gaussians | `semantic-gaussians` | ae53137 | Alternative semantic-feature projection onto an existing splat. |
| [3] | Gu et al., EgoLifter (ECCV'24) | facebookresearch/egolifter | `egolifter` | d177089 | Instance lifting from egocentric images into 3D; `egocentric_splats` builds on it. |
| [19] | Zhang et al., LPIPS (CVPR'18) | richzhang/PerceptualSimilarity | `perceptual-similarity-lpips` | 082bb24 | Perceptual metric for evaluation (also available as the `lpips` pip package). |
| [12] | Ramos et al., Fast-forward via semantic extraction (ICIP'16) | verlab/SemanticFastForward_ICIP_2016 | `semantic-fastforward-icip-2016` | b2ffeb7 | VeRLab semantic fast-forward lineage. |
| [15] | Silva et al., Towards Semantic FF + stabilization (EPIC@ECCV'16) | verlab/SemanticFastForward_ECCVW_2016 | `semantic-fastforward-eccvw-2016` | b22bd30 | Semantic fast-forward + stabilization baseline. |
| [16] | Silva et al., Weighted sparse sampling (CVPR'18) | verlab/SemanticFastForward_CVPR_2018 | `semantic-fastforward-cvpr-2018` | a8c6440 | Sparse-sampling + smoothing baseline. |
| [17] | Silva et al., Sparse-sampling framework (TPAMI'21) | verlab/SemanticFastForward_TPAMI_2020 | `semantic-fastforward-tpami-2020` | dd48cd5 | **Baseline (semantic side).** Repo is named `..._2020`; the proposal cites it as TPAMI 2021. |
| [13] | Ramos et al., Straight to the Point (CVPR'20) | verlab/StraightToThePoint_CVPR_2020 | `straight-to-the-point-cvpr-2020` | 86ca585 | Text-driven RL frame selection (VDAN). |
| [14] | Ramos et al., Text-driven Video Acceleration (TPAMI'22) | verlab/TextDrivenVideoAcceleration_TPAMI_2022 | `text-driven-video-accel-tpami-2022` | 878813d | **Baseline (semantic side).** Weakly-supervised RL acceleration. |

## Cloned — dataset-access tooling (code for the data papers)

These are code repositories, kept here with the other code. The datasets themselves live under
the data root (see [environment.md](environment.md)), not inside these clones.

| Ref | Paper | Upstream | Local folder | HEAD | Role |
|-----|-------|----------|--------------|------|------|
| [1],[10] | Project Aria; Aria Digital Twin (ADT) | facebookresearch/projectaria_tools | `projectaria_tools` | ebd58a3 | Read/preprocess Aria VRS, MPS, and ADT data. Dependency of `egocentric_splats`. |
| [2] | Ego-Exo4D (CVPR'24) | facebookresearch/Ego4d | `ego-exo4d` | 4bd10ed | Ego-Exo4D / Ego4D download + access CLI. |

## Additional relevant papers (beyond the proposal's 19 references)

Papers Guilherme added to `docs/papers/`. Those with public code are cloned; see
[decisions/0004](decisions/0004-extra-papers-and-two-machine-setup.md).

| Paper (short) | Upstream | Local folder | HEAD | Role / why kept |
|---------------|----------|--------------|------|------------------|
| FaVoR — Features via Voxel Rendering for Camera Relocalization (WACV'25) | utiasSTARS/FaVoR | `favor-relocalization` | fc3299b | Camera relocalization via rendered features; relevant to pose/trajectory estimation. |
| Utonia — Toward One Encoder for All Point Clouds (ICML'26) | Pointcept/Utonia | `utonia-point-encoder` | 5fce241 | Unified point-cloud encoder; candidate for 3D feature/semantic backbone. Inference code + weights. |
| VIGS-SLAM — Visual-Inertial Gaussian Splatting SLAM (ECCV'26) | cvg/VIGS-SLAM | `vigs-slam` | 22ffe24 | Visual-inertial GS SLAM; directly related to egocentric reconstruction + trajectory. |
| Silva et al., Making a long story short — multi-importance FF (JVCI'18) | verlab/SemanticFastForward_JVCI_2018 | `semantic-fastforward-jvci-2018` | 103277a | VeRLab semantic FF lineage; also a submodule of the CVPR'18 and TPAMI'20 repos. |

## Not cloned (no public code located)

| Ref | Paper | Why not | Impact |
|-----|-------|---------|--------|
| [8] | Li et al., **EgoSplat** (arXiv 2503.11345, 2025) | No public code release found (searched 2026-11-07; arXiv + project only). PDF in `docs/papers/`. | **Relevant.** EgoSplat is central to objective (i). Semantic lifting will likely be built on LangSplat / EgoLifter and re-implemented from the paper. Tracked in [decisions/0002](decisions/0002-reference-repository-selection.md). |
| [5] | Joshi et al., Real-time Hyperlapse (TOG'15) | Microsoft Research, no public code. | NVS-side baseline; reproduce from paper or compare qualitatively. |
| [7] | Kopf et al., First-person Hyperlapse (TOG'14) | Microsoft Research, no public code. | NVS-side baseline; reproduce from paper. |
| [18] | Warchocki et al., Evaluating dynamic 3DGS for egocentric (arXiv 2026) | TU Delft MSc thesis; no code repo located. PDF in `docs/papers/`. | Context / motivation only. |
| — | **KISS-GS** — 3DGS Compression Kept Simple (ECCV'26) | `fraunhoferhhi/KISS-GS` is a project-page repo only (gh-pages, no source). PDF in `docs/papers/`. | Optional: 3DGS compression for storing many scenes. Re-check for a source release. |
| — | Neves et al., A gaze-driven fast-forward (SIBGRAPI'20) | No dedicated code repo found. PDF in `docs/papers/verlab/`. | Context (semantic FF lineage). |
| — | Furlan et al., Fast-forwarding by Listening and Watching (CVPRW'18) | No dedicated code repo found. PDF in `docs/papers/verlab/`. | Context. |
| — | Ramos et al., Personalizing Fast-Forward Videos (social network) | No dedicated code repo found. PDF in `docs/papers/verlab/`. | Context. |

If code for any of these is released later, add the clone and update this table (and the relevant
decision record).

## Paper PDFs

All PDFs are under `docs/papers/` (VeRLab lineage under `docs/papers/verlab/`). The rendered thesis
proposal is `docs/thesis-template/exemplo/exemplo.pdf`; the writing template (UFMG `ppgccufmg`
class + PDF/A scripts) is the rest of `docs/thesis-template/`.

## Submodules to initialize before building

Clones were made shallow of submodules. Initialize per repo when you build it:

```bash
# generic
git -C <repo> submodule update --init --recursive
```

| Repo | Submodules | Notes |
|------|-----------|-------|
| `gaussian-splatting` | `submodules/simple-knn`, `submodules/diff-gaussian-rasterization`, `submodules/fused-ssim`, `SIBR_viewers` | Rasterizer + kNN need CUDA to build; SIBR_viewers is the (large) C++ viewer, optional. |
| `langsplat` | `submodules/langsplat-rasterization`, `submodules/simple-knn`, `submodules/segment-anything-langsplat` | CUDA build for the rasterizer. |
| `semantic-gaussians` | two `third_party/glm` under `channel-rasterization` and `rgbd-rasterization` | Header-only GLM. |
| `semantic-fastforward-cvpr-2018` | `_SemanticFastForward_JVCI_2018` | Shared MATLAB/Python code. |
| `semantic-fastforward-tpami-2020` | `_SemanticFastForward_JVCI_2018`, `_npy-matlab` | Shared code + npy-matlab bridge. |
| `favor-relocalization` | `thirdparty/PoseLib`, `thirdparty/SuperPoint`, `thirdparty/ALIKE` | Feature extractors + pose solver. |
| `vigs-slam` | `thirdparty/{lietorch,eigen,simple-knn,Sophus,lietorch_5090}`, `diff-gaussian-rasterization/third_party/glm` | CUDA builds; two lietorch variants (note `_5090`). |
| `facebook_egocentric`, `utonia-point-encoder`, `semantic-fastforward-jvci-2018`, and all other VeRLab/dataset repos | none | — |

Build/run specifics (CUDA versions, errors, fixes) belong in each repo's own runbook once it is
actually set up, not here.
