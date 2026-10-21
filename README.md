# Mapping Public Attitudes toward E-Agriculture in Global Twitter Discourse

Replication package for:

> Boshnjaku, A., Plasari, E. and Fata, I. "Mapping Public Attitudes toward E-Agriculture in Global Twitter Discourse", *AGRIS on-line Papers in Economics and Informatics* (under review).

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23532401.svg)](https://doi.org/10.5281/zenodo.23532401)


The study analyses 10,009 English-language tweets on e-agriculture posted between 8 July and 26 December 2024. It compares three sentiment classifiers (TextBlob, VADER and the transformer `cardiffnlp/twitter-roberta-base-sentiment`) against 600 manually annotated tweets. It then models topics with Latent Dirichlet Allocation and examines temporal, account-type and geographical patterns.

## Contents

| Path | Description |
|---|---|
| `data/tweets_labels.csv` | One row per tweet (10,009): tweet ID, timestamp, language, all sentiment scores and labels, LDA topic, account-type and geocoding flags |
| `data/gold_standard.csv` | The 600-tweet evaluation set: tweet ID, manual gold label, the three model labels, transformer confidence, topic |
| `results/lda_topics.csv` | Top-10 keywords and interpretation of the five LDA topics (Table 13) |
| `results/coherence_sweep.csv` | c_v and c_npmi coherence for k = 3 to 10 (Figure 5) |
| `results/lda_stability.json` | LDA settings and topic-stability result |
| `notebooks/replication.ipynb` | Reproduces the paper's tables and figures from the files above |
| `figures/` | Figures 1 to 5 at 300 dpi |

### Column reference — `data/tweets_labels.csv`

| Column | Meaning |
|---|---|
| `tweet_id` | Tweet ID (19-digit string; read it as text, not as a number) |
| `created_at` | Posting time, UTC |
| `lang` | Language code reported by the platform |
| `textblob_polarity`, `textblob_sentiment` | TextBlob polarity and label (>0 positive, 0 neutral, <0 negative) |
| `vader_compound`, `vader_sentiment` | VADER compound score and label (≥0.05 positive, ≤−0.05 negative) |
| `roberta_sentiment`, `roberta_confidence` | Transformer label and maximum softmax probability; empty for the 8 tweets the model did not score |
| `topic` | Dominant LDA topic (0 to 4) |
| `is_organization` | 1 if the display name contains, as a whole word, one of 14 institutional keywords (see notebook section 6) |
| `is_geocoded` | 1 if the user's profile location was resolved to coordinates |

## What is not included, and why

Tweet text, usernames, profile descriptions and profile locations are **not** redistributed. The X (Twitter) developer terms allow sharing tweet IDs but not tweet content, and the profile fields are personal data. All derived labels needed to reproduce the results are included, keyed by tweet ID.

To rerun the text-dependent steps (LDA refit, sentiment re-scoring), rehydrate the IDs in `data/tweets_labels.csv` through the X API and save the result as `data/rehydrated_text.csv` with columns `tweet_id,text`. The notebook then runs sections 10 and 11 automatically. Tweets deleted since collection cannot be recovered, so refitted results may differ slightly from the paper.

## Reproducing the results

**Google Colab**

```python
!git clone https://github.com/Endriplasari/eagriculture-twitter-sentiment.git
%cd eagriculture-twitter-sentiment/notebooks
```

Then open and run `replication.ipynb`.

**Locally**

```bash
pip install -r requirements.txt
jupyter notebook notebooks/replication.ipynb
```

Sections 1 to 9 need only `pandas`, `numpy`, `scikit-learn`, `scipy`, `matplotlib` and `openpyxl`, and run in seconds. All tables are written to `results/replicated_tables.xlsx`.

## Notes on the evaluation

- One of the 600 evaluation tweets has no transformer output, so transformer-based metrics use n = 599. TextBlob and VADER use all 600.
- The majority-vote ensemble resolves three-way ties (14 tweets) in favour of the transformer, the study's primary classifier, so the ensemble result is identical on every run.
- McNemar's test is reported in its continuity-corrected chi-squared form, with the exact binomial p-value printed alongside.

## Licence

Code: MIT (see `LICENSE`). Data files in `data/` and `results/`: CC BY 4.0 (see `LICENSE-DATA`).

## Citation

If you use this package, please cite the paper and this archive:

> Boshnjaku, A., Plasari, E. and Fata, I. (2026) "Replication package for: Mapping Public Attitudes toward E-Agriculture in Global Twitter Discourse", Zenodo, Version 1.0.0. DOI [10.5281/zenodo.23532401](https://doi.org/10.5281/zenodo.23532401).
