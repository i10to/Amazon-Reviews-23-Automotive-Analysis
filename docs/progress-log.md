# Progress log

[Back to README](../README.md)

Newest entries at the top. Each entry says what was done, what was decided, and what is next.

---

## 2026-11-22: Research question chosen, repository set up

**Done**
- Chose the research question: an **early-warning model** that predicts whether a product will end up poorly rated, using its listing details (meta file) and its early reviews (review file).
- Set up this repository with documentation.
- Notebook 01 checked the structure of both files with DuckDB: 10 columns in the review file, 16 in the meta file.

**Found**
- The meta file has two columns that the official docs don't mention: `subtitle` and `author`. They are probably book-page fields. To be checked in notebook 02.

**Decided**
- Sentiment will be **an input** (feature) for the model, not the model's answer.
- `average_rating` and `rating_number` from the meta file will **not** be used as inputs (data leakage).

**Next**
- Notebook 02: explore blanks, fake blanks, duplicates, value ranges, the date range, and how many reviews match a product.

---

## 2026-11-21: Dataset chosen

**Done**
- Chose Amazon Reviews 2023, Automotive category, after comparing product datasets.
- Moved work to Google Colab because the files are too big for a laptop.
- Downloaded both files into Colab.

**Found**
- The old download links (`datarepo.eng.ucsd.edu`) no longer work; the current ones are on `mcauleylab.ucsd.edu`.
- Loading the full review file into pandas crashes Colab (out of RAM). Switched to DuckDB.
