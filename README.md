<div align="center">

# 🎬 IMDb BERT Sentiment Analysis

![Python](https://img.shields.io/badge/Python-3.10-blue?logo=python) ![PyTorch](https://img.shields.io/badge/PyTorch-2.0-red?logo=pytorch) ![BERT](https://img.shields.io/badge/BERT-base--uncased-yellow?logo=huggingface) ![Dataset](https://img.shields.io/badge/IMDb-50K_Reviews-green) ![Status](https://img.shields.io/badge/status-complete-brightgreen)

**A research-grade NLP study fine-tuning BERT on 50K IMDb reviews — with error analysis, bias audit by review length, and a reproducible model card.**

> Upgraded from [SentimentAnalysis](https://github.com/DeekshithaKalluri/SentimentAnalysis) (CIS 732 — ML course), which benchmarked models on 2,500 samples. This version scales to the full 50K dataset, adds systematic error analysis identifying failure modes by review length, and introduces a bias audit and model card for research transparency.

</div>

---

## 📂 Repository Structure

| Path | Description |
|---|---|
| `outputs/figures/` | EDA, learning curves, confusion matrices, error plots |
| `outputs/results/` | CSV results for all models and error analysis |
| `config.py` | All hyperparameters and random seeds |
| `requirements.txt` | Locked dependency versions |

---

## 📝 Project Summary

Sentiment classification on the IMDb 50K dataset (25K train / 25K test, balanced), comparing classical and transformer-based approaches:

- **Logistic Regression** — TF-IDF with bigrams
- **LinearSVC** — TF-IDF with bigrams
- **Fine-Tuned BERT** — `bert-base-uncased`, 3 epochs on T4 GPU

All models evaluated on: **Accuracy · F1 Score · AUC-ROC · Confusion Matrix**

---

## 🔍 Key Results

| Model | Accuracy | F1 Score | AUC-ROC | Train Time |
|---|---|---|---|---|
| Logistic Regression | 0.8946 | 0.8950 | 0.9602 | 42.5s |
| LinearSVC | 0.8960 | 0.8957 | 0.9618 | 16.9s |
| **Fine-Tuned BERT** | **0.9232** | **0.9241** | **0.9767** | ~60 min |

✅ Fine-tuned BERT outperforms the best classical baseline by **+2.72% accuracy** and **+2.84% F1**

---

## 📈 BERT Training Progress

| Epoch | Avg Loss | Accuracy | F1 | AUC-ROC |
|---|---|---|---|---|
| 1 | 0.3216 | 0.9106 | 0.9141 | 0.9752 |
| 2 | 0.1675 | 0.9224 | 0.9215 | 0.9767 |
| **3** | **0.0882** | **0.9232** | **0.9241** | **0.9767** |

---

## 🔬 Error Analysis

- **1,921 total errors** on 25K test set (92.32% accuracy)
- **1,541 errors (80%) were high-confidence (>90%)** — BERT is systematically wrong, not uncertain
- **False positives dominate:** 1,114 neg→pos vs 807 pos→neg — model over-predicts positive sentiment
- **Primary failure mode:** mixed-sentiment reviews with contrast phrases
  - *"This was one of the worst Columbo episodes... However, I am only in the second season."* → labeled positive, predicted negative with 99.9% confidence

---

## ⚖️ Bias Audit — Accuracy by Review Length

| Length Bucket | # Reviews | Accuracy |
|---|---|---|
| Short (<100 words) | 3,091 | 93.53% |
| Medium (100–300 words) | 16,441 | 93.84% |
| **Long (>300 words)** | **5,468** | **87.03%** |

> 📉 BERT accuracy drops **6.81%** on long reviews — likely caused by 256-token truncation cutting off critical sentiment context in longer texts

---

## 🔁 Reproducibility

| Setting | Value |
|---|---|
| Random seed | `42` |
| Model | `bert-base-uncased` |
| Max token length | `256` |
| Batch size | `16` |
| Epochs | `3` |
| Learning rate | `2e-5` |
| Weight decay | `0.01` |
| Optimizer | `AdamW` |
| Warmup | `10% of total steps` |
| Dataset | `stanfordnlp/imdb` |

**Install dependencies:**
```bash
pip install transformers==4.40.0 datasets evaluate accelerate scikit-learn torch
```

---

## 🔮 Future Work

- Increase `max_length` to 512 to close the long-review accuracy gap
- Experiment with **RoBERTa** or **DistilBERT** for efficiency comparison
- Apply **LoRA / PEFT** for parameter-efficient fine-tuning
- Extend bias audit to genre and writing style dimensions

---

<div align="center">

**🔗 Related:** [Original Sentiment Analysis (CIS 732)](https://github.com/DeekshithaKalluri/SentimentAnalysis)

</div>
