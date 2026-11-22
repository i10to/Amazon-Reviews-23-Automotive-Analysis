# notebooks/

[Back to README](../README.md)

Run the notebooks **in number order**, in Google Colab. Each one starts with a short explanation of what it does, and every code line has a plain-word comment.

| Notebook | What it does | Status |
|---|---|---|
| [01_download_and_check_structure.ipynb](01_download_and_check_structure.ipynb) | Downloads both files into Colab and shows their columns and types | Done |
| `02_explore.ipynb` | Checks blanks, fake blanks, duplicates, odd values and dates | Next |
| `03_clean.ipynb` | Cleans the data | Planned |
| `04_sentiment.ipynb` | Adds the `sentiment` column | Planned |
| `05_features.ipynb` | Joins the files and builds product-level features | Planned |
| `06_models.ipynb` | Trains and compares models | Planned |
| `07_results.ipynb` | Results and explanation | Planned |

## How to open a notebook in Colab

1. Click the notebook file above.
2. Copy the page's web address.
3. Go to <https://colab.research.google.com>, choose **File → Open notebook → GitHub**, and paste the address.

Before you start, read [docs/04-colab-setup.md](../docs/04-colab-setup.md). It explains how to avoid running out of memory.
