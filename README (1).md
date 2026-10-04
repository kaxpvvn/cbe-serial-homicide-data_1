# Serial Homicide Offender Dataset (n = 150)

A hand-coded dataset of 150 serial homicide offenders, built for the paper:

**Behavioral Entropy as a Predictor of Capture Outcome: A Cross-Sectional Data-Mining Analysis of Serial Homicide Offenders**
Benyaphon Bualuang and Boonyavee Bualuang

The data support an exploratory, group-level study of **Composite Behavioral Entropy (CBE)**, a normalized index of how many different behavioral categories were recorded for each offender. See [Limitations](#limitations) and [Intended use](#intended-use) before using the data.

## Files

| File | Description |
|------|-------------|
| `serial_killers_data.csv` | The dataset: 150 rows (one per offender), a `case` label plus 17 variables, no missing fields |
| `CODEBOOK.md` | What each variable and code means (e.g., `V0`, `A1`, `M2`) |
| `LICENSE` | MIT License (covers the data and any code) |

## About the dataset

- **Unit of observation:** one serial homicide offender per row.
- **Variables:** a `case` label (the offender's name as given in the public sources) plus 17 variables: demographics (`Gender`, `country`, active-offending year range), twelve behavioral dimensions (victim, target type, location, approach, method, disposal, signature, organization, MO consistency, victim–offender relationship, motive type, diagnosed mental condition) and two outcomes (`Error`, `Caught`). Definitions are in [`CODEBOOK.md`](CODEBOOK.md), which also lists the small formatting fixes applied to the raw file.
- **Multi-label fields:** some dimensions can hold several codes in one cell, comma-separated (e.g., `A0, A1, A2`).
- **Sources:** compiled from two public secondary sources, the [Radford University Serial Killer Information Center](https://maamodt.asp.radford.edu/serial%20killer%20information%20center/project%20description.htm) and the [Crime Library](https://www.crimemuseum.org/crime-library/) of the National Museum of Crime & Punishment.
- **Collected:** March–May 2026 and August 2026.
- **How it was coded:** the codebook was designed by the authors (refined after informal feedback on the category scheme from researchers at NSTDA, Thailand). An author read each case narrative and assigned the codes. Claude (Anthropic) and ChatGPT (OpenAI) were then used as additional readers to check the codes; disagreements were resolved by the authors re-reading the narrative and other 2026 news reports. All final decisions were the authors'. **No independent human coder checked the coding.**

## How to use

Load the CSV and split multi-label cells into lists:

```python
import pandas as pd

df = pd.read_csv("serial_killers_data.csv")

def codes(cell):
    return [c.strip() for c in str(cell).split(",") if c.strip()]

# How often each approach code appears (A0, A1, A2)
print(df["approach"].apply(codes).explode().value_counts())

# Offenders with more than one method code
print((df["method"].apply(lambda x: len(set(codes(x)))) > 1).sum())
```

Compute Composite Behavioral Entropy (CBE) as defined in the paper:

```python
import math

DIMS = ["victim", "location", "approach", "method",
        "disposal", "organization", "relationship", "motive_type"]
K = {"victim": 5, "motive_type": 4, "location": 3, "approach": 3,
     "method": 3, "disposal": 3, "relationship": 3, "organization": 2}

for d in DIMS:
    k = df[d].apply(lambda x: len(set(codes(x))))
    df[d + "_H"] = k.apply(math.log2) / math.log2(K[d])   # 0 when only one code

df["CBE"] = df[[d + "_H" for d in DIMS]].mean(axis=1)
```

Notes:

- The column is `Gender` (capital G); the other names are as in the codebook.
- `year` is stored as text: a range (`1974-1978`), a single year (`1969`) or a decade (`1960s`).
- Only presence/absence of categories is recorded, so CBE is a **category-richness** index (equal-probability assumption), not a frequency-based Shannon entropy. See `CODEBOOK.md`.
- Single-valued variables (`target_type`, `signature`, `MO_consistency`, `diagnosed_Mental_Condition`, `Error`, `Caught`) are not used in CBE.

## Limitations

- **Convenience sample.** The 150 cases are those the authors could read and code in the project timeframe, not a random or complete roster. "No missing fields" means complete columns, not a complete sample.
- **Skewed coverage.** 132 of 150 offenders (88%) are American. The rest are labeled UK (7), Canada (3), Australia (3), England (2), Soviet Union (1), Hungary (1) and Mexico (1); note that `England` and `UK` are separate labels in the `country` column. Cases are mostly 20th century.
- **Almost everyone was caught.** 146 offenders are `CS1` (caught) and only 4 are `CS0`. Public rosters are built from solved cases, so this is not the real-world capture rate, and analyses of capture outcome are unreliable.
- **Coding reliability unknown.** Records were hand-coded by the two authors (both high school students) from secondary narratives. The codebook has not been externally validated, AI-assisted checking may share systematic biases, and no inter-coder reliability is available.
- **Presence/absence only.** Multi-label fields record which categories occurred, not how often, and depend on how liberally a category was recorded.
- **Sparse categories.** `E1` covers 70% of records and `E3` only 2.
- **Unverified correction.** One record was originally coded `DMT2` on `diagnosed_Mental_Condition` (outside the DMC0–DMC2 scheme). The authors recoded it to `DMC2` by their own judgment, without checking the primary source, and the CSV contains the recoded value. It is not used in CBE, but re-verify that row before relying on it.

## Intended use

This dataset is shared for research and education: reproducibility, methodology and critique of behavioral-diversity measures. Analyses are group-level, exploratory and descriptive and support no population-level inference.

> **Do not use this dataset or the paper's results for individual-level criminal profiling or to predict the risk that any person will be apprehended.** The sample is small, non-random, biased toward captured offenders and hand-coded; individual-level use could mislead with serious consequences for real people. Verify any record against original sources before relying on it.

The `case` column holds offender names as listed in the public sources. The codes are the authors' own interpretation of secondary narratives and may contain errors; they are not verified factual records about any individual.

## License

This repository (data and code) is released under the [MIT License](LICENSE).

## Citation

If you use this dataset or code in your research, please cite our paper:

> B. Bualuang and B. Bualuang, **Behavioral Entropy as a Predictor of Capture Outcome: A Cross-Sectional Data-Mining Analysis of Serial Homicide Offenders** (Under review at IEEE ICDM 2026 - Teen Research Track)
>
> *Note: Venue, year, and DOI/link will be updated once the paper is officially published.*
