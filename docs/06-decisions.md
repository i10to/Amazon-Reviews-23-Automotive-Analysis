# 06: Decisions and reasons

[Back to README](../README.md)

Every choice that changes the data, with the reason behind it. This is the place to look when someone asks "why did you drop that?".

Status: **Decided** = agreed; **Planned** = agreed but not yet applied in the notebooks; **Open** = still to decide.

---

## Columns: review file

| Column | Decision | Reason | Status |
|---|---|---|---|
| `rating` | Keep | Needed to build the target and to label sentiment | Decided |
| `title` | Keep | Short, strong sentiment signal ("Broke after a week") | Decided |
| `text` | Keep | Main input for sentiment | Decided |
| `parent_asin` | Keep | Join key to the meta file | Decided |
| `timestamp` | Keep | Splits early reviews (inputs) from later reviews (target) | Decided |
| `verified_purchase` | Keep | Feature: share of verified buyers among early reviews | Decided |
| `user_id` | Keep for now | Needed to check duplicate reviews (same user, same product); may drop after | Decided |
| `asin` | Drop | Redundant identifier. It marks a variant (colour, size); the research works at product level, and the meta file is keyed only by `parent_asin`. | Planned |
| `images` | Drop | Heavy, and photos are not part of the research question | Planned |
| `helpful_vote` | Drop | **Data leakage.** Votes pile up after a review is posted, up to the 2023 collection date, so they carry information from the future. | Planned |

## Columns: meta file

| Column | Decision | Reason | Status |
|---|---|---|---|
| `parent_asin` | Keep | Join key | Decided |
| `price` | Keep | Feature (often missing; check how much) | Decided |
| `store` | Keep | Feature. Treat `"Unknown"` as missing. | Decided |
| `categories` | Keep | Gives the subcategory, e.g. Tires & Wheels | Decided |
| `features`, `description` | Keep, then turn into numbers | e.g. number of bullet points, description length | Decided |
| `title` | Keep | Product name: length or keywords as features | Decided |
| `main_category` | Use to filter rows, then drop | After filtering, every row says "Automotive", so it adds nothing | Planned |
| `details` | Drop | Its useful part (brand or manufacturer) largely duplicates `store`; the other keys differ between products and would take heavy work to extract | Planned |
| `images`, `videos` | Drop | Heavy, not part of the research question | Planned |
| `average_rating`, `rating_number` | Drop | **Data leakage.** They summarise all reviews, including the later ones the target comes from. | Planned |
| `bought_together` | Drop | Not related to the research question; looks mostly empty | Planned |
| `subtitle`, `author` | Check first, then drop | Undocumented book-page fields. Count filled rows first: they may flag books or manuals. | Open |

## Rows

| Decision | Reason | Status |
|---|---|---|
| Keep only products with `main_category = 'Automotive'` (1,717,837 of 2,003,129, about 85.8%) | The research is about automotive products; Amazon's own main category defines the scope consistently | Planned |
| Exclude products with no main category (NULL: 34,116, about 1.7%) | Small share. Rescuing them through their `categories` list was considered and judged unnecessary. | Planned |
| Report the share of **reviews** kept, not just products | Reviews are not spread evenly across products, so the review share can differ from 85.8% | Planned |

## Join

| Check | Result | Status |
|---|---|---|
| `parent_asin` is unique in meta | Passed: 2,003,129 rows = 2,003,129 distinct values, so no duplicates and no blanks | Done |
| Share of reviews with no matching product | Not run yet | Open |
| Row count after a left join equals the review row count (19,955,450) | Not run yet | Open |

## Target

| Decision | Detail | Status |
|---|---|---|
| Create a binary target, `poorly_rated` | No existing column fits, so it is built per product | Decided |
| Inputs from **early** reviews, target from **later** reviews | Prevents data leakage | Decided |
| Early window | Time window (e.g. first 30 or 90 days) or review count (e.g. first 5 or 10) | Open |
| Cut-off for "poorly rated" | e.g. later average rating below 3.5 | Open |
| Minimum reviews per product | e.g. at least 5 early and 10 later | Open |
