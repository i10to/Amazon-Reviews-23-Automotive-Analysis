# Progress log

[Back to README](../README.md)

Newest entries at the top. Each entry says what was done, what was decided, and what is next.

---

## 2026-11-23: Tables, column selection, join and Automotive filter

**Done** (notebook [02_build_tables_and_join.ipynb](../notebooks/02_build_tables_and_join.ipynb), rebuilt from the actual Colab notebook)
- Built DuckDB tables `review` (19,955,450 rows) and `meta` (2,003,129 rows). Meta was loaded with `ignore_errors = true`.
- Dropped 3 columns from review (`images`, `asin`, `helpful_vote`) and 7 from meta (`average_rating`, `rating_number`, `images`, `videos`, `details`, `subtitle`, `author`).
- Verified the join key: no missing `parent_asin` in either table, no duplicates in meta. Review covers 2,003,039 distinct products.
- Joined every review to its product with a left join into `review_meta`: 19,955,450 rows, the same as the review table, so no review was duplicated.
- Filtered to Automotive: kept 16,857,383 reviews (about 84.5%) and removed 2,912,691 reviews of 38 other categories.

**Decided**
- The full list of variables the model needs, and the final one-row-per-product shape: see [01-project-plan.md](01-project-plan.md#variables-used).
- Proposed definition of "poorly rated": later (after 90 days) average rating below 3.5, with at least 5 early and 10 later reviews. To be confirmed in the EDA.

**Open**
- 185,376 rows with no main category remain in `review_meta`. Removal is recommended; it mixes reviews whose product isn't in meta with products lacking a category label.
- `main_category` and `bought_together` are still in `review_meta`, to be dropped after the step above.

**Changed in the repository**
- Notebook `02_explore_part1.ipynb` (reconstructed from screenshots) was replaced by `02_build_tables_and_join.ipynb`, based on the actual Colab notebook.
- The stage numbering in the README and the notebook index was updated (8 stages).
- Added the variable list, the proposed target definition and leakage safeguards to the project plan; updated the decisions log, data dictionary (new `review_meta` table), dataset quirks, Colab troubleshooting and glossary.

**Next**
- Remove the rows with no main category, then start notebook 03 (EDA): data quality, distributions, and review timing per product.

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
