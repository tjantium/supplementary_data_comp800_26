# Dataset authenticity verification

**Cited run:** `20260819-035617-25bf2d99`  
**Generated:** 2026-08-19  
**Purpose:** Prove that tabulated SNR / classifier numbers come from the real UCI Begleiter EEG Database (not a synthetic surrogate).

---

## 1. Clarification: 20 subjects ≠ synthetic

The UCI EEG Database ([id=121](https://archive.ics.uci.edu/dataset/121/eeg+database), DOI [10.24432/C5TS3D](https://doi.org/10.24432/C5TS3D)) ships **three official tiers**:

| Tier | File(s) | Subjects | Size |
| --- | --- | --- | --- |
| Small | `smni97_eeg_data.tar.gz` | 2 | ~4 MB |
| **Large (SMNI)** | `SMNI_CMI_TRAIN.tar.gz` + `SMNI_CMI_TEST.tar.gz` | **20** (10 alcoholic + 10 control) | ~74 MB |
| Full | `eeg_full.tar` / per-subject tarballs | 122 | ~700 MB |

This project downloads the **official Large / SMNI split** from the UCI KDD mirror, parses the raw `.rd.<trial>.gz` trial files, averages **S2 match** trials per subject, then runs SNR ranking and nested CV.  
\(N=20\) is the UCI-published large benchmark cohort — **not** a hand-made surrogate.

Subject IDs on disk match Begleiter naming (`co2a…` alcoholic, `co2c…` control):

```
co2a0000364, co2a0000365, co2a0000368, co2a0000369, co2a0000370,
co2a0000371, co2a0000372, co2a0000375, co2a0000377, co2a0000378,
co2c0000337, co2c0000338, co2c0000339, co2c0000340, co2c0000341,
co2c0000342, co2c0000344, co2c0000345, co2c0000346, co2c0000347
```

Each subject contributes **20 averaged S2-match trials** (10 from TRAIN + 10 from TEST).

---

## 2. Download provenance

| Item | Value |
| --- | --- |
| Mirror | `https://kdd.ics.uci.edu/databases/eeg/` |
| TRAIN URL | `…/SMNI_CMI_TRAIN.tar.gz` |
| TEST URL | `…/SMNI_CMI_TEST.tar.gz` |
| Remote `Content-Length` (HEAD) | TRAIN **38494526**, TEST **38815160** bytes |
| Local file size | TRAIN **38494526**, TEST **38815160** bytes (**exact match**) |
| Remote `Last-Modified` | Wed, 13 Oct 1999 (canonical UCI dump) |
| Extracted trial files | 600 TRAIN + 600 TEST `.rd.*.gz` |

Reproduce:

```bash
uv run python src/download_data.py
uv run python src/pipeline.py --note "UCI SMNI nested SNR + hyperparameter search"
```

---

## 3. SHA-256 checksums

### Raw UCI archives (integrity of the download)

```
755746fb5137bad3ba3d6734107bd62353426e7201d401154e07233b303df31b  data/raw/uci/SMNI_CMI_TRAIN.tar.gz
2f73a08d0a96302e9b8faac489a05da98bd70399128e039a031bb90abc96b130  data/raw/uci/SMNI_CMI_TEST.tar.gz
```

### Sample raw trial files (Begleiter IDs present on disk)

```
2f059116c23edd7f9e6d2fc91a0db5ff2541160a58469af54dcc78d80f002a0f  …/co2a0000364/co2a0000364.rd.009.gz   # TRAIN, S2 match
e85afe423d78f8802929a1faf328a129acf4d90698eb0838ac2198de4c9371fc  …/co2c0000337/co2c0000337.rd.000.gz   # TRAIN, control
d9137fd19347d1f1dfff932d28b943af3d70956ebe35c1e164733371629d33ad  …/co2a0000364/co2a0000364.rd.030.gz   # TEST
13bf4425c3d5eca4ab49373c79c46b311c73d727a702319d76b8897638205471  …/co2c0000337/co2c0000337.rd.038.gz   # TEST
```

Example header from `co2a0000364.rd.009.gz` (real UCI format):

```
# co2a0000364.rd
# 120 trials, 64 chans, 416 samples 368 post_stim samples
# 3.906000 msecs uV
# S2 match , trial 9
# FP1 chan 0
9 FP1 0 -0.163
```

### Processed tensors (pipeline inputs)

```
420424649b410d3b9c7a1e744f075a86ccc5d1649d7df49627fc595fc0cedcac  data/processed/epochs.npz
5437746f65402acec9180e78fc17fbf6a0ee67632934d655bd3b9e1519e564bb  data/processed/features.npz
0afd8f8fe5e23f61db12bb2375eabc8e07ce64cde53b494525259870633be1f7  data/raw/dataset_manifest.json
```

`epochs.npz` keys: `epochs (20, 64, 256)`, `labels (20,)`, `times (256,)`, `channel_names (64,)`, `source = uci_smni_cmi`.

### Cited nested-CV run (`20260819-035617-25bf2d99`)

```
71db23832f6a6a855df5b4aeafde133435ca6d55b8fa7060bf4ada5752847988  artefacts/runs/20260819-035617-25bf2d99/manifest.json
6387297bfd1f73aa6c066c5a98bdc6767812366d3ef04194e8a56f176d09e4a1  artefacts/runs/20260819-035617-25bf2d99/metrics/metrics.json
ee093bd65f4dc8ac4561c529d072702b4a3a89d88f383ad005c61852ba9650c8  artefacts/runs/20260819-035617-25bf2d99/tables/tab3_top20_snr_channels.csv
7c63119c7586cbdfd86e607430b4e9f3e029b910e5df14eddde4b3edacbf5a8a  artefacts/runs/20260819-035617-25bf2d99/tables/tab4_classifier_benchmark_loocv.csv
e5156feae32686fa3d5c2c5d41e7001f9404dfa7fc2eb7b07c7c2f9b28421443  artefacts/runs/20260819-035617-25bf2d99/tables/tab6_nested_modal_hyperparams.csv
c943cc505d0bed0ca29201d2f3d47e152243c5ed423b431757629603ea84fd08  artefacts/INTERPRETATION.md
```

Recompute checksums anytime:

```bash
shasum -a 256 data/raw/uci/SMNI_CMI_*.tar.gz data/processed/*.npz \
  artefacts/runs/20260819-035617-25bf2d99/tables/tab3_top20_snr_channels.csv \
  artefacts/runs/20260819-035617-25bf2d99/tables/tab4_classifier_benchmark_loocv.csv
```

---

## 4. Number spot-check (recomputed from `epochs.npz`)

Re-running the P3-window SNR formula on the processed tensor reproduces the tabulated values (max absolute delta on top-20 SNR < \(5\times10^{-5}\)):

| Rank | Channel | Recomputed SNR | Table SNR |
| --- | --- | --- | --- |
| 1 | CPZ | 0.497420 | 0.4974 |
| 2 | CP1 | 0.398959 | 0.3990 |
| 3 | AFZ | 0.324271 | 0.3243 |
| 4 | CZ | 0.316188 | 0.3162 |
| 5 | FZ | 0.314168 | 0.3142 |

Nested LOOCV (cited run):

| Model | Accuracy | ROC-AUC |
| --- | --- | --- |
| Random Forest | 0.50 | 0.48 |
| SVM (RBF) | **0.70** | **0.76** |
| MLP | 0.50 | 0.52 |

---

## 5. What to put in the IEEE report

Suggested wording for Section III (Data):

> Recordings were obtained from the UCI Machine Learning Repository EEG Database (Begleiter Neurodynamics Lab; DOI 10.24432/C5TS3D). We downloaded the official SMNI large split (`SMNI_CMI_TRAIN.tar.gz`, `SMNI_CMI_TEST.tar.gz`; SHA-256 listed in `artefacts/VERIFICATION.md`). The archives contain the same 20 subjects (10 alcoholic, 10 control). For each subject we averaged all S2-match trials across train and test (20 trials/subject), yielding subject-level ERP tensors of shape \((20, 64, 256)\) at 256 Hz. Extra UCI sensors X, Y, and nd were removed before SNR ranking and classification. The full 122-subject dump (~700 MB) was not used; \(N=20\) is the UCI-published large benchmark, not a synthetic cohort.

Also cite: `run_id = 20260819-035617-25bf2d99`.

---

## 6. Early synthetic runs (do not cite)

Runs `20260819-032452-*` and `20260819-033639-*` used a now-removed synthetic ERP generator for pipeline smoke-testing. They are archived under `artefacts/runs/` but **must not** appear in the report. All numbers in figures/tables for the paper should come from `20260819-035617-25bf2d99` (or a later UCI re-run).
