---
layout: page
title: "Homework 1"
permalink: /articoli/HMW-1/
---

**Author:** {{ site.author }}  
**Matricola:** {{ site.author_student_number }}  
**Email:** [{{ site.author_email }}](mailto:{{ site.author_email }})  
**Course:** {{ site.course }} — {{ site.university }}  
**Academic Year:** {{ site.academic_year }}

---

# What is statistics, and why can it be useful for cybersecurity?

## Introduction

Statistics is the practice of collecting, summarizing, and using data to make informed decisions. Cybersecurity is the practice of protecting computers, networks, and data from harm. Combining the two helps teams spot attacks, prioritize work, and measure whether defenses actually help.

## What is statistics?

* **Describe data.** Use tables, charts, and summary numbers (counts, mean, median, percentiles, variance) to get a quick picture of what's happening in a system.
* **Model uncertainty.** Probability models let you say how likely an observation is under normal conditions — which helps decide whether to investigate something.
* **Draw conclusions from samples.** We often only see part of a system. Statistical inference helps estimate properties of the whole system from samples and gives measures of confidence (e.g., confidence intervals).
* **Predict and classify.** Using past labeled data, models can predict whether a new event is benign or malicious.
* **Assess change.** Statistical tests and experimental design (A/B testing) let you check whether a change — a new rule, a patch, a config tweak — actually improved security, or whether observed differences are likely due to chance.

## Common statistical tools used in cybersecurity

* **Descriptive statistics & visualization:** histograms, time-series plots, boxplots, heatmaps. Great for initial exploration.
* **Time-series analysis:** smoothing, moving averages, and seasonal decomposition to model regular patterns and detect anomalies.
* **Anomaly detection methods:** thresholding (percentiles), control charts (e.g., EWMA), clustering and density estimation to find outliers.
* **Supervised classification:** logistic regression, decision trees, Naive Bayes and modern machine-learning classifiers to label events.
* **Unsupervised methods:** clustering (k-means, DBSCAN), principal component analysis (PCA) for reducing dimensionality and spotting unusual behavior.
* **Evaluation metrics:** precision, recall, F1-score, ROC/AUC. In security, carefully choose metrics that reflect the cost of missed attacks vs. false alarms.

## How statistics helps in real security tasks

### 1. Intrusion and anomaly detection

By modelling normal behavior (e.g., typical login rates, CPU usage, or network flows) you can detect deviations. Simple approaches include using rolling percentiles or z-scores; advanced approaches use density estimation or time-series models.

### 2. Malware and phishing detection

Statistical classifiers trained on labeled samples (malicious/benign) can score new emails, files, or URLs. Feature engineering — turning raw text/logs into counts or indicators — is often the most important part.

### 3. Prioritizing vulnerabilities and alerts

Statistics can combine features (severity scores, age, exploit reports) to estimate the probability that a vulnerability will be exploited. That helps teams triage limited resources.

### 4. Measuring effectiveness

Use before/after comparisons or randomized experiments to check whether a change reduced incidents. Proper statistical design prevents confusing correlation for causation.

### 5. Forensics and incident timelines

Statistical aggregation helps reconstruct sequences of events from noisy logs and estimate time ranges when an attacker was active.

## Practical examples

### Example A — Simple spike detector

1. Collect web request counts per minute for the last 30 days.
2. For each minute-of-day, compute the 95th and 99th percentile.
3. If the current minute’s count exceeds the 99th percentile for that minute-of-day, flag an alert and record context (source IPs, user agents).

Advantages: easy to implement and explain. Cautions: needs to handle missing data and special events (deployments) that change baseline behavior.

### Example 1 — Basic email classifier 

1. Build a dataset of past emails labeled spam/ham.
2. Extract features such as presence of suspicious words, number of links, and sender domain reputation.
3. Train a Naive Bayes or logistic regression model and evaluate using precision/recall.
4. Deploy with a conservative threshold to avoid blocking important emails; monitor false positive rates.

### Example 2 — Prioritizing CVEs

1. Gather data: CVE severity, exploit presence in public repos, asset criticality.
2. Fit a simple logistic regression predicting whether a CVE was observed exploited historically.
3. Use predicted probabilities to rank which vulnerabilities to patch first.

## Conclusion

Statistics provides clear methods to transform noisy cybersecurity data into actionable information: detect anomalies, classify threats, prioritize work, and measure effects. Learning to explore data, choose appropriate models, and evaluate results carefully will make the security work more effective and defensible.

---

## Sources

* Wikipedia — *Statistics*: [https://en.wikipedia.org/wiki/Statistics](https://en.wikipedia.org/wiki/Statistics)
* Wikipedia — *Computer security*: [https://en.wikipedia.org/wiki/Computer_security](https://en.wikipedia.org/wiki/Computer_security)
* Encyclopedia Britannica — *Statistics*: [https://www.britannica.com/science/statistics](https://www.britannica.com/science/statistics)
