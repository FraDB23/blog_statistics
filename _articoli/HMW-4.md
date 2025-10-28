---
layout: page
title: "Homework 4"
permalink: /articoli/HMW-4/
---

**Author:** {{ site.author }}  
**Matricola:** {{ site.author_student_number }}  
**Email:** [{{ site.author_email }}](mailto:{{ site.author_email }})  
**Course:** {{ site.course }} — {{ site.university }}  
**Academic Year:** {{ site.academic_year }}

---

#  Law of Large Numbers (LLN) using a bernoulli variable

{% include freq-widget.html %}


{% raw %}

## Code

```html
{% include freq-widget.html %}
```


{% endraw %}

### Overview
The page implements an interactive simulation of empirical (relative) frequencies for Bernoulli trials (success/failure experiments). It simulates several independent sample paths of repeated Bernoulli trials with probability `p` of success, computes per-time averages and quantile bands across simulated paths, and visualizes:
- the **mean empirical frequency** \(f(t)\) at each trial number `t` (the average across all simulated paths), and
- a **spread band** showing the 10%–90% quantile range at each `t` (i.e., the region between the 10th and the 90th percentile of the paths),
- a dashed horizontal line for the theoretical probability `p`,
- a histogram of the final empirical frequencies `f(N)` across the simulated sample paths.

The UI has three inputs (number of trials `N`, number of simulated paths `paths`, and success probability `p`) and two buttons: **Run** (simulate and draw) and **Clear** (clear the canvas).

---

### Important functions and pieces

#### `bernoulli(p)`
A tiny helper that returns `1` (success) with probability `p`, otherwise `0`. Internally it uses `Math.random() < p` for the Bernoulli draw.

#### `simulatePath(N, p)`
Simulates a single path of length `N` and returns an array `f[0..N]` where `f[t]` is the relative frequency of successes after `t` trials (i.e., number_of_successes_up_to_t / t). Implementation details:
- `f[0]` is set to `0` (no trials yet).
- For each `t` from `1` to `N`, it draws a Bernoulli sample and updates a running `sum` of successes; `f[t] = sum / t`.

This produces a time series of empirical frequencies for one simulated run.

#### `quantile(sortedArr, q)`
Computes a linear-interpolated quantile from a **sorted** array. The index is computed as `q * (n-1)` and the function interpolates between neighboring array entries if needed. This function is used to obtain the 10th and 90th percentiles at each time `t` across the simulated paths.

#### Canvas setup and `resizeCanvas()`
The canvas is sized to match its CSS width and a fixed height (520 px), with devicePixelRatio handling for crisp rendering on high-DPI screens:
- `canvas.width` and `canvas.height` are set to the CSS dimensions multiplied by `devicePixelRatio`.
- `ctx.setTransform(ratio, 0, 0, ratio, 0, 0)` is used so drawing coordinates are in CSS pixels while the backing store has the proper pixel density.

A `resize` event listener keeps the canvas consistent when the window changes size.

#### `layout(N)`
Splits the canvas area vertically into two zones:
- `main` (approx. 75%): the time-series plot (mean + quantile band)
- `hist` (approx. 22%): the histogram of final frequencies f(N)
Both subareas have their own margins for axes and labels.

#### Axis drawing and mapping
- `drawMainAxes(main, N)` paints a simple background grid, axes lines, and numeric labels for the Y-axis (0–1) and X ticks (0, N/4, N/2, 3N/4, N).
- `makeMapper(main, N)` returns two mapping functions `x(t)` and `y(value)` which convert data coordinates (trial `t` and relative frequency `value`) into canvas pixel coordinates using the area dimensions and margins. The `y` function flips the vertical axis so `0` is at the bottom of the plot area.

#### `drawMeanAndBand(map, meanArr, qLowArr, qHighArr)`
Draws the confidence/spread band and the mean curve:
- The band is drawn by creating a polygon that follows the 90th percentile curve from left to right and then returns along the 10th percentile curve from right to left; this polygon is filled with a semi-transparent blue to represent spread.
- The mean line `meanArr[t]` is drawn on top with a solid thicker line.

This visualizes typical concentration of sample paths around the mean and how the band narrows with more trials (if paths concentrate around `p`).

#### `drawHistogram(histArea, finals, bins)`
Draws a histogram of the final empirical frequencies `f(N)` across all simulated paths:
- Values are clamped to `[0,1]` and assigned to `bins` equally sized buckets.
- Each bin is scaled in height relative to the maximum bin count.
- Bars are drawn from the bottom of the histogram area upward.
- X-axis tick labels `0.00, 0.25, 0.50, 0.75, 1.00` are printed below the histogram.

Note: the histogram uses a red fill, and has a parameter `bins` (default 36 in the code) that can be changed.

#### `runSimulation(N, paths, p)`
The central function that executes the simulation and triggers drawing:
1. `resizeCanvas()` and `clearCanvas()` ensure a consistent drawing area.
2. Simulates `paths` independent paths by repeatedly calling `simulatePath(N, p)` and storing their arrays in `all`.
3. For each `t` in `0..N` it builds an array of values across all paths at time `t`, sorts it, computes the mean and quantiles (10% and 90%) using `quantile`.
4. Calls the layout and drawing functions:
   - draws main axes,
   - draws a dashed horizontal line at the theoretical probability `p` (so you can compare empirical behaviour to expectation),
   - draws the quantile band and mean line,
   - computes `finals = all.map(a => a[N])` and draws the histogram of those final values.
5. Updates the textual info area with the number of paths, trials, p, and the empirical mean of `f(N)` across paths.

#### UI wiring
- The "Run" button reads numeric input values (bounded) for `N`, `paths`, and `p`, sets the info text to `"Running..."`, then calls `runSimulation` synchronously.
- The "Clear" button clears the canvas and writes `"Cleared."` in the info text.

---

### Notes 

- **Statistical behavior**: As `N` grows, each path's `f(t)` tends (by the Law of Large Numbers) to concentrate near `p`. The 10%–90% band should shrink with increasing `t` (for fixed `paths`) and the histogram of `f(N)` becomes more tightly clustered around `p` on average.
