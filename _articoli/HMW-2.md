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

---

# Computing K univariant distributions

In this homework, as you can see below, i have used a population of 50 units of people. Each unit has 4 attributes: name, age, height and weight.
Only the last three attributes has been used.

| ID | Nome                | Età | Altezza (cm) | Peso (kg) |
|----|----------------------|-----|---------------|-----------|
| 1  | Mario Rossi          | 32  | 178.5         | 75.2      |
| 2  | Luca Bianchi         | 25  | 182.0         | 80.1      |
| 3  | Giulia Verdi         | 29  | 165.3         | 58.4      |
| 4  | Francesca Neri       | 41  | 170.2         | 64.7      |
| 5  | Andrea Gallo         | 36  | 176.8         | 72.0      |
| 6  | Chiara Romano        | 23  | 160.4         | 54.2      |
| 7  | Davide Conti         | 28  | 185.0         | 83.6      |
| 8  | Laura Rizzi          | 35  | 168.9         | 62.5      |
| 9  | Simone Greco         | 27  | 180.6         | 77.1      |
| 10 | Elisa Moretti        | 31  | 162.7         | 56.3      |
| 11 | Federico Esposito    | 33  | 175.0         | 70.5      |
| 12 | Martina Costa        | 26  | 167.3         | 59.9      |
| 13 | Giorgio Fontana      | 45  | 179.2         | 82.0      |
| 14 | Ilaria Gatti         | 22  | 158.8         | 52.4      |
| 15 | Paolo Marchetti      | 39  | 183.4         | 85.7      |
| 16 | Sara Colombo         | 30  | 169.5         | 61.3      |
| 17 | Alessio Ferri        | 28  | 181.7         | 79.5      |
| 18 | Beatrice Marini      | 34  | 166.1         | 60.2      |
| 19 | Riccardo Leone       | 26  | 177.9         | 74.6      |
| 20 | Eleonora Bruno       | 40  | 164.8         | 59.0      |
| 21 | Stefano Fiore        | 37  | 180.0         | 78.3      |
| 22 | Valentina Greco      | 24  | 163.9         | 55.8      |
| 23 | Gabriele Serra       | 29  | 182.5         | 82.1      |
| 24 | Claudia Riva         | 32  | 170.7         | 63.2      |
| 25 | Matteo Testa         | 27  | 174.3         | 69.0      |
| 26 | Alessandra Villa     | 33  | 168.0         | 61.5      |
| 27 | Tommaso De Luca      | 38  | 185.5         | 88.9      |
| 28 | Roberta Parisi       | 36  | 165.2         | 60.0      |
| 29 | Emanuele Puglisi     | 42  | 178.8         | 84.4      |
| 30 | Veronica Longo       | 31  | 160.6         | 53.8      |
| 31 | Lorenzo Rizzi        | 25  | 179.5         | 75.6      |
| 32 | Marta Fabbri         | 29  | 162.0         | 57.1      |
| 33 | Pietro Cattaneo      | 35  | 184.1         | 86.2      |
| 34 | Silvia De Santis     | 28  | 167.4         | 59.4      |
| 35 | Davide Lombardi      | 30  | 180.3         | 80.5      |
| 36 | Camilla D’Amico      | 23  | 159.7         | 51.9      |
| 37 | Nicola Gallo         | 26  | 176.9         | 72.4      |
| 38 | Elena Ferraro        | 27  | 165.6         | 58.0      |
| 39 | Carlo Bianco         | 43  | 182.9         | 85.3      |
| 40 | Serena Palmieri      | 24  | 161.5         | 54.9      |
| 41 | Giovanni Basile      | 39  | 178.2         | 81.7      |
| 42 | Angela Caruso        | 37  | 166.9         | 62.1      |
| 43 | Fabio Grassi         | 34  | 180.8         | 77.9      |
| 44 | Lucia Vitale         | 29  | 164.2         | 58.8      |
| 45 | Daniele Riva         | 31  | 177.3         | 74.2      |
| 46 | Alice Leone          | 25  | 162.9         | 55.6      |
| 47 | Marco Ferrara        | 33  | 181.0         | 79.1      |
| 48 | Giada Pellegrini     | 30  | 168.5         | 60.7      |
| 49 | Antonio Sanna        | 41  | 183.7         | 86.5      |
| 50 | Elena Gallo          | 28  | 160.2         | 54.1      |
