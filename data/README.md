# data/

[Back to README](../README.md)

**The data files are not stored in this repository.** They are several GB, which is far over GitHub's file size limits, and they can be downloaded free at any time.

To get them, run notebook [01_download_and_check_structure.ipynb](../notebooks/01_download_and_check_structure.ipynb) in Google Colab. It downloads both files straight into Colab.

| File | What it holds |
|---|---|
| `Automotive.jsonl.gz` | Reviews: one row per review |
| `meta_Automotive.jsonl.gz` | Products: one row per product |

The [.gitignore](../.gitignore) file stops these and other big data files (`.jsonl`, `.gz`, `.parquet`, `.csv`) from being uploaded by accident.

Download links and details: [docs/02-dataset.md](../docs/02-dataset.md).
