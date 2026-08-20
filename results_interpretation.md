# Interpretation note — `20260819-035617-25bf2d99`

This file is written by the pipeline so the discussion section can cite a frozen run.

## What was analysed

**uci_smni_cmi** — UCI ML Repository EEG Database (DOI 10.24432/C5TS3D).
20 subjects, 64-channel ERPs at 256 Hz, 1.0 s epochs.
Trials averaged per subject for condition **S2 match**.
Features are mean amplitude in the P3 window (250--420 ms) on scalp electrodes
(UCI extra sensors X, Y, nd are dropped).
Classifiers use **nested CV**: each outer fold fits SNR ranking and a small
hyperparameter grid on the training subjects only.
Classes: control (0) vs. alcoholic (1).

## SNR spatial ranking

Highest SNR electrodes: CPZ (0.497), CP1 (0.399), AFZ (0.324), CZ (0.316), FZ (0.314), AF2 (0.311), C6 (0.301), F4 (0.300).

Of the top-10 SNR channels, 3 sit in the parietal P3 cluster.
Alcoholism-risk cohorts in the Begleiter literature often show a **reduced P3** over
centro-parietal scalp; check whether those electrodes dominate this ranking.

Red bars in Fig. 3 mark the P3 cluster. Fig. 4 is the scalp map.
Fig. 7 shows midline ERPs (dashed line = 320 ms).

## Correlation (Fig. 2)

Neighbour electrodes share volume-conducted signal, so the correlation matrix is
smooth rather than diagonal. That is why SNR ranking (or another spatial prune)
is used before linear / kernel models: correlated channels inflate variance.

## Classifiers

| Model | Protocol | Accuracy | Precision | Recall | F1 | ROC-AUC |
| --- | --- | --- | --- | --- | --- | --- |
| Random Forest | LOOCV | 0.500 | 0.500 | 0.700 | 0.583 | 0.480 |
| Random Forest | 10-fold | 0.450 | 0.462 | 0.600 | 0.522 | 0.530 |
| SVM (RBF) | LOOCV | 0.700 | 0.700 | 0.700 | 0.700 | 0.760 |
| SVM (RBF) | 10-fold | 0.550 | 0.545 | 0.600 | 0.571 | 0.490 |
| MLP | LOOCV | 0.500 | 0.500 | 0.500 | 0.500 | 0.520 |
| MLP | 10-fold | 0.550 | 0.545 | 0.600 | 0.571 | 0.460 |

LOOCV is the appropriate protocol for this small *N* (20 UCI SMNI subjects).
Hyperparameters and SNR channel count are chosen on an **inner** stratified
grid (no test-subject leakage). 10-fold numbers use the same nested scheme.

## How to cite this run

Use `run_id = 20260819-035617-25bf2d99` plus the git SHA stored in `manifest.json`.
Canonical IEEE copies live in `artefacts/figures/` and `artefacts/tables/`.
The immutable copies for this execution stay under `artefacts/runs/20260819-035617-25bf2d99/`.

## Caveat

This run uses the UCI **SMNI large** split (20 subjects), not the full 122-subject
archive (~700 MB). Nested search reduces default-hyperparameter failure (e.g. RF
below chance on 64-D vectors) but cannot invent a large effect in a small sample.
