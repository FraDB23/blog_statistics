---
layout: page
title: "Homework 5"
permalink: /articoli/HMW-5/
---

**Author:** {{ site.author }}  
**Matricola:** {{ site.author_student_number }}  
**Email:** [{{ site.author_email }}](mailto:{{ site.author_email }})  
**Course:** {{ site.course }} — {{ site.university }}  
**Academic Year:** {{ site.academic_year }}

---

# Measures of Center and Spread

---

## Why this matters
When a dataset is presented — such as test scores, daily temperatures, or investment returns — two fundamental questions arise: **Where do the values sit?** and **How much do they vary?** Measures of *location* (center) and *dispersion* (spread) answer those questions. The following sections provide practical definitions, short examples, and rules of thumb for selecting appropriate summaries.

---

## Measures of center (describing a "typical" value)
These statistics summarize where the bulk of the data lies.

### Arithmetic mean (the familiar average)
The arithmetic mean equals the sum of the values divided by the number of observations. It uses every data point and has convenient mathematical properties.

- Formula: `x̄ = (x₁ + x₂ + ... + xₙ) / n`
- Example: for `6, 7, 8, 5, 9` → `x̄ = 7`

**Appropriate use:** symmetric distributions without strong outliers; also fundamental in regression and many inferential procedures.

**Caveat:** extreme values can distort the mean.

---

### Median (the center by rank)
The median is the middle value after sorting the observations; if the count is even, it equals the average of the two middle values.

- Interpretation: 50% of observations lie below the median and 50% above.
- Example: for `3, 5, 7, 8, 10` → median = `7`

**Appropriate use:** skewed data or datasets with outliers; provides a robust notion of a typical case.

---

### Mode (the most frequent value)
The mode denotes the most common observation in the dataset and naturally applies to categorical or discrete data.

- Example: in `2, 3, 3, 5, 7, 7, 7, 9` → mode = `7`

**Appropriate use:** categorical variables or highlighting the single most frequent outcome.

---

### Mid-range (quick centre estimate)
The mid-range equals `(min + max) / 2` and provides a simple center based on extremes.

**Appropriate use:** limited; sometimes useful for symmetric distributions or quick checks.

---

## Alternative means for specific contexts
Different types of means match different generative processes.

- **Weighted mean:** `Σ(xᵢ·wᵢ) / Σwᵢ` — for observations with unequal importance (e.g., grades weighted by credits).
- **Geometric mean:** `(∏ xᵢ)^(1/n)` or `exp((1/n)Σ ln xᵢ)` — for multiplicative processes and average growth rates.
- **Harmonic mean:** `n / Σ(1/xᵢ)` — for averaging rates when a fixed quantity (distance, work) is divided by varying speeds.
- **Quadratic mean / RMS:** `sqrt((1/n) Σ xᵢ²)` — emphasizes large magnitudes; common in physics and signal processing.

**Rule of thumb:** use arithmetic mean for additive processes, geometric mean for multiplicative processes, and harmonic mean for rates.

---

## Measures of spread (quantifying variability)
After selecting a center, spread quantifies deviations around it.

### Range
`max − min`. Simple but highly sensitive to outliers.

---

### Interquartile Range (IQR)
`IQR = Q3 − Q1` measures the spread of the middle 50% and is robust to tails. It pairs well with the median and is commonly used in boxplots. Outliers are often flagged at `Q1 − 1.5·IQR` and `Q3 + 1.5·IQR`.

---

### Variance and Standard Deviation
- **Variance:** average squared deviation from the mean: `σ² = (1/n) Σ (xᵢ − x̄)²` (or `1/(n−1)` for sample variance).
- **Standard deviation:** `σ = sqrt(variance)` — expressed in original data units.

**Appropriate use:** symmetric or nearly normal data and applications requiring analytic properties (e.g., confidence intervals, hypothesis tests).

**Caveat:** both measures are sensitive to extreme values.

---

### Coefficient of Variation (CV)
`CV = SD / mean` — a unitless measure for comparing relative variability across datasets with different scales.

---

### Mean Absolute Deviation (MAD)
`MAD = (1/n) Σ |xᵢ − x̄|` — a more interpretable and sometimes more robust alternative to variance.

---

## Short illustrative examples
1. **Outlier effect:** data `2, 3, 5, 5, 100` → mean `= 23` (misleading), median `= 5` (more representative of the bulk).
2. **Average speed:** travel at `60 km/h` out and `40 km/h` back over the same distance → harmonic mean gives the correct average speed: `H ≈ 48 km/h`.
3. **Growth rates:** returns `+10%`, `+20%`, `−10%` → geometric mean ≈ `+6%` average annual growth (not the arithmetic average).

---

## Practical decision checklist
1. Inspect a histogram or boxplot. If the distribution appears symmetric → report **mean ± SD**. If skewed → report **median (IQR)**.
2. For rates or multiplicative processes → consider the **geometric mean**. For speeds or rates with fixed units → consider the **harmonic mean**.
3. For non-technical audiences → prefer intuitive summaries (median and IQR) with brief explanations.
4. When uncertain → report both `mean ± SD` and `median (IQR)`; disagreement between them signals skew or outliers.

---

## Compact formula table

| Measure | Formula | When to use |
|---|---|---|
| Arithmetic mean | `x̄ = (1/n) sum_{i=1}^n x_i` | Symmetric data without strong outliers; default for many analyses |
| Median | middle value of ordered data | Skewed data or when outliers are present |
| Mode | value with highest frequency | Categorical or discrete data |
| Weighted mean | `sum(w_i * x_i) / sum(w_i)` | When observations have different importance |
| Geometric mean | `(prod_{i=1}^n x_i)^(1/n)` or `exp((1/n) sum ln x_i)` | Multiplicative processes (growth rates) |
| Harmonic mean | `n / sum(1/x_i)` | Averaging rates (e.g., average speed over equal distances) |
| Variance (population) | `sigma^2 = (1/n) sum (x_i - x̄)^2` | Quantifies squared dispersion; used in many models |
| Standard deviation | `sigma = sqrt(sigma^2)` | Same units as data; intuitive measure of spread |
| Interquartile range (IQR) | `IQR = Q3 - Q1` | Robust measure of spread (middle 50%) |
| Range | `max - min` | Quick check; sensitive to outliers |
| Coefficient of variation (CV) | `CV = SD / x̄` | Compare relative variability across scales |
| Mean absolute deviation (MAD) | `MAD = (1/n) sum (x_i - x̄)` | More interpretable/robust alternative to variance |
| Quadratic mean (RMS) | `sqrt((1/n) sum x_i^2)` | Emphasizes large values; used in physics/signal processing |

If preferred, worked numerical examples can be added as a second compact table (one row per example).