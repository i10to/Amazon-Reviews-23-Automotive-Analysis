# notebooks/

[Back to README](../README.md)

Run the notebooks **in number order**, in Google Colab. Each one starts with a short explanation of what it does, and every code line has a plain-word comment.

| Notebook | What it does | Status |
|---|---|---|
| [01_download_and_check_structure.ipynb](01_download_and_check_structure.ipynb) | Downloads both files into Colab and shows their columns and types | Done |
| [02_build_tables_and_join.ipynb](02_build_tables_and_join.ipynb) | Builds DuckDB tables, drops unneeded columns, checks the join key, joins reviews to products, filters to Automotive | Done (one open item) |
| `03_eda.ipynb` | Data quality, distributions, and review timing per product | Next |
| `04_clean.ipynb` | Cleaning: types, text codes, missing values | Planned |
| `05_sentiment.ipynb` | Sentiment from early review text | Planned |
| `06_features_and_target.ipynb` | One row per product: inputs and the `poorly_rated` target | Planned |
| `07_models.ipynb` | Model training and comparison | Planned |
| `08_results.ipynb` | Results and interpretation | Planned |

**Important:** notebook 02 keeps its tables in memory, so after a session restart, run notebooks 01 and 02 again before continuing.

## How to open a notebook in Colab

1. Click the notebook file above.
2. Copy the page's web address.
3. Go to <https://colab.research.google.com>, choose **File → Open notebook → GitHub**, and paste the address.

Before you start, read [docs/04-colab-setup.md](../docs/04-colab-setup.md). It explains how to avoid running out of memory.
