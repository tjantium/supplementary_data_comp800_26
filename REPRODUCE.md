# Reproducing the cited results

**Run ID:** `20260819-035617-25bf2d99`

## Prerequisites

- Python ≥ 3.11
- [uv](https://docs.astral.sh/uv/) package manager
- Clone: `https://github.com/thiwankajayasiri/neuro_800_tasks`

## Commands

```bash
cd neuro_800_tasks
uv sync
uv run python src/download_data.py
uv run python src/preprocess.py
uv run python src/pipeline.py --note "UCI SMNI nested SNR + hyperparameter search"
```

The download step fetches `SMNI_CMI_TRAIN.tar.gz` and `SMNI_CMI_TEST.tar.gz` from the UCI KDD mirror (~74 MB total), parses raw `.rd.<trial>.gz` files, and writes `data/processed/epochs.npz`.

## Verify checksums

After download, compare archive hashes to `provenance/uci_download_urls.txt`:

```bash
shasum -a 256 data/raw/uci/SMNI_CMI_*.tar.gz
```

Compare tabulated outputs to this supplementary pack:

```bash
shasum -a 256 artefacts/supplementary/tables/tab3_top20_snr_channels.csv
shasum -a 256 artefacts/supplementary/tables/tab4_classifier_benchmark_loocv.csv
```

Expected values are listed in `CHECKSUMS.sha256`.

## Spot-check SNR

```bash
cd src && uv run python - <<'PY'
from preprocess import load_features
from feature_selection import compute_snr
import pandas as pd

features, labels, channels, _, _ = load_features()
snr = compute_snr(features, labels, channels)
tab = pd.read_csv("../artefacts/supplementary/tables/tab3_top20_snr_channels.csv")
print(snr.head(5))
print("Top channel match:", snr.iloc[0]["channel"] == tab.iloc[0]["channel"])
PY
```
