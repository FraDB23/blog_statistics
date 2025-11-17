---
layout: page
title: "Homework 9"
permalink: /articoli/HMW-9/
---

# Homework: Probability Interpretations and the Axiomatic Approach

---

## 1. Introduction

This short blog-post reviews the main interpretations of probability (classical, frequentist, Bayesian, and geometric) and explains how the axiomatic approach helps remove conceptual inconsistencies among them. It also summarizes the relationship between probability theory and measure theory (sigma-algebras, probability measures, measurable functions, random variables). Finally, using the probability axioms, it derives the **subadditivity property** and the **inclusion–exclusion principle**.

---

## 2. Main Interpretations of Probability

### 2.1 Classical Interpretation

Probability is defined as the ratio of favorable outcomes to total possible outcomes, assuming all outcomes are equally likely. This works well for symmetric experiments (like rolling a fair die). However, it struggles with real-world cybersecurity scenarios where events are not naturally uniform.

### 2.2 Frequentist Interpretation

Probability is seen as the long-run relative frequency of an event after many repetitions. This is useful for empirical measurements, for example estimating the false positive rate of an intrusion detection system (IDS). Its limitation is that it doesn't deal well with unique or non-repeatable events.

### 2.3 Bayesian Interpretation

Probability represents a degree of belief, updated when new information arrives. This fits cybersecurity especially well — defenders update beliefs about attack likelihoods using logs and threat intelligence. The downside is the subjectivity in choosing priors.

### 2.4 Geometric Interpretation

Probability is based on geometric quantities such as length, area, or volume. A classic example is picking a point uniformly in an interval. This appears in continuous models, such as timing analysis. It still relies on assumptions about uniformity.

### 2.5 How the Axiomatic Approach Resolves Inconsistencies

Kolmogorov’s axiomatic system defines probability as a function on a sigma-algebra that satisfies non-negativity, normalization, and countable additivity. This abstracts away the philosophical interpretation and provides a consistent framework so all interpretations follow the same mathematical rules.

---

## 3. Probability and Measure Theory

### 3.1 Sigma-Algebras

A sigma-algebra is a collection of sets closed under complements and countable unions. It specifies which events are measurable.

### 3.2 Probability Measures

A probability measure assigns a value in [0,1] to each event in the sigma-algebra, satisfying the axioms. It generalizes intuitive ideas of frequency or belief.

### 3.3 Measurable Functions and Random Variables

A random variable is a measurable function from the sample space to the real numbers. This allows us to apply probability measures to real-valued outcomes such as packet sizes or detection delays.

---

## 4. Derivations from the Probability Axioms

### 4.1 Subadditivity

For any two events A and B:

**P(A OR B) ≤ P(A) + P(B)**

**Proof:**
We can rewrite the union as:

A OR B = A OR (B BUT NOT A)

where A and (B BUT NOT A) are disjoint. By additivity:

P(A OR B) = P(A) + P(B BUT NOT A) ≤ P(A) + P(B).

### 4.2 Inclusion–Exclusion Principle

For two events A and B:

**P(A OR B) = P(A) + P(B) - P(A AND B)**

This works because adding P(A) and P(B) counts the overlap twice; subtracting P(A AND B) corrects the double-counting.

## 5. Conclusion

The main interpretations of probability each provide useful perspectives, but they can clash conceptually. The axiomatic approach gives a unified, consistent foundation. Measure theory connects probability with rigorous mathematical tools, making it suitable for continuous models and random variables. Finally, standard results like subadditivity and inclusion–exclusion follow directly from the axioms, reinforcing the internal consistency of the framework.


