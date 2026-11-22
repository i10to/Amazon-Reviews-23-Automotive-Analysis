# Amazon Reviews 2023: Automotive Analysis

A data analysis and machine learning project on Amazon product reviews in the **Automotive** category (car parts, accessories, tools and so on).

**Research question (draft):**

> Can a product's listing details and its **early** customer reviews predict whether the product will end up **poorly rated**?

In plain words: if we look at a new product's page (price, store, category, description) and the first few weeks of reviews, can we warn early that this product is going to end up with bad ratings?

---

## Start here

New to this project? Read the files in this order. Each one is short.

| Step | File | What you learn |
|---|---|---|
| 1 | [docs/01-project-plan.md](docs/01-project-plan.md) | The question, why it matters, and the plan from raw data to model |
| 2 | [docs/02-dataset.md](docs/02-dataset.md) | Where the data comes from, how big it is, and how to download it |
| 3 | [docs/03-data-dictionary.md](docs/03-data-dictionary.md) | Every column in both files, explained in plain words |
| 4 | [docs/04-colab-setup.md](docs/04-colab-setup.md) | How to run the notebooks in Google Colab without crashing |
| 5 | [notebooks/](notebooks/) | The actual code, one notebook per stage |
| Any time | [docs/05-glossary.md](docs/05-glossary.md) | Plain-word meanings of the technical terms used here |
| Any time | [docs/progress-log.md](docs/progress-log.md) | What has been done so far, and what is next |

---

## Folder map

```
amazon-reviews-23-automotive-analysis/
├── README.md                  <- you are here: the front door
├── docs/                      <- explanations, no code
│   ├── 01-project-plan.md
│   ├── 02-dataset.md
│   ├── 03-data-dictionary.md
│   ├── 04-colab-setup.md
│   ├── 05-glossary.md
│   └── progress-log.md
├── notebooks/                 <- the code, run in Google Colab, in number order
│   ├── README.md
│   └── 01_download_and_check_structure.ipynb
├── data/                      <- data is NOT stored here (too big); see data/README.md
│   └── README.md
└── .gitignore                 <- tells Git to never upload the big data files
```

---

## Project status

| Stage | Notebook | Status |
|---|---|---|
| 1. Download the data and check its structure | `01_download_and_check_structure.ipynb` | Done |
| 2. Explore the data (blanks, duplicates, odd values) | `02_explore.ipynb` | Next |
| 3. Clean the data | `03_clean.ipynb` | Planned |
| 4. Add a sentiment column from review text | `04_sentiment.ipynb` | Planned |
| 5. Join reviews to products and build features | `05_features.ipynb` | Planned |
| 6. Train and compare ML models | `06_models.ipynb` | Planned |
| 7. Results and write-up | `07_results.ipynb` | Planned |

The stages after 1 may change as the project goes. The [progress log](docs/progress-log.md) records every change.

---

## Tools used

- **Google Colab**: free cloud notebooks, so the big files never touch a laptop
- **DuckDB**: reads the huge data files straight from disk, without loading them all into memory
- **pandas**: works with the smaller samples as tables
- **Python**: everything else

---

## Data source

Amazon Reviews 2023, collected by McAuley Lab (UC San Diego). Official page: <https://amazon-reviews-2023.github.io>

Full details, sizes and download links are in [docs/02-dataset.md](docs/02-dataset.md).

The data files are **not** in this repository. They are several GB and are downloaded fresh inside Colab each session.
