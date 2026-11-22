# 01: Project plan

[Back to README](../README.md)

## The question

> Can a product's listing details and its **early** customer reviews predict whether the product will end up **poorly rated**?

## Why it matters

A marketplace like Amazon gets new products every day. If a product is going to disappoint customers, it is better to know early: before thousands of people buy it and leave bad reviews.

An **early-warning model** could help:
- a **marketplace** spot risky listings and check them
- a **seller** find problems with a product before it builds a bad reputation
- a **shopper** avoid products that are likely to disappoint

## Why both files are needed

The dataset comes in two files (see [02-dataset.md](02-dataset.md)):

| File | Gives us | Example inputs for the model |
|---|---|---|
| **review** | What customers say and when | Share of negative early reviews, share of verified buyers, how fast reviews arrive |
| **meta** | What the product is | Price, store, category, number of features listed, length of description, has images or videos |

The two files link through the column **`parent_asin`** (the product ID). Neither file alone tells the full story, which is why the project joins them.

## The plan, step by step

1. **Download and check structure.** Get both files into Colab and look at their columns and types. *(Done: notebook 01.)*
2. **Explore.** Check blanks (including fake blanks like `"None"` or `[]`), duplicates, odd values, date range, and how many reviews match a product.
3. **Clean.** Fix types (e.g. timestamps), handle blanks, and decide what to drop (for example books or manuals mixed into the Automotive category).
4. **Add sentiment.** Label each review positive, neutral or negative from its text. This becomes a new column called `sentiment`. It is an input for the model, not the model's answer.
5. **Build product-level features.** Join reviews to products on `parent_asin`, then summarise each product's **early** reviews.
6. **Train and compare models.** Classification models (for example Logistic Regression, Random Forest, LightGBM), judged on scores that work with uneven classes, like F1.
7. **Explain results.** Which inputs matter most, where the model fails, and what that means for a marketplace.

## The big trap: data leakage

**Data leakage** means the model is accidentally shown the answer while learning. It then scores very well in testing and fails in real use.

This project has two easy ways to leak:

1. **Using the same reviews for the inputs and the answer.** If "poorly rated" is decided from a product's reviews, and the inputs also use those same reviews, the model is partly reading the answer.
   **Fix:** split by time. Inputs come from **early** reviews only (for example the first few weeks). The answer comes from **later** reviews.
2. **Using `average_rating` from the meta file as an input.** That column *is* the product's rating, so it is the answer in disguise.
   **Fix:** never use `average_rating` (or `rating_number`, which is counted over the same full period) as an input.

## Decisions still open

These are not decided yet. Each will be recorded in the [progress log](progress-log.md) when made.

- The exact **early window** (first 30 days? first 10 reviews?)
- The exact **"poorly rated" cut-off** (later average rating below 3.5? share of negative reviews above some level?)
- How sentiment is made (rule-based tool or a trained text model)
- How many products and reviews to sample, given Colab's memory
- Whether to drop products that are books or manuals
