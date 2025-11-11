---
layout: page
title: "Homework 7"
permalink: /articoli/HMW-7/
---

**Author:** {{ site.author }}  
**Matricola:** {{ site.author_student_number }}  
**Email:** [{{ site.author_email }}](mailto:{{ site.author_email }})  
**Course:** {{ site.course }} — {{ site.university }}  
**Academic Year:** {{ site.academic_year }}

---

Given a server that receives weekly security updates for n weeks and m attackers who can breach the system with probability p, generate random trajectories of the servers cumulative scores, assign +1 if the server remains secure in a week and -1 if it is breached, draw a parallel with a random walk and count how many trajectories reach each possible total score in your simulation and how those figures converge to the binomial distribution as n and m diverge

---

{% include attacked-server-widget.html %}

---

## Code

```html

{% include attacked-server-widget.html %}

```

### Explanation

- Each week: **secure (+1)** or **breached (−1)**.  
  - Weekly breach probability: `breachProb = 1 - (1 - p)^m`  
  - Weekly secure probability: `q = (1 - p)^m`

- Let `S` = number of secure weeks in `n` weeks  
  → `S ~ Binomial(n, q)`

- Final cumulative score: `T = 2*S - n`  
  (possible values: `-n, -n+2, ..., n`)

- Simulation:  
  Run many independent trajectories, count final scores (histogram).  
  Compare empirical frequencies to theoretical expected counts:  
  `expected = simulations * BinomialPMF(n, k, q)`

- Convergence metric:  
  `L1 = sum(|p_empirical[k] - p_theoretical[k]| for k in all outcomes)`
