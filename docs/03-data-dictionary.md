# 03: Data dictionary

[Back to README](../README.md)

Every column in both files, in plain words, plus the joined table built in notebook 02. The **Type** column shows what DuckDB detected in notebook 01. Columns marked **(dropped)** were removed in notebook 02; reasons are in [06-decisions.md](06-decisions.md).

Quick guide to the types:
- **DOUBLE**: a number that can have decimals (e.g. 4.5)
- **BIGINT**: a whole number, possibly a very big one (e.g. 1588687728923)
- **VARCHAR**: text
- **BOOLEAN**: True or False
- **VARCHAR[]**: a list of texts packed into one cell
- **STRUCT(...)[]**: a list of small records packed into one cell (each record has its own named parts)
- **MAP / JSON**: a set of name-and-value pairs packed into one cell

---

## Review file: `Automotive.jsonl.gz`

One row = one review. 10 columns.

| Column | Type | Meaning | Notes |
|---|---|---|---|
| `rating` | DOUBLE | Stars the reviewer gave, from 1.0 to 5.0 | Source of the "poorly rated" answer. Expect most reviews to be 4 or 5 stars. |
| `title` | VARCHAR | The review's headline | |
| `text` | VARCHAR | The review's main text | Used to make the `sentiment` column |
| `images` (dropped) | STRUCT[] | Photos the reviewer posted, each in small, medium and large sizes | Heavy; usually not loaded |
| `asin` (dropped) | VARCHAR | ID of the exact product variant (e.g. one colour or size) | |
| `parent_asin` | VARCHAR | ID of the overall product. Variants of the same product share it. | **Use this to join to the meta file.** |
| `user_id` | VARCHAR | ID of the reviewer | |
| `timestamp` | BIGINT | When the review was posted, as milliseconds since 1 Jan 1970 | Divide by 1000 before converting to a date. Needed to split early vs later reviews. |
| `helpful_vote` (dropped) | BIGINT | How many people marked the review as helpful | Most are 0, a few are very large |
| `verified_purchase` | BOOLEAN | True if Amazon confirmed the reviewer bought the product | |

---

## Meta file: `meta_Automotive.jsonl.gz`

One row = one product. 16 columns.

| Column | Type | Meaning | Notes |
|---|---|---|---|
| `main_category` | VARCHAR | The product's main category (domain) | |
| `title` | VARCHAR | The product's name | Same column name as in the review file, but a different meaning. Rename one before joining. |
| `average_rating` (dropped) | DOUBLE | Star rating shown on the product page | **Never use as a model input.** It is the answer in disguise (data leakage). |
| `rating_number` (dropped) | BIGINT | How many ratings the product has | Also counted over the full period: don't use as an input |
| `features` | VARCHAR[] | Bullet-point features from the product page | List in one cell |
| `description` | VARCHAR[] | The product description | List in one cell |
| `price` | DOUBLE | Price in US dollars when the data was collected | Often missing |
| `images` (dropped) | STRUCT[] | Product photos in different sizes | Heavy; usually not loaded |
| `videos` (dropped) | STRUCT[] | Product videos, each with a title and link | |
| `store` | VARCHAR | Name of the store (seller or brand) | Check for spelling variants of the same store |
| `categories` | VARCHAR[] | Category path, from broad to narrow | List in one cell |
| `details` (dropped) | MAP | Extra details, e.g. brand, materials, sizes | Name-and-value pairs in one cell |
| `parent_asin` | VARCHAR | ID of the product | **Use this to join to the review file.** |
| `bought_together` | JSON | Bundles the website recommends | |
| `subtitle` (dropped) | VARCHAR | **Not in the official docs.** A book-page field (e.g. "Hardcover"). | Expected to be mostly empty. See quirk 4 in [02-dataset.md](02-dataset.md). |
| `author` (dropped) | JSON | **Not in the official docs.** A book's author. | Expected to be mostly empty. Filled rows are probably books or manuals. |

---

## Joined table: `review_meta` (notebook 02)

One row = one review, with its product's details attached. Built with a left join on `parent_asin`, then filtered to Automotive products.

| Column | From | Meaning |
|---|---|---|
| `parent_asin` | review | Product ID (the join key) |
| `rating` | review | Stars the reviewer gave |
| `title` | review | The review's headline |
| `text` | review | The review's main text |
| `user_id` | review | Reviewer ID (for the duplicate check, then dropped) |
| `timestamp` | review | When the review was posted, in milliseconds since 1 Jan 1970 |
| `verified_purchase` | review | True if Amazon confirmed the purchase |
| `main_category` | meta | Product's main category (to be dropped after filtering) |
| `product_title` | meta | Product name (renamed from `title` to avoid the clash) |
| `price` | meta | Price in US dollars |
| `store` | meta | Store or brand name |
| `categories` | meta | Category path, broad to narrow |
| `features` | meta | Bullet-point features (list in one cell) |
| `description` | meta | Product description (list in one cell) |
| `bought_together` | meta | Recommended bundles (to be dropped) |

Rows: 19,955,450 after the join; 16,857,383 Automotive reviews plus 185,376 rows with no main category after the Automotive filter.

## How the two files connect

```
review file                         meta file
(one row per review)                (one row per product)

parent_asin = B0BV88374L  ───────►  parent_asin = B0BV88374L
parent_asin = B0BV88374L  ───────►  (same product: one product has many reviews)
parent_asin = B0B2WGS5ND  ───────►  parent_asin = B0B2WGS5ND
```

Many reviews point to one product. This is called a **many-to-one** join.
