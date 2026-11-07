# Final draft notes (ufmg_proposta_mestrado/final)

Build: `cd final/exemplo && export PATH=/Library/TeX/texbin:$PATH && pdflatex exemplo && bibtex exemplo && pdflatex exemplo && pdflatex exemplo`

## Rules applied
1. Only uncommented draft ideas. 2. All dates in 2027; venue dates estimated from previous editions; ACCV / ICLR 2027 / CVPR 2027 dropped. 3. Lv et al. (facebookresearch/egocentric_splats) already run on whole Aria dataset = done work. 4. Verified refs only. 5. English, ppgccufmg mscproposta, 5 pages.

## Source map (paragraph -> run)
- Title: GPT-v1 ("Semantics-Aware Gaussian Splatting for Egocentric Hyperlapse"), year 2026.
- Tema par.1: GPT-v1 opening (O6) + refs from results2/claude-fable-5.1 (O3).
- Tema par.2: results2/claude-opus-5 (S1) + results2/gpt-5.6-sol (S3) + GPT-v1 tail.
- Tema par.3: GPT-v1 (K3, Gaussian SLAM sentence dropped) + results2/claude-fable-5.1 Lv sentence (G1).
- Tema par.4: results2/gpt-5.6-sol (T1, first sentence dropped).
- Tema par.5: results2/kimi-k3 (B1, B2) + results2/claude-fable-5.1 (G1).
- Objetivos: general = results2/claude-opus-5 (G1) with fable-style cite; items = opus (S1) + fable r2 (S2) + GPT-v1 (S5, items i and v), inline (i)-(v) style.
- Metodologia: Reconstruction = opus (R1); Replication = fable r2 (P1); Integration = fable r2 (S3) + GPT-v1 (T3 frame) + gpt r2 (T2 cost terms, SplaTraj); Evaluation = opus (E2).
- Aspectos Eticos: results2/claude-fable-5.1 (A1), verbatim.
- Cronograma: new prose, frame from fable r2 (A1), rows from opus (B1) / gpt r1 (B2) / gpt r2 (B3); user: replication under way, first results Jan-Feb 2027.
- Plano de Publicacao: intro = fable r2 (I2, "Table~\ref" -> "The table below"); table = opus (T1) with SIBGRAPI May -> Jun.

## Verified venue facts (checked 2026-10-28)
- ICCV 2027: Oct 3-7, Hong Kong (main conf 5-7). Deadline not posted; ICCV 2025 was Mar 7.
- SIGGRAPH 2027: Aug 8-12, Anaheim. Deadline not posted; usually Jan.
- SIBGRAPI: 2026 edition Sep 29-Oct 2; 2025 deadline Jun 23 -> extended Jun 30. 2027 call not published.
- ICLR 2027 deadline Sep 25, 2026 (passed). CVPR 2027 deadline Nov 16, 2026 (passed).
- ACCV biennial: Dec 8-12 2024, Dec 14-18 2026, next 2028.
- Lv et al. code: https://github.com/facebookresearch/egocentric_splats (SIGGRAPH 2025).

## Bib fixes vs source runs
silva2021sparse 43(4) (opus had 43(9), kimi 43(6)); ramos2022text full authors, no issue (TPAMI 45(2) is Feb 2023); furlan2018listening as arXiv (CVPR-W venue unverified); silva2016towards includes Ferreira; qin2024langsplat authors Qin, Li, Zhou, Wang, Pfister; guo2024semantic authors Guo, Ma, Fan, Liu, Li. DOIs removed for space (kept here: kopf 10.1145/2601097.2601195; joshi 10.1145/2766954; ramos2016 10.1109/ICIP.2016.7532977; silva2016 10.1007/978-3-319-46604-0_40; silva2018making 10.1016/j.jvcir.2018.02.013; silva2021 10.1109/TPAMI.2020.2983929; ramos2022 10.1109/TPAMI.2022.3157198; lv2025 10.1145/3721238.3730753; pan2023 10.1109/ICCV51070.2023.01842).

## Fitting tricks in exemplo.tex
natbib [numbers,sort&compress] + abbrvnat; \setlength{\afterchapskip}{0.6cm} before \chapter{}; bibliography in {\scriptsize\SingleSpacing\setlength{\bibsep}{2pt} ...}; venue table scriptsize + \SingleSpacing, bare tabular.

## Cuts applied for the 5-page fit (2026-10-28)
All menu items except T-e (EgoLifter sentence kept): T-a (VeRLab gaze/sound/social sentence; drops silva2018making, neves2020gaze, furlan2018listening, ramos2020personalizing), T-b, T-c, T-d, T-f, T-g, O-a, O-b, O-c, M-a (SplaTraj; drops liu2024splatraj), M-b, M-c, M-d, M-e, M-f, C-a, C-b, P-a. Tema par.1 and par.2 merged. Dissertation step moved to Jul--Dec. Pre-cut version kept as exemplo/exemplo.tex.v1. Result: 5 pages, 19 refs, 0 errors, ~4 lines of slack on page 4.
