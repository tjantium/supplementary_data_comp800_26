# Supplementary Material

**Spatio-Temporal EEG Pattern Recognition in Genetic Predisposition to Alcoholism**

COMP800 Neuroinformatics — Auckland University of Technology (AUT)  
**Pipeline run:** `20260819-035617-25bf2d99` · **Git SHA:** `c135a75` · **Generated:** August 2026

---

## 1. Purpose of this package

This folder is the **secondary / supplementary material** companion to the main IEEE-format assignment report. It is intended for examiners, reviewers, or readers who want to inspect **data provenance**, **reproducibility evidence**, and **frozen numerical outputs** without navigating the full software repository.

Contents are **read-only snapshots** from one completed pipeline execution. Figures and tables match the values cited in the report discussion.

---

## 2. How to cite

### 2.1 This supplementary package (IEEE-style)

> T. Jayasiri, “Supplementary Material: Spatio-Temporal EEG Pattern Recognition in Genetic Predisposition to Alcoholism,” COMP800 Neuroinformatics Assignment, Auckland Univ. of Technol., Aug. 2026. Pipeline run\_id `20260819-035617-25bf2d99`.

### 2.2 Analysis pipeline (software)

> T. Jayasiri, *Spatio-Temporal EEG Pattern Recognition in Genetic Predisposition to Alcoholism*, v0.1.0, 2026. [Online]. Available: https://github.com/thiwankajayasiri/neuro_800_tasks

### 2.3 Primary dataset

> H. Begleiter, “EEG Database — SMNI Large Split,” UCI Machine Learning Repository, 1999. doi: [10.24432/C5TS3D](https://doi.org/10.24432/C5TS3D)

### 2.4 ERP task specification

> X. L. Zhang, H. Begleiter, B. Porjesz, W. Wang, and A. Litke, “Event related potentials during object recognition tasks,” *Brain Res. Bull.*, vol. 38, no. 6, pp. 531–538, 1995. doi: [10.1016/0361-9230(95)02023-5](https://doi.org/10.1016/0361-9230(95)02023-5)

### 2.5 BibTeX

Machine-readable records are in [`citation/CITATION.bib`](citation/CITATION.bib). Example:

```bibtex
@misc{jayasiri2026eeg_supplementary,
  author       = {Jayasiri, Thiwanka},
  title        = {Supplementary Material: Spatio-Temporal {EEG} Pattern Recognition in Genetic Predisposition to Alcoholism},
  howpublished = {COMP800 Neuroinformatics Assignment, Auckland University of Technology},
  year         = {2026},
  note         = {Pipeline run\_id 20260819-035617-25bf2d99}
}
```

---

## 3. Dataset used

| Property | Value |
| --- | --- |
| Source | UCI ML Repository — EEG Database (id = 121) |
| DOI | [10.24432/C5TS3D](https://doi.org/10.24432/C5TS3D) |
| Split | SMNI **large** tier (official UCI download) |
| Subjects | **20** (10 alcoholic, 10 control) |
| Spatial | 64 scalp channels (10/20 extended); **61** used after dropping non-scalp UCI sensors X, Y, nd |
| Temporal | 256 Hz, 1.0 s epochs (256 samples) |
| Condition | Delayed matching-to-sample — **S2 match** trials averaged per subject |
| Labels | 0 = control, 1 = alcoholic |

**Download URLs and SHA-256 checksums** for the raw archives are in [`provenance/uci_download_urls.txt`](provenance/uci_download_urls.txt).  
**Subject IDs** are listed in [`provenance/subject_list.txt`](provenance/subject_list.txt).  
Parsed metadata from the download step is in [`data/dataset_manifest.json`](data/dataset_manifest.json).

> **Note:** Raw `.tar.gz` archives (~74 MB) are **not** bundled here due to size and UCI redistribution terms. They can be re-downloaded from the URLs above; checksums prove byte-identical copies were used.

---

## 4. Analysis summary (cited run)

| Stage | Description |
| --- | --- |
| Preprocessing | Per-subject mean ERP for S2 match; P3-window feature = mean amplitude 250–420 ms post-stimulus |
| Feature ranking | Signal-to-noise ratio (SNR) per channel: \|μ₁ − μ₀\| / (σ₁ + σ₀) |
| Correlation | 64×64 inter-channel Pearson matrix (Fig. 2) |
| Classifiers | Random Forest, RBF-SVM, MLP |
| Validation | **Nested** LOOCV and 10-fold stratified CV (inner grid: SNR channel count + hyperparameters) |
| Random seed | 42 |

### 4.1 Key results (LOOCV)

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC |
| --- | --- | --- | --- | --- | --- |
| Random Forest | 0.500 | 0.500 | 0.700 | 0.583 | 0.480 |
| **SVM (RBF)** | **0.700** | **0.700** | **0.700** | **0.700** | **0.760** |
| MLP | 0.500 | 0.500 | 0.500 | 0.500 | 0.520 |

Full tables: [`tables/tab4_classifier_benchmark_loocv.tex`](tables/tab4_classifier_benchmark_loocv.tex) (LOOCV), [`tables/tab5_classifier_benchmark_kfold.tex`](tables/tab5_classifier_benchmark_kfold.tex) (10-fold), [`tables/tab6_nested_modal_hyperparams.tex`](tables/tab6_nested_modal_hyperparams.tex) (inner-CV winners).

Top SNR channels: CPZ, CP1, AFZ, CZ, FZ — see [`tables/tab3_top20_snr_channels.csv`](tables/tab3_top20_snr_channels.csv).

Narrative interpretation for the discussion section: [`results_interpretation.md`](results_interpretation.md).

---

## 5. File guide

```
supplementary/
├── README.md                 ← this guide
├── REPRODUCE.md              ← step-by-step reproduction commands
├── VERIFICATION.md           ← provenance audit (checksums, spot-checks)
├── CHECKSUMS.sha256          ← SHA-256 of every file in this folder
├── MANIFEST.json             ← machine-readable inventory
├── run_manifest.json         ← run metadata (data, methods, run_id)
├── results_interpretation.md ← discussion-ready summary
├── citation/
│   └── CITATION.bib          ← IEEE BibTeX records
├── data/
│   └── dataset_manifest.json ← download/parser metadata
├── provenance/
│   ├── uci_download_urls.txt
│   └── subject_list.txt
├── metrics/
│   └── metrics.json          ← full JSON metrics export
├── tables/                   ← CSV + LaTeX (report-ready)
└── figures/                  ← 300 DPI PNG + vector PDF
```

### Figures

| File | Description |
| --- | --- |
| `fig1_prisma_flowchart.pdf` | PRISMA 2020 literature screening flow |
| `fig2_channel_correlation_heatmap.png` | Inter-electrode correlation (subset) |
| `fig3_snr_feature_ranking.png` | Top-20 SNR channel bar chart |
| `fig4_spatial_headmap_topography.png` | Scalp topography of SNR weights |
| `fig5_model_roc_curves.png` | ROC curves (RF, SVM, MLP) |
| `fig6_confusion_matrices.png` | Confusion matrices per model |
| `fig7_erp_waveforms.png` | Grand-average midline ERP waveforms |

### Tables

| File | Description |
| --- | --- |
| `tab1_prisma_literature_matrix.*` | Systematic review synthesis matrix |
| `tab2_dataset_spatiotemporal_specs.*` | Dataset specification |
| `tab3_top20_snr_channels.*` | SNR ranking (all channels + top 20) |
| `tab4_classifier_benchmark_loocv.*` | Classifier metrics — LOOCV |
| `tab5_classifier_benchmark_kfold.*` | Classifier metrics — 10-fold |
| `tab6_nested_modal_hyperparams.*` | Inner-CV selected hyperparameters |

---

## 6. Integrity and verification

- **`CHECKSUMS.sha256`** — verify any file: `shasum -a 256 -c CHECKSUMS.sha256`
- **`VERIFICATION.md`** — documents UCI archive sizes, `epochs.npz` hash, and independent SNR recomputation
- **`run_manifest.json`** — frozen run_id, DOI, subject count, and method summary

To reproduce from source code, see [`REPRODUCE.md`](REPRODUCE.md).

---

## 7. Scope limits (read before citing)

1. **Sample size:** This run uses the UCI SMNI **large** split (*N* = 20), not the full 122-subject archive (~700 MB). LOOCV metrics are appropriate for small *N* but have high variance.
2. **No clinical claim:** Results are coursework classification benchmarks on a public ERP dataset; they are not diagnostic evidence.
3. **Excluded runs:** Earlier repository runs on synthetic surrogate data (run_ids `20260819-032452-*`, `20260819-033639-*`) must **not** be cited; they achieved trivial 1.0 accuracy on separable fake data.

---

## 8. Contact and licence

**Author:** Thiwanka Jayasiri · COMP800 Neuroinformatics, AUT  
**Repository:** https://github.com/thiwankajayasiri/neuro_800_tasks  
**Licence:** MIT (software); figures/tables in this pack may be reused with attribution per assignment guidelines.
