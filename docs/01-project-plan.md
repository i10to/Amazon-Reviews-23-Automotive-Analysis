# 01: Project plan

[Back to README](../README.md)

## The question

> **Can a product's listing details and its early customer reviews predict whether the product will end up poorly rated?**

## Why it matters

New products are listed on Amazon every day. If a product is going to disappoint customers, it is far better to know early, before thousands of people buy it and leave poor reviews. An **early-warning model** could help:

- a **marketplace** flag risky listings for review
- a **seller** find and fix problems before the product builds a poor reputation
- a **shopper** avoid products that are likely to disappoint

## Why both files are needed

| File | Contributes | Examples |
|---|---|---|
| **review** | What customers say, and when | Early average rating, share of negative early reviews, share of verified buyers |
| **meta** | What the product is | Price, store, subcategory, how detailed the listing is |

The two files link through **`parent_asin`**, the product ID.

---

## Variables used

This is the complete list of columns the project needs. Everything else has been dropped (reasons in [06-decisions.md](06-decisions.md)).

### From the review table

| Column | Role |
|---|---|
| `parent_asin` | Links each review to its product |
| `timestamp` | Splits each product's reviews into **early** (first 90 days) and **later** (after that) |
| `rating` | **Later** reviews: build the answer (poorly rated or not). **Early** reviews: early average rating, as an input. |
| `text`, `title` | **Early** reviews only: sentiment (positive / neutral / negative), as an input |
| `verified_purchase` | **Early** reviews only: share of buyers Amazon confirmed, as an input |
| `user_id` | Used only to check for duplicate reviews during cleaning, then dropped |

### From the meta table

| Column | Role |
|---|---|
| `parent_asin` | Links the product to its reviews |
| `price` | Input |
| `store` | Input (e.g. established brand vs unknown seller) |
| `categories` | Input: the subcategory (e.g. Tires & Wheels) |
| `features` | Input, turned into a number: how many bullet points |
| `description` | Input, turned into a number: description length |
| `title` | Input, turned into a number: product name length |

### What the model sees: one row per product

| parent_asin | price | store | n_features | early_avg_rating | early_negative_share | early_verified_share | **poorly_rated** |
|---|---|---|---|---|---|---|---|
| P1 | 12.99 | Caltric | 5 | 4.6 | 0.05 | 0.90 | **0** |
| P2 | 39.99 | Unknown | 0 | 2.8 | 0.40 | 0.60 | **1** |

*(Illustrative values.)* Every column left of `poorly_rated` is an **input**: listing details plus early reviews. `poorly_rated` is the **answer**, built from later reviews only.

---

## Defining "poorly rated" (proposed)

No column in the data says whether a product is good or bad, so the answer has to be **created**. The proposed definition:

> A product is **poorly rated (1)** if the average rating of its reviews **after its first 90 days** is **below 3.5 stars**; otherwise it is **0**.
> Only products with **at least 5 reviews in the first 90 days** and **at least 10 reviews after** are included.

| Choice | Proposed value | Why |
|---|---|---|
| Measure | Average rating of later reviews | Easy to explain; matches how shoppers read star ratings |
| Cut-off | Below 3.5 stars | An absolute standard, unlike "worst 20% of products" |
| Early window | First 90 days after the product's first review | Fits the early-warning idea: "three months in, should we flag this product?" |
| Minimum reviews | 5 early, 10 later | Prevents labels and inputs from resting on one or two opinions |

**Status: proposed, not final.** These values will be checked against the data in the EDA (how many products survive the minimums, and what share end up as 1) before being fixed. The cut-offs 3.0 and 4.0 will be tested as a robustness check.

Alternatives considered: share of 1 to 2 star later reviews above a set level (catches mixed products, but the level is harder to justify); worst 20% of products (controls balance, but "poor" becomes relative); a fixed number of early reviews instead of 90 days (equal information per product, but not truly "early" for slow sellers).

---

## Pipeline

1. **Download and check structure.** Both files into Colab; inspect columns and types. *(Done: notebook 01.)*
2. **Build tables, select columns, join, filter.** DuckDB tables, drop unneeded columns, verify the join key, join reviews to products, keep Automotive only. *(Done: notebook 02.)*
3. **EDA.** Data quality (blanks, fake blanks, duplicates, value ranges, dates), distributions (ratings, prices, stores, subcategories), and review timing per product, which fixes the target's window and minimums.
4. **Clean.** Fix types (timestamps to dates), decode web codes in text (e.g. `&#34;`), handle missing values.
5. **Sentiment.** Label each early review as positive, neutral or negative from its text and title.
6. **Features and target.** Summarise early reviews per product, add listing features, build `poorly_rated` from later reviews, and join everything into one row per product.
7. **Models.** Classification models (e.g. Logistic Regression, Random Forest, LightGBM), evaluated with F1 and similar metrics suited to imbalanced classes.
8. **Results.** Which inputs matter most, where the model fails, and what it means for a marketplace.

---

## Data leakage: the main risk

**Data leakage** means the model is accidentally shown the answer while learning. It scores well in testing and fails in real use. This project guards against it in three ways:

1. **Time split.** Inputs come only from early reviews; the answer comes only from later reviews.
2. **No future-facing columns as inputs.** `average_rating` and `rating_number` (summaries over all reviews) and `helpful_vote` (votes accumulate long after a review is posted) are dropped.
3. **Product-level split for testing.** A product's rows never appear in both the training and the test data.
