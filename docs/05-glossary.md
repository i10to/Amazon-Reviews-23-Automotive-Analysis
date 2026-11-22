# 05: Glossary

[Back to README](../README.md)

Plain-word meanings of the terms used in this project, in A to Z order.

| Term | Meaning |
|---|---|
| **ASIN / parent_asin** | Amazon's product ID. `asin` is one exact variant (e.g. red, size M). `parent_asin` is the overall product that the variants belong to. |
| **Classification** | A kind of ML task where the answer is a category, e.g. "poorly rated" or "not poorly rated". |
| **Colab** | Google Colab: free notebooks that run on Google's computers through the browser. |
| **Data leakage** | When a model is accidentally shown the answer while learning. It scores well in testing but fails in real use. |
| **DataFrame** | pandas' name for a table: rows and columns, like a sheet in Excel. |
| **DuckDB** | A tool that runs SQL queries on files directly from disk, without loading them all into memory. |
| **F1 score** | A model score that balances catching the cases you care about against raising false alarms. Better than accuracy when one class is much rarer. |
| **Fake blank** | A value that means "missing" but isn't stored as a true blank, e.g. the text `"None"`, an empty text `""` or an empty list `[]`. |
| **Feature** | One input the model uses, e.g. price or share of negative reviews. |
| **Imbalanced classes** | When one answer is much more common than the others, e.g. far more good products than bad ones. |
| **JSON Lines (`.jsonl`)** | A text file where each line is one separate record. |
| **Join** | Combining two tables by matching a shared column, here `parent_asin`. |
| **Parquet** | A compact, fast file format for tables. Good for saving samples to Google Drive. |
| **RAM** | A computer's short-term memory, where data sits while you work on it. Colab's free tier has about 12.7 GB. |
| **Sample** | A smaller random part of the data, used when the whole thing is too big. |
| **Sentiment** | Whether a piece of text sounds positive, neutral or negative. |
| **Target** | The answer the model learns to predict, here whether a product ends up poorly rated. |
| **Unix time (milliseconds)** | Time counted as the number of milliseconds since 1 January 1970. The review `timestamp` uses this. |
