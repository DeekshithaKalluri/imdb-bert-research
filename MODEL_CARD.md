<div align="center">

# 🤖 Model Card — IMDb BERT Sentiment Classifier

![Model](https://img.shields.io/badge/Model-bert--base--uncased-yellow?logo=huggingface) ![Task](https://img.shields.io/badge/Task-Sentiment_Classification-blue) ![Dataset](https://img.shields.io/badge/Dataset-IMDb_50K-green) ![Accuracy](https://img.shields.io/badge/Accuracy-92.32%25-brightgreen) ![F1](https://img.shields.io/badge/F1-0.9241-brightgreen)

*Following the [Hugging Face Model Card](https://huggingface.co/docs/hub/model-cards) standard for research transparency.*

</div>

---

## 📋 Model Details

| Field | Value |
|---|---|
| **Base model** | `bert-base-uncased` |
| **Model type** | BERT fine-tuned for sequence classification |
| **Task** | Binary sentiment classification (positive / negative) |
| **Author** | Deekshitha Chowdary Kalluri |
| **Language** | English |
| **License** | MIT |

---

## 🎯 Intended Use

**Primary use:**
- Classifying sentiment of English movie reviews as positive or negative

**Intended users:**
- NLP researchers comparing transformer vs classical baselines
- Developers building review analysis pipelines

**Out-of-scope use:**
- Non-English text
- Reviews outside the entertainment/media domain
- Fine-grained sentiment (neutral, ratings beyond binary)
- Safety-critical applications

---

## 📦 Training Data

| Property | Value |
|---|---|
| **Dataset** | `stanfordnlp/imdb` |
| **Size** | 50,000 reviews (25K train / 25K test) |
| **Balance** | Perfectly balanced — 12,500 positive, 12,500 negative per split |
| **Domain** | English-language movie reviews from IMDb |
| **Labels** | `0` = negative, `1` = positive |

---

## ⚙️ Training Procedure

| Hyperparameter | Value |
|---|---|
| **Random seed** | `42` |
| **Max token length** | `256` |
| **Batch size** | `16` |
| **Epochs** | `3` |
| **Learning rate** | `2e-5` |
| **Optimizer** | `AdamW` |
| **Weight decay** | `0.01` |
| **LR schedule** | Linear warmup + decay |
| **Warmup steps** | 10% of total steps (468 / 4689) |
| **Gradient clipping** | `1.0` |
| **Hardware** | NVIDIA Tesla T4 (Google Colab) |
| **Training time** | ~60 minutes |

---

## 📊 Evaluation Results

### Overall Performance

| Metric | Score |
|---|---|
| **Accuracy** | 0.9232 |
| **F1 Score** | 0.9241 |
| **AUC-ROC** | 0.9767 |
| **Precision (pos)** | 0.92 |
| **Recall (pos)** | 0.93 |

### Comparison to Baselines

| Model | Accuracy | F1 | AUC-ROC |
|---|---|---|---|
| Logistic Regression | 0.8946 | 0.8950 | 0.9602 |
| LinearSVC | 0.8960 | 0.8957 | 0.9618 |
| **BERT (this model)** | **0.9232** | **0.9241** | **0.9767** |

---

## 🔬 Error Analysis

- **Total errors:** 1,921 / 25,000 (7.68%)
- **False positives:** 1,114 (neg predicted as pos)
- **False negatives:** 807 (pos predicted as neg)
- **High-confidence errors (>90%):** 1,541 — the model is systematically wrong, not uncertain

**Identified failure modes:**
1. **Mixed-sentiment reviews** — contrast phrases like *"worst episode... However, I enjoyed"* confuse the classifier
2. **Long reviews truncated** — reviews exceeding 256 tokens lose critical ending context
3. **Irony and sarcasm** — strongly negative language used positively (e.g. *"terribly good"*)

---

## ⚖️ Bias Audit

Performance was evaluated across review length buckets to identify systematic disparities:

| Length Bucket | # Reviews | Accuracy | Δ vs Overall |
|---|---|---|---|
| Short (<100 words) | 3,091 | 93.53% | +1.21% |
| Medium (100–300 words) | 16,441 | 93.84% | +1.52% |
| **Long (>300 words)** | **5,468** | **87.03%** | **−6.29%** |

> ⚠️ **Finding:** The model shows a statistically meaningful accuracy gap of **6.81%** on long reviews. This is likely caused by the 256-token truncation window cutting off sentiment-bearing content that appears late in longer reviews.

**Mitigation recommendation:** Increase `max_length` to 512 or use a sliding-window approach for long-form text.

---

## ⚠️ Limitations

- **Token truncation:** Reviews longer than 256 tokens are truncated, which measurably reduces accuracy on long-form content
- **Domain specificity:** Trained only on movie reviews — performance may degrade on product reviews, social media, or other domains
- **Binary labels only:** Cannot distinguish neutral, mixed, or fine-grained sentiment
- **English only:** Not evaluated on non-English text
- **Static model:** Does not adapt to evolving language or new review styles post-training

---

## 🔁 Reproducibility

All experiments are fully reproducible with seed `42`. See `config.py` for all hyperparameters and `requirements.txt` for the locked environment.

```bash
pip install transformers==4.40.0 datasets evaluate accelerate scikit-learn torch
```

---

<div align="center">

**🔗 Full study:** [imdb-bert-research](https://github.com/DeekshithaKalluri/imdb-bert-research) · **🔗 Original project:** [SentimentAnalysis](https://github.com/DeekshithaKalluri/SentimentAnalysis)

</div>
