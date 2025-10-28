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
<div>
<title>Empirical frequencies</title>
  <style>
    :root{ --bg:#ffffff; --text:#032b5c; --muted:#4b6b8a; }
    html,body{ margin:0; font-family:system-ui, -apple-system, "Segoe UI", Roboto, Arial; background:var(--bg); color:var(--text); }
    .wrap{ max-width:980px; margin:20px auto; padding:12px; }
    .card{ background:#f8fbff; border-radius:8px; padding:12px; }
    .controls{ display:flex; gap:10px; flex-wrap:wrap; align-items:center; margin:10px 0; }
    label{ font-size:13px; color:var(--muted); }
    input[type=number]{ padding:6px 8px; border-radius:6px; border:1px solid #e6eefc; }
    button{ padding:7px 10px; border-radius:6px; border:0; background:var(--text); color:#fff; cursor:pointer; }
    canvas{ width:100%; height:520px; margin-top:8px; border-radius:6px; background:linear-gradient(180deg,#ffffff,#f1f7ff); display:block; }
    .info{ margin-top:8px; font-size:13px; color:var(--muted); }
  </style>
<body>
  <div class="wrap">
    <div class="card">
      <h1>Empirical frequencies — mean + spread</h1>
      <p class="info">Main plot: mean f(t) and 10%-90% band. Histogram of f(N) below.</p>

      <div class="controls">
        <label for="N">Trials (N)</label>
        <input id="N" type="number" min="1" max="2000" value="30">

        <label for="paths">Paths</label>
        <input id="paths" type="number" min="1" max="2000" value="30">

        <label for="p">p</label>
        <input id="p" type="number" min="0" max="1" step="0.01" value="0.5">

        <button id="run">Run</button>
        <button id="clear">Clear</button>
      </div>

      <canvas id="plot"></canvas>
      <div id="info" class="info">Ready.</div>
    </div>
  </div>

  <script>

    // bernoulli sample
    function bernoulli(p) { return Math.random() < p ? 1 : 0; }

    // simulate single path returns f[0..N]
    function simulatePath(N, p) {
      const f = new Array(N + 1);
      let sum = 0;
      f[0] = 0;
      for (let t = 1; t <= N; t++) {
        sum += bernoulli(p);
        f[t] = sum / t;
      }
      return f;
    }

    // compute quantile (array must be sorted)
    function quantile(sortedArr, q) {
      if (sortedArr.length === 0) return 0;
      const idx = q * (sortedArr.length - 1);
      const lo = Math.floor(idx);
      const hi = Math.ceil(idx);
      if (lo === hi) return sortedArr[lo];
      const w = idx - lo;
      return sortedArr[lo] * (1 - w) + sortedArr[hi] * w;
    }

    // canvas setup
    const canvas = document.getElementById('plot');
    const ctx = canvas.getContext('2d');

    function resizeCanvas() {
      const cssW = canvas.clientWidth;
      const cssH = 520;
      const ratio = window.devicePixelRatio || 1;
      canvas.width = Math.round(cssW * ratio);
      canvas.height = Math.round(cssH * ratio);
      canvas.style.height = cssH + 'px';
      ctx.setTransform(ratio, 0, 0, ratio, 0, 0);
    }
    window.addEventListener('resize', resizeCanvas);
    resizeCanvas();

    function clearCanvas() { ctx.clearRect(0, 0, canvas.width, canvas.height); }

    // layout: top main (75%), bottom histogram (20%)
    function layout(N) {
      const W = canvas.clientWidth;
      const H = 520;
      const histH = Math.floor(H * 0.22);
      const mainH = H - histH - 12; // small gap
      const margins = { left: 50, right: 20, top: 30, bottom: 30 };
      return {
        main: { x: 0, y: 0, w: W, h: mainH, m: margins },
        hist: { x: 0, y: mainH + 12, w: W, h: histH, m: { left: 50, right: 20, top: 10, bottom: 20 } }
      };
    }

    // draw axes (simple) for main
    function drawMainAxes(main, N) {
      const w = main.w, h = main.h, m = main.m;
      // horizontal grid lines
      ctx.strokeStyle = '#e6eefc';
      ctx.lineWidth = 1;
      for (let i = 0; i <= 4; i++) {
        const yy = m.top + (h - m.top - m.bottom) * i / 4;
        ctx.beginPath(); ctx.moveTo(m.left, yy); ctx.lineTo(m.left + (w - m.left - m.right), yy); ctx.stroke();
      }
      // axis lines
      ctx.strokeStyle = '#a9c2f7'; ctx.lineWidth = 1.2;
      ctx.beginPath(); ctx.moveTo(m.left, m.top); ctx.lineTo(m.left, h - m.bottom); ctx.lineTo(m.left + (w - m.left - m.right), h - m.bottom); ctx.stroke();

      // labels
      ctx.fillStyle = '#032b5c'; ctx.font = '12px system-ui';
      ctx.fillText('Trials →', m.left + (w - m.left - m.right) - 60, h - m.bottom + 18);
      ctx.fillText('Relative freq', m.left - 44, m.top + 10);

      // y labels 0..1
      for (let i=0;i<=4;i++){
        const val = (1 - i/4).toFixed(2);
        const yy = m.top + (h - m.top - m.bottom) * i / 4;
        ctx.fillText(val, m.left - 44, yy + 4);
      }
      // x ticks
      const xs = [0, Math.floor(N/4), Math.floor(N/2), Math.floor(3*N/4), N];
      for (const xi of xs){
        const xx = m.left + (w - m.left - m.right) * (xi / N);
        ctx.fillText(String(xi), xx - 8, h - m.bottom + 18);
      }
    }

    // mapping functions
    function makeMapper(main, N) {
      const w = main.w, h = main.h, m = main.m;
      return {
        x: (t) => m.left + (w - m.left - m.right) * (t / N),
        y: (val) => m.top + (h - m.top - m.bottom) * (1 - val)
      };
    }

    // draw mean line and shaded band between q10 and q90
    function drawMeanAndBand(map, meanArr, qLowArr, qHighArr) {
      // band (qHigh -> qLow)
      ctx.beginPath();
      ctx.moveTo(map.x(0), map.y(qHighArr[0]));
      for (let t = 1; t < meanArr.length; t++) {
        ctx.lineTo(map.x(t), map.y(qHighArr[t]));
      }
      for (let t = meanArr.length - 1; t >= 0; t--) {
        ctx.lineTo(map.x(t), map.y(qLowArr[t]));
      }
      ctx.closePath();
      ctx.fillStyle = 'rgba(20,110,230,0.12)';
      ctx.fill();

      // mean line
      ctx.beginPath();
      ctx.strokeStyle = 'rgba(20,110,230,1)';
      ctx.lineWidth = 2;
      ctx.moveTo(map.x(0), map.y(meanArr[0]));
      for (let t = 1; t < meanArr.length; t++) ctx.lineTo(map.x(t), map.y(meanArr[t]));
      ctx.stroke();
    }

    // draw histogram in the bottom area
    function drawHistogram(histArea, finals, bins=30) {
      const hm = histArea.m;
      const innerW = histArea.w - hm.left - hm.right;
      const innerH = histArea.h - hm.top - hm.bottom;
      const counts = new Array(bins).fill(0);
      for (const v of finals) {
        const clamped = Math.max(0, Math.min(1, v));
        const bin = Math.min(bins - 1, Math.floor(clamped * bins));
        counts[bin]++;
      }
      const maxCount = Math.max(...counts, 1);
      const barWUnit = innerW / (bins);

      // draw vertical bars
      for (let b = 0; b < bins; b++) {
        const cnt = counts[b];
        if (cnt === 0) continue;
        const x = hm.left + histArea.x + b * barWUnit;
        const barH = (cnt / maxCount) * (innerH - 8);
        // draw from bottom up
        ctx.fillStyle = 'rgba(220,40,40,0.9)';
        ctx.fillRect(x + 2, histArea.y + hm.top + innerH - barH, Math.max(1, barWUnit - 4), barH);
      }

      // draw x axis labels 0..1 under histogram
      ctx.fillStyle = '#032b5c'; ctx.font = '11px system-ui';
      for (let i = 0; i <= 4; i++) {
        const frac = i / 4;
        const x = histArea.x + hm.left + frac * innerW;
        ctx.fillText((frac).toFixed(2), x - 10, histArea.y + histArea.h - 4);
      }
    }

    // main runner: compute mean, quantiles and draw them
    function runSimulation(N, paths, p) {
      resizeCanvas();
      clearCanvas();

      // simulate all paths and collect values per time t
      const all = new Array(paths);
      for (let i = 0; i < paths; i++) all[i] = simulatePath(N, p);

      // prepare arrays mean[t], q10[t], q90[t]
      const meanArr = new Array(N + 1);
      const qLowArr = new Array(N + 1);
      const qHighArr = new Array(N + 1);

      for (let t = 0; t <= N; t++) {
        const vals = new Array(paths);
        for (let i = 0; i < paths; i++) vals[i] = all[i][t];
        vals.sort((a,b) => a - b);
        // mean
        const sum = vals.reduce((a,b)=>a+b,0);
        meanArr[t] = sum / vals.length;
        // quantiles
        qLowArr[t] = quantile(vals, 0.10); // 10th percentile
        qHighArr[t] = quantile(vals, 0.90); // 90th percentile
      }

      const L = layout(N);
      drawMainAxes(L.main, N);

      const map = makeMapper(L.main, N);

      // draw theoretical p line
      ctx.save();
      ctx.strokeStyle = 'rgba(20,110,230,1)'; ctx.lineWidth = 1.8; ctx.setLineDash([6,4]);
      ctx.beginPath(); ctx.moveTo(map.x(0), map.y(p)); ctx.lineTo(map.x(N), map.y(p)); ctx.stroke();
      ctx.setLineDash([]); ctx.restore();

      // draw band + mean
      drawMeanAndBand(map, meanArr, qLowArr, qHighArr);

      // histogram using final values f(N)
      const finals = all.map(a => a[N]);
      drawHistogram(L.hist, finals, 36);

      // info
      const empiricalMean = finals.reduce((a,b)=>a+b,0)/finals.length;
      document.getElementById('info').textContent = `Paths: ${paths}, Trials: ${N}, p: ${p.toFixed(3)} — empirical mean f(N): ${empiricalMean.toFixed(4)}`;
    }

    // UI wiring
    document.getElementById('run').addEventListener('click', function () {
      const N = Math.max(1, Math.min(2000, parseInt(document.getElementById('N').value) || 200));
      const paths = Math.max(1, Math.min(2000, parseInt(document.getElementById('paths').value) || 200));
      const p = Math.max(0, Math.min(1, parseFloat(document.getElementById('p').value) || 0.5));
      document.getElementById('info').textContent = 'Running...';
      // synchronous computation (no animation)
      runSimulation(N, paths, p);
    });

    document.getElementById('clear').addEventListener('click', function () {
      clearCanvas();
      document.getElementById('info').textContent = 'Cleared.';
    });

  </script>
</div>

## Code

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Empirical frequencies — band + mean</title>
  <style>
    :root{ --bg:#ffffff; --text:#032b5c; --muted:#4b6b8a; }
    html,body{ margin:0; font-family:system-ui, -apple-system, "Segoe UI", Roboto, Arial; background:var(--bg); color:var(--text); }
    .wrap{ max-width:980px; margin:20px auto; padding:12px; }
    .card{ background:#f8fbff; border-radius:8px; padding:12px; }
    .controls{ display:flex; gap:10px; flex-wrap:wrap; align-items:center; margin:10px 0; }
    label{ font-size:13px; color:var(--muted); }
    input[type=number]{ padding:6px 8px; border-radius:6px; border:1px solid #e6eefc; }
    button{ padding:7px 10px; border-radius:6px; border:0; background:var(--text); color:#fff; cursor:pointer; }
    canvas{ width:100%; height:520px; margin-top:8px; border-radius:6px; background:linear-gradient(180deg,#ffffff,#f1f7ff); display:block; }
    .info{ margin-top:8px; font-size:13px; color:var(--muted); }
  </style>
</head>
<body>
  <div class="wrap">
    <div class="card">
      <h1>Empirical frequencies — mean + spread</h1>
      <p class="info">Main plot: mean f(t) and 10%-90% band. Histogram of f(N) below.</p>

      <div class="controls">
        <label for="N">Trials (N)</label>
        <input id="N" type="number" min="1" max="2000" value="30">

        <label for="paths">Paths</label>
        <input id="paths" type="number" min="1" max="2000" value="30">

        <label for="p">p</label>
        <input id="p" type="number" min="0" max="1" step="0.01" value="0.5">

        <button id="run">Run</button>
        <button id="clear">Clear</button>
      </div>

      <canvas id="plot"></canvas>
      <div id="info" class="info">Ready.</div>
    </div>
  </div>

  <script>

    // bernoulli sample
    function bernoulli(p) { return Math.random() < p ? 1 : 0; }

    // simulate single path returns f[0..N]
    function simulatePath(N, p) {
      const f = new Array(N + 1);
      let sum = 0;
      f[0] = 0;
      for (let t = 1; t <= N; t++) {
        sum += bernoulli(p);
        f[t] = sum / t;
      }
      return f;
    }

    // compute quantile (array must be sorted)
    function quantile(sortedArr, q) {
      if (sortedArr.length === 0) return 0;
      const idx = q * (sortedArr.length - 1);
      const lo = Math.floor(idx);
      const hi = Math.ceil(idx);
      if (lo === hi) return sortedArr[lo];
      const w = idx - lo;
      return sortedArr[lo] * (1 - w) + sortedArr[hi] * w;
    }

    // canvas setup
    const canvas = document.getElementById('plot');
    const ctx = canvas.getContext('2d');

    function resizeCanvas() {
      const cssW = canvas.clientWidth;
      const cssH = 520;
      const ratio = window.devicePixelRatio || 1;
      canvas.width = Math.round(cssW * ratio);
      canvas.height = Math.round(cssH * ratio);
      canvas.style.height = cssH + 'px';
      ctx.setTransform(ratio, 0, 0, ratio, 0, 0);
    }
    window.addEventListener('resize', resizeCanvas);
    resizeCanvas();

    function clearCanvas() { ctx.clearRect(0, 0, canvas.width, canvas.height); }

    // layout: top main (75%), bottom histogram (20%)
    function layout(N) {
      const W = canvas.clientWidth;
      const H = 520;
      const histH = Math.floor(H * 0.22);
      const mainH = H - histH - 12; // small gap
      const margins = { left: 50, right: 20, top: 30, bottom: 30 };
      return {
        main: { x: 0, y: 0, w: W, h: mainH, m: margins },
        hist: { x: 0, y: mainH + 12, w: W, h: histH, m: { left: 50, right: 20, top: 10, bottom: 20 } }
      };
    }

    // draw axes (simple) for main
    function drawMainAxes(main, N) {
      const w = main.w, h = main.h, m = main.m;
      // horizontal grid lines
      ctx.strokeStyle = '#e6eefc';
      ctx.lineWidth = 1;
      for (let i = 0; i <= 4; i++) {
        const yy = m.top + (h - m.top - m.bottom) * i / 4;
        ctx.beginPath(); ctx.moveTo(m.left, yy); ctx.lineTo(m.left + (w - m.left - m.right), yy); ctx.stroke();
      }
      // axis lines
      ctx.strokeStyle = '#a9c2f7'; ctx.lineWidth = 1.2;
      ctx.beginPath(); ctx.moveTo(m.left, m.top); ctx.lineTo(m.left, h - m.bottom); ctx.lineTo(m.left + (w - m.left - m.right), h - m.bottom); ctx.stroke();

      // labels
      ctx.fillStyle = '#032b5c'; ctx.font = '12px system-ui';
      ctx.fillText('Trials →', m.left + (w - m.left - m.right) - 60, h - m.bottom + 18);
      ctx.fillText('Relative freq', m.left - 44, m.top + 10);

      // y labels 0..1
      for (let i=0;i<=4;i++){
        const val = (1 - i/4).toFixed(2);
        const yy = m.top + (h - m.top - m.bottom) * i / 4;
        ctx.fillText(val, m.left - 44, yy + 4);
      }
      // x ticks
      const xs = [0, Math.floor(N/4), Math.floor(N/2), Math.floor(3*N/4), N];
      for (const xi of xs){
        const xx = m.left + (w - m.left - m.right) * (xi / N);
        ctx.fillText(String(xi), xx - 8, h - m.bottom + 18);
      }
    }

    // mapping functions
    function makeMapper(main, N) {
      const w = main.w, h = main.h, m = main.m;
      return {
        x: (t) => m.left + (w - m.left - m.right) * (t / N),
        y: (val) => m.top + (h - m.top - m.bottom) * (1 - val)
      };
    }

    // draw mean line and shaded band between q10 and q90
    function drawMeanAndBand(map, meanArr, qLowArr, qHighArr) {
      // band (qHigh -> qLow)
      ctx.beginPath();
      ctx.moveTo(map.x(0), map.y(qHighArr[0]));
      for (let t = 1; t < meanArr.length; t++) {
        ctx.lineTo(map.x(t), map.y(qHighArr[t]));
      }
      for (let t = meanArr.length - 1; t >= 0; t--) {
        ctx.lineTo(map.x(t), map.y(qLowArr[t]));
      }
      ctx.closePath();
      ctx.fillStyle = 'rgba(20,110,230,0.12)';
      ctx.fill();

      // mean line
      ctx.beginPath();
      ctx.strokeStyle = 'rgba(20,110,230,1)';
      ctx.lineWidth = 2;
      ctx.moveTo(map.x(0), map.y(meanArr[0]));
      for (let t = 1; t < meanArr.length; t++) ctx.lineTo(map.x(t), map.y(meanArr[t]));
      ctx.stroke();
    }

    // draw histogram in the bottom area
    function drawHistogram(histArea, finals, bins=30) {
      const hm = histArea.m;
      const innerW = histArea.w - hm.left - hm.right;
      const innerH = histArea.h - hm.top - hm.bottom;
      const counts = new Array(bins).fill(0);
      for (const v of finals) {
        const clamped = Math.max(0, Math.min(1, v));
        const bin = Math.min(bins - 1, Math.floor(clamped * bins));
        counts[bin]++;
      }
      const maxCount = Math.max(...counts, 1);
      const barWUnit = innerW / (bins);

      // draw vertical bars
      for (let b = 0; b < bins; b++) {
        const cnt = counts[b];
        if (cnt === 0) continue;
        const x = hm.left + histArea.x + b * barWUnit;
        const barH = (cnt / maxCount) * (innerH - 8);
        // draw from bottom up
        ctx.fillStyle = 'rgba(220,40,40,0.9)';
        ctx.fillRect(x + 2, histArea.y + hm.top + innerH - barH, Math.max(1, barWUnit - 4), barH);
      }

      // draw x axis labels 0..1 under histogram
      ctx.fillStyle = '#032b5c'; ctx.font = '11px system-ui';
      for (let i = 0; i <= 4; i++) {
        const frac = i / 4;
        const x = histArea.x + hm.left + frac * innerW;
        ctx.fillText((frac).toFixed(2), x - 10, histArea.y + histArea.h - 4);
      }
    }

    // main runner: compute mean, quantiles and draw them
    function runSimulation(N, paths, p) {
      resizeCanvas();
      clearCanvas();

      // simulate all paths and collect values per time t
      const all = new Array(paths);
      for (let i = 0; i < paths; i++) all[i] = simulatePath(N, p);

      // prepare arrays mean[t], q10[t], q90[t]
      const meanArr = new Array(N + 1);
      const qLowArr = new Array(N + 1);
      const qHighArr = new Array(N + 1);

      for (let t = 0; t <= N; t++) {
        const vals = new Array(paths);
        for (let i = 0; i < paths; i++) vals[i] = all[i][t];
        vals.sort((a,b) => a - b);
        // mean
        const sum = vals.reduce((a,b)=>a+b,0);
        meanArr[t] = sum / vals.length;
        // quantiles
        qLowArr[t] = quantile(vals, 0.10); // 10th percentile
        qHighArr[t] = quantile(vals, 0.90); // 90th percentile
      }

      const L = layout(N);
      drawMainAxes(L.main, N);

      const map = makeMapper(L.main, N);

      // draw theoretical p line
      ctx.save();
      ctx.strokeStyle = 'rgba(20,110,230,1)'; ctx.lineWidth = 1.8; ctx.setLineDash([6,4]);
      ctx.beginPath(); ctx.moveTo(map.x(0), map.y(p)); ctx.lineTo(map.x(N), map.y(p)); ctx.stroke();
      ctx.setLineDash([]); ctx.restore();

      // draw band + mean
      drawMeanAndBand(map, meanArr, qLowArr, qHighArr);

      // histogram using final values f(N)
      const finals = all.map(a => a[N]);
      drawHistogram(L.hist, finals, 36);

      // info
      const empiricalMean = finals.reduce((a,b)=>a+b,0)/finals.length;
      document.getElementById('info').textContent = `Paths: ${paths}, Trials: ${N}, p: ${p.toFixed(3)} — empirical mean f(N): ${empiricalMean.toFixed(4)}`;
    }

    // UI wiring
    document.getElementById('run').addEventListener('click', function () {
      const N = Math.max(1, Math.min(2000, parseInt(document.getElementById('N').value) || 200));
      const paths = Math.max(1, Math.min(2000, parseInt(document.getElementById('paths').value) || 200));
      const p = Math.max(0, Math.min(1, parseFloat(document.getElementById('p').value) || 0.5));
      document.getElementById('info').textContent = 'Running...';
      // synchronous computation (no animation)
      runSimulation(N, paths, p);
    });

    document.getElementById('clear').addEventListener('click', function () {
      clearCanvas();
      document.getElementById('info').textContent = 'Cleared.';
    });

  </script>
</body>
</html>
```

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

---