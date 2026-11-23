# 02: The dataset

[Back to README](../README.md)

## What it is

**Amazon Reviews 2023** is a large public collection of Amazon product reviews and product details, gathered in 2023 by **McAuley Lab** at UC San Diego. It covers reviews from **May 1996 to September 2023**, split into 33 product categories.

This project uses one category: **Automotive**.

- Official page: <https://amazon-reviews-2023.github.io>
- Paper to cite: Hou, Y., Li, J., He, Z., Yan, A., Chen, X., and McAuley, J. (2024). *Bridging Language and Items for Retrieval and Recommendation.* arXiv preprint arXiv:2403.03952.

## Size of the Automotive category

From the official statistics table (rounded):

| Users (reviewers) | Items (products) | Ratings (reviews) |
|---|---|---|
| 8.0 million | 2.0 million | 20.0 million |

Counted in notebook 02: **19,955,450 reviews** and **2,003,129 products** (2,003,039 of which have at least one review).

That is far too big to load into memory at once. See [04-colab-setup.md](04-colab-setup.md) for how the project handles it.

## The two files

| File | One row is | Main use |
|---|---|---|
| `Automotive.jsonl.gz` (**review**) | one customer review | ratings, review text, who reviewed, when |
| `meta_Automotive.jsonl.gz` (**meta**) | one product | price, store, category, description |

They link through **`parent_asin`**, the product ID. Every column is explained in [03-data-dictionary.md](03-data-dictionary.md).

## File format: what `.jsonl.gz` means

- **`.jsonl`** (JSON Lines): a text file where **each line is one record**. For example, one line = one review written as `{"rating": 5.0, "title": "Great fit", ...}`.
- **`.gz`**: the file is zipped (compressed) to save space. DuckDB and pandas can read it **without unzipping**.

## Where to download, and why the sizes differ

| Source | Format | Review file | Meta file |
|---|---|---|---|
| McAuley Lab (used in this project) | zipped `.jsonl.gz` | smaller | smaller |
| Hugging Face mirror | unzipped `.jsonl` | 8.73 GB | 5.35 GB |

It is the **same data**. Hugging Face just stores it unzipped, so it looks bigger.

Download links used in notebook 01 (as listed on the official page in November 2026):

- Review: `https://mcauleylab.ucsd.edu/public_datasets/data/amazon_2023/raw/review_categories/Automotive.jsonl.gz`
- Meta: `https://mcauleylab.ucsd.edu/public_datasets/data/amazon_2023/raw/meta_categories/meta_Automotive.jsonl.gz`

These are file downloads, not web pages. Clicking them in a browser starts a big download. Run them in Colab instead (notebook 01).

**If a link stops working:** an older version of these links pointed to `datarepo.eng.ucsd.edu` and no longer works. Check the official page for the current link. As a backup, the unzipped files are on Hugging Face:
- <https://huggingface.co/datasets/McAuley-Lab/Amazon-Reviews-2023/tree/main/raw/review_categories>
- <https://huggingface.co/datasets/McAuley-Lab/Amazon-Reviews-2023/tree/main/raw/meta_categories>

## Known quirks (things the official page doesn't tell you)

Found while working with the files. Each one matters for cleaning.

1. **`timestamp` is in milliseconds**, not seconds. Divide by 1000 before turning it into a date, or every date will be wrong.
2. **`price` is often missing.** The docs list it as a number, but many products have no price.
3. **Fake blanks.** Some empty values are stored as `"None"`, `""` or `[]` instead of a true blank. pandas `info()` does not count these as missing.
4. **Two undocumented columns in the meta file: `subtitle` and `author`.** These are fields from Amazon **book** pages (for example the book format and its author). For normal car parts they are empty. Where they are filled, the "product" is probably a book or repair manual sold in the Automotive category. Both columns were dropped in notebook 02 without counting the filled rows, so a small number of books or manuals may remain.
5. **Lists and dictionaries inside one cell.** `features`, `description`, `categories`, `images` and `details` each pack several values into one cell. They need flattening before analysis.
6. **Some reviews may have no matching product** in the meta file. They are part of the 185,376 joined rows with no main category (notebook 02); an exact count is still open.
7. **Not every product in the Automotive file is automotive.** `main_category` has 38 categories plus blanks. About 85.8% of products (1,717,837) and about 84.5% of reviews (16,857,383) are Automotive; the rest include Industrial & Scientific, AMAZON FASHION and Sports & Outdoors.
8. **`store` uses `"Unknown"`** as a fake blank.
9. **Web codes in review text**, like `&#34;` (which means `"`). They need converting back to normal characters before text analysis.
10. **The meta file has lines DuckDB can't read by default.** It was loaded with `ignore_errors = true`, which skips unreadable lines silently. The loaded product count is 2,003,129.

## Why this dataset fits the project brief

- **Public:** free to download.
- **Not cleaned:** has missing values, fake blanks, undocumented columns and packed lists (see the quirks above).
- **At least 50,000 records:** about 20 million reviews and 2 million products.
- **Domain:** product and customer data.
