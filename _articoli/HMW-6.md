---
layout: page
title: "Homework 6"
permalink: /articoli/HMW-6/
---

**Author:** {{ site.author }}  
**Matricola:** {{ site.author_student_number }}  
**Email:** [{{ site.author_email }}](mailto:{{ site.author_email }})  
**Course:** {{ site.course }} — {{ site.university }}  
**Academic Year:** {{ site.academic_year }}

---

# Online algorithms for statistics

In this homwork i will implement and test the corresponding online algorithms, which update statistics incrementally as new data becomes available.

---

{% include online-statistics-widget.html %}

---

## Algorithm

```html

{% include online-statistics-widget.html %}

```

---

## Explanation of the algorithm

### State (fields)

- `n` — number of data points processed.  
- `sum` — total sum of values (for convenience).  
- `mean` — current running mean.  
- `M2` — accumulated sum of squared deviations from the mean.  
- `min`, `max` — smallest and largest observed values (not part of the variance math).

---

### Update procedure (`add(x)`)

Each time a new data point `x` is received, the algorithm updates internal state as follows:

1. `n += 1`  
2. `sum += x`  
3. `delta = x - mean`  
4. `mean += delta / n`  ← new mean after including `x`  
5. `delta2 = x - mean`  
6. `M2 += delta * delta2`  ← stable incremental update for variance

This update rule is **numerically stable** because it never subtracts two large, similar numbers (which can cause catastrophic cancellation).  
It keeps track of deviations relative to the *current* mean, so the accumulated quantities remain small and well-behaved.

---

### Variance and standard deviation formulas

- **Sample variance:**  
  `s^2 = M2 / (n - 1)`  
  (used when `n > 1`, otherwise `NaN`).

- **Population variance:**  
  `sigma^2 = M2 / n`

- **Standard deviation:**  
  `s = sqrt(s^2)`

The code computes only the *sample variance* by default.

---

### Computational properties

- **Time complexity:** O(1) per new observation.  
- **Memory usage:** O(1). Only a few running totals are stored — the full dataset is never kept.  
- **Numerical stability:** Excellent, due to Welford’s incremental formulation.


