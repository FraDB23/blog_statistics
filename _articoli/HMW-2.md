---
layout: page
title: "Homework 2"
permalink: /articoli/HMW-2/
---

**Author:** {{ site.author }}  
**Matricola:** {{ site.author_student_number }}  
**Email:** [{{ site.author_email }}](mailto:{{ site.author_email }})  
**Course:** {{ site.course }} — {{ site.university }}  
**Academic Year:** {{ site.academic_year }}

---

# Dataset and Distribution

## Dataset

A **dataset** is a structured collection of related data, usually organized as a table. In this tabular form:

- **Rows** represent individual observations or statistical units (for example: a student, an object, or an event).
- **Columns** represent variables or attributes measured for each observation (for example: Age, Gender, Grade).

Variables are commonly classified as:
- **Quantitative** — numeric measures (integers or continuous values like height or weight).
- **Qualitative (categorical)** — named categories without intrinsic order (for example: gender, type).
- **Ordinal** — categorical variables with a natural order but not appropriate for arithmetic operations (for example: education level or rating).
- **Temporal** — date or time values.

Datasets can be described as:
- **Structured** — with a fixed schema (relational tables, CSV files).
- **Semi‑structured** — having some organization but a flexible schema (JSON, XML).
- **Unstructured** — lacking a predefined schema (texts, images, audio).

In a DBMS (for example MySQL, SQLite, Microsoft Access) datasets are stored as tables that can be queried, filtered, and analyzed. A dataset is a finite realization of a population or process and is the basis for descriptive and inferential analysis.

---

## Distribution

A **distribution** describes how the values of a variable, or of several variables jointly, are spread across their possible values. Distributions can be considered in two main ways:

- **Empirical (frequency) distributions**: obtained from the dataset by counting occurrences or computing relative frequencies (for example, frequency tables, histograms).
- **Probabilistic (theoretical) distributions**: mathematical models that assign probabilities to possible outcomes (for example: Normal, Binomial, Poisson).

By dimensionality, distributions include:
- **Univariate distribution**: distribution of a single variable (for example, the distribution of Age; useful summaries include mean, median, mode, variance).
- **Bivariate distribution**: joint distribution of two variables, often summarized in contingency tables or scatterplots, showing how pairs of values co‑occur and revealing association or dependence.
- **Multivariate distribution**: joint distribution of three or more variables. For k = 3 relationships can still be represented (for example via 3D views or layered tables); for k > 3 summaries rely on algebraic objects (for example covariance matrices) or multivariate models.
