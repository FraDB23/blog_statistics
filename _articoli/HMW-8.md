---
layout: page
title: "Homework 8"
permalink: /articoli/HMW-8/
---

**Author:** {{ site.author }}  
**Matricola:** {{ site.author_student_number }}  
**Email:** [{{ site.author_email }}](mailto:{{ site.author_email }})  
**Course:** {{ site.course }} — {{ site.university }}  
**Academic Year:** {{ site.academic_year }}

---

Discuss the analogies between this homework ([Homework 7](/blog_statistics/articoli/HMW-7/)) and the previous one based on the Bernoulli process simulation of the Law of Large Numbers ([Homework 4](/blog_statistics/articoli/HMW-4/)), highlighting similarities and differences, illustrate the mathematical properties and relationships with binomial coefficients, Pascals triangle, binomial expansion, the Fibonacci sequence, and combinatorial coefficients, etc

---

**Goal:** show how Homework 7 (server random walk) and Homework 4 (Bernoulli / LLN) are the same basic Bernoulli experiment seen in two ways.

## 1. The two problems

- **HMW‑4 (LLN):** simulate many Bernoulli trials with success probability `p`. Track the empirical frequency `f(t) = successes / t` and show that `f(t)` concentrates near `p` as `t` grows.

- **HMW‑7 (random walk):** each week is `+1` (secure) with probability `q` or `-1` (breached) with probability `1-q`. With `m` attackers of individual breach prob `p` we have:

```text
q = (1-p)**m
S ~ Binomial(n, q)   # number of secure weeks
T = 2*S - n          # final signed score (values -n, -n+2, ..., n)
```

We simulate many trajectories and count how many end at each `T` — this histogram matches the binomial probabilities.

## 2. Main similarities

- Both use independent Bernoulli trials as the building block.
- The binomial coefficients `C(n,k)` appear in both: they count sequences with exactly `k` successes (or secure weeks).
- Limit theorems apply to both: LLN (frequencies → probability) and CLT (binomial → normal for large `n`).

## 3. Main differences

- **What we look at:** HMW‑4 studies time averages `S/n`; HMW‑7 studies the endpoint `T` (signed sum).
- **Parameterization:** HMW‑4 uses `p`; HMW‑7 uses `q = (1-p)**m` because of multiple attackers.
- **Interpretation:** HMW‑4 emphasizes convergence; HMW‑7 emphasizes counting paths and exact PMF.

## 4. Short notes on combinatorics

- Pascal's triangle contains the binomial coefficients `C(n,k)` used as weights in the PMF.
- The binomial expansion `(q + (1-q))^n` is the algebraic form of summing those probabilities.
- Fibonacci numbers appear only if you change the counting rules (for example, forbid consecutive breaches). They are not needed for the basic independent‑trial model.