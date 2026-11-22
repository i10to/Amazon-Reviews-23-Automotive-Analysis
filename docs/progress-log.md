# Progress log

[Back to README](../README.md)

Newest entries at the top. Each entry says what was done, what was decided, and what is next.

---

## 2026-11-22 (afternoon): First EDA checks and column decisions

**Done** (notebook 02)
- Loaded both files into DuckDB tables, `review` and `meta`.
- Row counts: **19,955,450 reviews** and **2,003,129 products**.
- Join-key check: `parent_asin` in meta has 2,003,129 distinct values, equal to the row count. Every product appears once, and none is missing its ID.
- Counted distinct `helpful_vote` values: 741.
- Counted products per `main_category`: 38 categories plus NULL. Automotive has 1,717,837 products (about 85.8%), and NULL has 34,116 (about 1.7%).

**Decided** (full list with reasons: [06-decisions.md](06-decisions.md))
- Keep only products with `main_category = 'Automotive'`; exclude NULL.
- Drop `asin`, `images`, `helpful_vote` (review) and `details`, `images`, `videos`, `average_rating`, `rating_number`, `bought_together` (meta).
- Build a new binary target, `poorly_rated`: inputs from early reviews, target from later reviews.

**Found**
- `store` uses the text `"Unknown"` as a fake blank.
- Review text contains web codes like `&#34;` (which means `"`). They need converting before sentiment analysis.
- `price` is missing for many products.

**Not applied yet**
- The column drops and the Automotive filter are decided but not yet run in the notebook.

**Next**
- Check `subtitle` and `author` (count filled rows), then apply the filter and the drops.
- Join checks 2 and 3: reviews with no matching product, and the row count after a left join.
- Look at how many reviews products have, and how fast they arrive, to choose the early window and minimums for the target.

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
