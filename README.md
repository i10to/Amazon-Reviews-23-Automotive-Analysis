# Amazon Reviews 2023: Automotive Product Analysis

An end-to-end data analysis and machine learning project on Amazon's **Automotive** category (car parts, accessories and tools), using the public *Amazon Reviews 2023* dataset from McAuley Lab, UC San Diego.

## Research question

> **Can a product's listing details and its early customer reviews predict whether the product will end up poorly rated?**

In plain words: looking only at a product's page (price, store, category, how detailed the listing is) and its first 90 days of reviews, can we warn early that the product is heading for a poor rating?

An early-warning model like this would let a marketplace review risky listings, and a seller fix problems, before thousands of customers are affected.

---

## Key figures (so far)

| Item | Count |
|---|---|
| Reviews in the Automotive file | 19,955,450 |
| Products in the Automotive file | 2,003,129 |
| Products whose main category is Automotive | 1,717,837 (about 85.8%) |
| Reviews of Automotive products | 16,857,383 (about 84.5%) |
| Reviews with no main category (pending removal) | 185,376 |

---

## Start here

New to the project? Read in this order. Each file is short.

| Step | File | What you learn |
|---|---|---|
| 1 | [docs/01-project-plan.md](docs/01-project-plan.md) | The question, the variables used, how "poorly rated" is defined, and the pipeline |
| 2 | [docs/02-dataset.md](docs/02-dataset.md) | Where the data comes from, its size, how to download it, known quirks |
| 3 | [docs/03-data-dictionary.md](docs/03-data-dictionary.md) | Every column in every table, in plain words |
| 4 | [docs/04-colab-setup.md](docs/04-colab-setup.md) | How to run the notebooks in Google Colab without running out of memory |
| 5 | [notebooks/](notebooks/) | The code, one notebook per stage, run in number order |
| Any time | [docs/06-decisions.md](docs/06-decisions.md) | Every data decision and the reason behind it |
| Any time | [docs/05-glossary.md](docs/05-glossary.md) | Plain-word meanings of the technical terms |
| Any time | [docs/progress-log.md](docs/progress-log.md) | What has been done, found and decided, by date |

---

## Pipeline

```
 Raw files (.jsonl.gz)        DuckDB tables             Joined table              Model table
+----------------------+    +----------------+    +---------------------+    +-----------------------+
| Automotive (reviews) |--->| review         |--+ | review_meta         |    | one row per product   |
| meta_Automotive      |--->| meta           |--+>| (Automotive only)   |--->| inputs + poorly_rated |
+----------------------+    +----------------+    +---------------------+    +-----------------------+
      Notebook 01             Notebook 02             Notebook 02              Notebooks 03 to 06
                         (drop unneeded columns)  (join on parent_asin,
                                                   filter to Automotive)
```

---

## Project status

| Stage | Notebook | Status |
|---|---|---|
| 1. Download the data and check its structure | [01_download_and_check_structure.ipynb](notebooks/01_download_and_check_structure.ipynb) | Done |
| 2. Build tables, select columns, join, filter to Automotive | [02_build_tables_and_join.ipynb](notebooks/02_build_tables_and_join.ipynb) | Done (one open item: removing rows with no main category) |
| 3. Exploratory data analysis: data quality, distributions, review timing | `03_eda.ipynb` | Next |
| 4. Cleaning | `04_clean.ipynb` | Planned |
| 5. Sentiment from review text | `05_sentiment.ipynb` | Planned |
| 6. Product-level features and the `poorly_rated` target | `06_features_and_target.ipynb` | Planned |
| 7. Model training and comparison | `07_models.ipynb` | Planned |
| 8. Results and interpretation | `08_results.ipynb` | Planned |

Stages 3 onward may be adjusted as the work progresses; every change is recorded in the [progress log](docs/progress-log.md).

---

## Folder map

```
amazon-reviews-23-automotive-analysis/
├── README.md                  <- you are here
├── docs/                      <- explanations and decisions (no code)
│   ├── 01-project-plan.md
│   ├── 02-dataset.md
│   ├── 03-data-dictionary.md
│   ├── 04-colab-setup.md
│   ├── 05-glossary.md
│   ├── 06-decisions.md
│   └── progress-log.md
├── notebooks/                 <- the code, run in Google Colab, in number order
│   ├── README.md
│   ├── 01_download_and_check_structure.ipynb
│   └── 02_build_tables_and_join.ipynb
├── data/                      <- data is NOT stored here (too large); see data/README.md
│   └── README.md
└── .gitignore                 <- keeps the large data files out of the repository
```

---

## Tools

| Tool | Role |
|---|---|
| **Google Colab** | Free cloud notebooks: the multi-GB files never touch a local machine |
| **DuckDB** | Queries the large files and tables with SQL, without loading everything into memory |
| **pandas** | Works with small results and samples as tables |
| **Python** | Everything else: sentiment, features, models |

---

## Data source and citation

Amazon Reviews 2023, collected by McAuley Lab, UC San Diego. Official page: <https://amazon-reviews-2023.github.io>

> Hou, Y., Li, J., He, Z., Yan, A., Chen, X., and McAuley, J. (2024). *Bridging Language and Items for Retrieval and Recommendation.* arXiv preprint arXiv:2403.03952.

The data files are not stored in this repository. They are downloaded fresh in Colab each session (see [data/README.md](data/README.md)).
