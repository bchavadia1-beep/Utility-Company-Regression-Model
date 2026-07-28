# Utility-Company-Regression-Model
# AFM244 — Seasonal Dummy Variables in Regression

**Notebook:** `AFM244_S26u_Thursday_Dummy_Variables.ipynb`

A walkthrough of building a linear regression model that uses a **seasonal dummy variable** (winter vs. non-winter) and an **interaction term** to forecast revenue from production. Built in Google Colab / Jupyter using `statsmodels`.

---

## What this notebook does

1. Loads a dataset of production and revenue observations.
2. Creates a `winter` dummy variable flagging December, January, and February.
3. Creates an interaction term (`production × winter`) so the slope can differ by season.
4. Splits the data into training and testing sets using a pre-existing `type` column.
5. Fits an OLS model: `revenue ~ production + winter_DV + winter_interaction`.
6. Derives separate revenue equations for winter and non-winter months.
7. Visualizes the training data with two fitted regression lines (winter and non-winter).

The **MAPE evaluation** is set up as a participation task but is **not yet implemented** in the notebook (see *Outstanding task* below).

---

## Requirements

- Python 3
- `numpy`, `pandas`, `matplotlib`, `statsmodels`

The notebook imports these at the top; in Colab they are pre-installed.

## Input data

The notebook reads a file named **`AICPA_regressionAnalysisData.csv`** from the working directory. This file is **not** included in the notebook — you'll need it present (e.g. uploaded to your Colab session) for the notebook to run.

Based on how the code uses the file, the CSV is expected to contain at least these columns (I'm inferring these from the code, not from the CSV itself — you may want to confirm against the actual file):

| Column | Used for |
|---|---|
| `date` | Parsed to datetime; the month drives the winter dummy |
| `production` | Independent variable |
| `revenue` | Dependent variable (target) |
| `type` | Row label, expected to contain the values `dt4training` and `dt4testing` to split the data |

---

## Step-by-step

**Cell 0 — Load data.** Imports libraries, sets a 2-decimal display format, reads the CSV into `HS_data`, and displays it.

**Cell 1 — Parse dates.** Converts the `date` column to datetime so month extraction works.

**Cell 3 — Winter dummy.** Creates `winter_DV = 1` when the month is 12, 1, or 2, else `0`.

**Cell 4 — Interaction term.** Creates `winter_interaction = production × winter_DV`. This lets winter have both a different intercept and a different slope.

**Cell 5 — Train/test split.** Splits `HS_data` into `dt4training` and `dt4testing` by filtering on the `type` column.

**Cell 7 — Fit the model.** Sets `y = revenue`, `x = [production, winter_DV, winter_interaction]`, adds a constant, and fits `sm.OLS(y, x)` as `model1`.

**Cell 8 — Interpretation.** Writes out the fitted equation and simplifies it for the two seasons (setting `winter` to 0 or 1).

**Cell 10 — Visualization.** Scatter-plots the training data, extracts the four coefficients from `model1.params`, and plots a red non-winter line and a blue winter line.

---

## The model

The fitted equation (from the notebook's own recorded output) is approximately:

```
revenue ≈ 5,629,257.08 + 13.51·production + 14.16·(production·winter) − 201,742·winter
```

- **Non-winter** (`winter = 0`): `revenue ≈ 5,629,257.08 + 13.51·production`
- **Winter** (`winter = 1`): `revenue ≈ (5,629,257.08 − 201,742) + (13.51 + 14.16)·production`

> ⚠️ These coefficient values come from one recorded run inside the notebook, not from an independent calculation on my end. They will change if the input CSV changes, so treat them as illustrative and re-check against your own model output before relying on them.

A note on the plotting code in Cell 10: the coefficients are unpacked as
`intercept, production_coefficient, winter_DV_intercept, winter_coefficient = model1.params`.
Because `model1.params` follows the column order `const, production, winter_DV, winter_interaction`, the variable named `winter_DV_intercept` actually holds the **winter_DV** coefficient (the intercept shift, ≈ −201,742) and `winter_coefficient` holds the **interaction** coefficient (the slope shift, ≈ 14.16). The lines plot correctly, but the variable names read a little counterintuitively — worth keeping in mind if you extend the code.

---

## Outstanding task (participation)

The notebook flags a to-do: **submit the Colab file with a MAPE score calculated for this model.** As written, the notebook:

- creates `dt4testing` and displays it, but
- does **not** generate predictions on the test set, and
- does **not** compute MAPE.

To complete it you would need to build the same feature matrix for `dt4testing`, predict revenue with `model1`, and compute MAPE against the actual test revenue. I've left that unimplemented here rather than guess at the exact form you want — if you'd like, tell me whether you want MAPE on the **testing** set (the usual choice) or the **training** set, and I can draft that cell for you.

---

## How to run

1. Open the notebook in Google Colab (or Jupyter).
2. Make `AICPA_regressionAnalysisData.csv` available in the working directory (in Colab, upload it or mount Drive).
3. Run the cells top to bottom.
