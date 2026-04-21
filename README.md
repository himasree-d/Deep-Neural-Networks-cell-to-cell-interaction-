# Predicting Cellular Interactions from Gene Expression Data



---

## Overview

This project builds a two-phase deep learning pipeline to predict cell-cell interactions from single-cell RNA sequencing (scRNA-seq) data. Given the gene expression profiles of two cell types, the models classify whether those cell types are likely to interact — a key step in understanding disease mechanisms, pregnancy biology, and cancer immunology.

The pipeline is trained and evaluated on the **CellPhoneDB Maternal-Fetal Interface** dataset (10x Genomics scRNA-seq).

---

## Pipeline

### Phase 1 — ANN (Feature Extractor)
An Artificial Neural Network is trained on concatenated gene expression vectors of cell pairs. It learns compact **64-dimensional representations** of cell-pair biology, which serve as input features for Phase 2.

### Phase 2 — CNN (Interaction Classifier)
The 64-dim ANN embeddings are fed into six CNN architectures, each trained independently to refine the interaction prediction:

| Architecture | Best Metric |
|---|---|
| LeNet | Balanced accuracy/F1 |
| AlexNet | Balanced FP/FN (no class bias) |
| VGGNet | Predicts all as positive |
| ZFNet | Best overall F1 (0.758) |
| GoogLeNet | Consistently outperforms ANN on Acc, Recall, F1 |
| ResNet | Highest recall (0.867) |

---

## Dataset

**CellPhoneDB Maternal-Fetal Interface** — 10x Genomics scRNA-seq

Preprocessing follows the CellPhoneDB v2.0 protocol:
- Top **200 highly variable genes** selected
- Up to **3,000 cells** subsampled per run

---

## Results Summary

Each member ran experiments on a different data subset/seed. Key findings:

- **ZFNet** achieved the best overall F1 score (0.758)
- **ResNet** achieved the highest recall (0.867), useful for discovery-focused tasks
- **VGGNet** collapsed completely on the 64-dim feature space (always predicts "Interact"), confirming deeper architectures need richer feature inputs
- **GoogLeNet** was the most consistent CNN, outperforming the ANN baseline across accuracy, recall, and F1
- **ANN alone** remained competitive — more balanced precision/recall than most CNNs
- Data sampling strategy significantly impacts results across configurations

Full per-member results are stored in the `*_results.json` files.

---

## Repo Structure

```
├── CellPhoneDB_ANN_LeNet.ipynb         # ANN + LeNet pipeline
├── CellPhoneDB_ANN_AlexNet.ipynb       # ANN + AlexNet pipeline
├── CellPhoneDB_ANN__VGGNet.ipynb       # ANN + VGGNet pipeline
├── CellPhoneDB_ANN_ZFNet.ipynb         # ANN + ZFNet pipeline
├── CellPhoneDB_ANN_GoogLeNet.ipynb     # ANN + GoogLeNet pipeline
├── CellPhoneDB_ANN__ResNet.ipynb       # ANN + ResNet pipeline
├── comparision.ipynb                   # Cross-architecture comparison
├── *_results.json                      # Saved metrics for each model
└── results_explanation.pdf             # Full presentation with plots
```

---

## Evaluation Metrics

All models are evaluated on:
- **Accuracy** — overall correct predictions
- **Precision** — of predicted interactions, how many are real
- **Recall** — of real interactions, how many were caught
- **F1 Score** — harmonic mean of precision and recall

---

## Tech Stack

- **Python** + **PyTorch**
- **scanpy** / **anndata** for scRNA-seq preprocessing
- **scikit-learn** for evaluation metrics
- Notebooks run on Google Colab / local GPU

---

## Use Cases

- Identifying novel signaling pathways between immune and placental cells during pregnancy
- Cancer immunology — mapping tumor-immune cell communication to guide immunotherapy design

---


## References

- Efremova et al. (2020). *CellPhoneDB: inferring cell–cell communication from combined expression of multi-subunit ligand–receptor complexes.* Nature Protocols.
- Dataset: [CellPhoneDB Maternal-Fetal Interface](https://www.ebi.ac.uk/biostudies/arrayexpress/studies/E-MTAB-6701)
