# 06: Decisions and reasons

[Back to README](../README.md)

Every choice that changes the data, with the reason behind it. This is the reference for "why was this dropped?" questions.

**Status key:** **Applied** = done in a notebook. **Decided** = agreed, not yet applied. **Proposed** = suggested, to be confirmed with data. **Open** = still to decide.

---

## 1. Columns: review table

| Column | Decision | Reason | Status |
|---|---|---|---|
| `rating` | Keep | Builds the target (later reviews) and an input (early reviews) | Applied |
| `title` | Keep | Short, strong sentiment signal ("Broke after a week") | Applied |
| `text` | Keep | Main input for sentiment | Applied |
| `parent_asin` | Keep | Join key to the meta table | Applied |
| `timestamp` | Keep | Splits early reviews (inputs) from later reviews (target) | Applied |
| `verified_purchase` | Keep | Input: share of verified buyers among early reviews | Applied |
| `user_id` | Keep for now | Needed for the duplicate-review check; dropped afterwards | Applied |
| `asin` | Drop | Redundant identifier. It marks a variant (colour, size); the research works at product level, and the meta table is keyed only by `parent_asin`. | Applied (notebook 02) |
| `images` | Drop | Heavy; photos are outside the research question | Applied (notebook 02) |
| `helpful_vote` | Drop | **Data leakage.** Votes accumulate after a review is posted, up to the 2023 collection date, so they carry information from the future. | Applied (notebook 02) |

## 2. Columns: meta table

| Column | Decision | Reason | Status |
|---|---|---|---|
| `parent_asin` | Keep | Join key | Applied |
| `price` | Keep | Input (often missing; extent checked in EDA) | Applied |
| `store` | Keep | Input. `"Unknown"` is treated as missing. | Applied |
| `categories` | Keep | Input: the subcategory, e.g. Tires & Wheels | Applied |
| `features`, `description` | Keep, then convert to numbers | e.g. number of bullet points, description length | Applied |
| `title` | Keep (as `product_title` after the join) | Input: name length or keywords | Applied |
| `main_category` | Keep until filtering, then drop | Used to filter to Automotive; afterwards every row has the same value, so it carries no information | Filter applied; drop pending |
| `average_rating`, `rating_number` | Drop | **Data leakage.** Both summarise all reviews, including the later ones the target is built from. | Applied (notebook 02) |
| `details` | Drop | Its useful content (brand or manufacturer) largely duplicates `store`; the other keys vary between products and would need heavy extraction | Applied (notebook 02) |
| `images`, `videos` | Drop | Heavy; outside the research question | Applied (notebook 02) |
| `subtitle`, `author` | Drop | Undocumented book-page fields, not relevant to the question. Dropped without counting the filled rows first; a few books or manuals may remain among the products. | Applied (notebook 02) |
| `bought_together` | Drop | Not related to the research question | Decided (still in `review_meta`) |

## 3. Join key checks

| Check | Result | Status |
|---|---|---|
| `parent_asin` missing in meta | 0 rows | Applied |
| `parent_asin` duplicated in meta | None: 2,003,129 distinct values = 2,003,129 rows | Applied |
| `parent_asin` missing in review | 0 rows | Applied |
| Distinct `parent_asin` in review | 2,003,039 (many reviews per product, as expected) | Applied |
| Row count after the left join | 19,955,450 = review row count, so no review was duplicated by the join | Applied |
| Reviews whose product is not in meta | Not counted separately yet; they are part of the 185,376 rows with no main category | Open |

**Note on the row-count check:** equal row counts prove that no review was copied (no duplicate products in meta). They do **not** prove that every review found its product, because a left join also keeps reviews with no match.

## 4. Rows

| Decision | Reason | Status |
|---|---|---|
| Join every review to its product with a **left join** into a new table, `review_meta` | One table with review and product columns; a left join keeps every review so unmatched ones can be counted | Applied (notebook 02) |
| Keep only rows whose `main_category` is **Automotive** (16,857,383 reviews kept; 2,912,691 reviews of 38 other categories removed) | The research is about automotive products; Amazon's own main category defines the scope consistently | Applied (notebook 02) |
| Remove rows with **no main category** (185,376 reviews) | They mix two cases: reviews whose product is not in meta (no product details at all) and products with no category label. Rescuing the second group was considered and judged not worth it, as it is about 1.1% of the remaining reviews. | Decided (recommended), pending |

## 5. Target: "poorly rated"

| Decision | Detail | Status |
|---|---|---|
| Create a binary target, `poorly_rated` | No existing column fits, so it is built per product | Decided |
| Inputs from **early** reviews only, target from **later** reviews only | Prevents data leakage | Decided |
| Measure and cut-off | Later average rating below 3.5 stars = 1 | Proposed |
| Early window | First 90 days after the product's first review | Proposed |
| Minimum reviews per product | At least 5 early and 10 later | Proposed |
| Robustness check | Repeat with cut-offs 3.0 and 4.0 | Proposed |

The proposed values will be confirmed in the EDA, against how many products survive the minimums and what share end up as 1. Full reasoning is in [01-project-plan.md](01-project-plan.md#defining-poorly-rated-proposed).
