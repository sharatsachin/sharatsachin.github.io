---
title: AWS ML Speciality Notes (Part 3.1)
date: 2023-02-07 00:00:00 +0530
comments: true
categories: [aws, ml]
tags: [aws]
math: true
---

This post gives a quick review on the framing of business problems as machine learning problems.

<!--more-->

# Frame business problems as machine learning problems

## Determine when to use/when not to use ML

Use ML when:
- There is tons of data that need to be analyzed for patterns that can be used to make predictions
- You cannot code the rules. No simple rule-based solution is possible
- You cannot scale (say, to manually recognize a few million emails)

Don't use ML when:
- You want to solve a simple problem for which a rule-based solution would suffice
- You don't have labeled data and in-house expertise

Best practics when deciding on ML problem:
* define success criteria
* establish a performance metric
* define inputs, outputs and metrics
* decide if ML suitable?
* data sourcing and annotation objectives
* select a simple model

## Know the difference between supervised and unsupervised learning

![](https://www.datasciencecentral.com/wp-content/uploads/2021/10/3862950628-1.png)
(From [here](https://www.datasciencecentral.com/supervised-learning-vs-unsupervised-in-one-picture/))

Two types of classification: binary  and multiclass


## Selecting from among classification, regression, forecasting, clustering, recommendation, etc

Machine learning
* Supervised learning
    * Classification
    * Regression
* Unsupervised learning
    * Clustering
        * [Topic modeling](https://monkeylearn.com/blog/introduction-to-topic-modeling/)
            * LDA (Latent Dirichlet Allocation)$$ ^\star $$
            * NTM (Neural Topic Model)$$ ^\star $$
    * Embeddings
        * Object2Vec$$ ^\star $$
    * Anomaly Detection
        * Random Cut Forest$$ ^\star $$
        * IP Insights$$ ^\star $$
    * Dimensionality Reduction
        * Principle Component Analysis (PCA)$$ ^\star $$


![](https://i.imgur.com/gq6p4S0.png)
(From [here](https://www.analyticsvidhya.com/blog/2021/03/everything-you-need-to-know-about-machine-learning/))

Sagemaker algorithms for each of them:
















