# 04: Running the notebooks in Google Colab

[Back to README](../README.md)

## Why Colab

The data files are several GB. Colab runs the code on Google's computers for free, so:
- the files download to Google's machine, not your laptop
- your laptop only runs the web browser

## What a free Colab machine gives you

Checked with `!df -h` and the Resources panel (November 2026). The numbers can change between sessions.

| Resource | Amount | What it's for |
|---|---|---|
| **Disk** | about 108 GB | Storing the downloaded files. Plenty of room. |
| **RAM** (memory) | about 12.7 GB | Holding data while you work on it. **This is the real limit.** |

To check them yourself: click **Connect** at the top right, then click the RAM / Disk bar to open the **Resources** panel.

## The number one rule: never load a whole file into pandas

The review file is about 8.7 GB once unzipped. pandas needs several times that in RAM. A line like this **will crash Colab**:

```python
reviews = pd.read_json("Automotive.jsonl.gz", lines=True)   # DON'T: loads everything
```

Instead, the project uses **DuckDB**:
1. DuckDB reads the file **straight from disk**.
2. You ask it only for what you need: the structure, a count, some columns, a sample.
3. Only that small result goes into pandas.

The notebooks also cap DuckDB at **6 GB** of RAM with `SET memory_limit = '6GB'`. Note that this cap covers DuckDB only, **not** pandas tables. The rule of thumb:

> DuckDB (max 6 GB) + your pandas tables + about 1 GB for Colab itself must stay under 12.7 GB.

## Things that get wiped

When a Colab session ends or times out, **everything on its disk is deleted**, including the downloaded files.

- Each new session: rerun notebook 01's download cells. Or skip the download if `!ls -lh` shows the files are still there.
- Anything you want to keep (e.g. a cleaned sample): save it to Google Drive as a `.parquet` file.

## Common errors and fixes

| What you see | Cause | Fix |
|---|---|---|
| Colab crashes or restarts during a cell | Ran out of RAM, usually from loading a full file into pandas | Restart the session, then use DuckDB to sample or select columns first |
| `ParserException: syntax error at end of input` in a DuckDB query | The file-name variable holds the wrong thing, e.g. `review_file = !wget ...` saves wget's messages, not the file name | Download with `!wget` on its own line, then type the file name into the variable yourself |
| A file named `Automotive.jsonl.gz.1` appears | `wget` was run twice; it doesn't overwrite, it saves a second copy | Delete the copy with `!rm Automotive.jsonl.gz.1` |
| A downloaded file is only a few KB | The download failed (often a changed link) | Check the current link on the official page (see [02-dataset.md](02-dataset.md)) |
| A DuckDB scan takes several minutes | Normal: it reads the whole zipped file | Wait. Slow is fine; a crash is not. |
