---
title: AWS ML Speciality Notes (Part 3.5)
date: 2023-02-11 00:00:00 +0530
comments: true
categories: [aws, ml]
tags: [aws]
math: true
---

This post gives a quick review on the evalution of machine learning models.

<!--more-->

# Evaluate machine learning models

## Bias and variance

Bias is the simplifying assumptions made by the model to make the target function easier to approximate.
Variance is the amount that the estimate of the target function will change given different training data.

For classification and regression, we can decompose the error of a learned classifier into two terms: 
1. Bias: the class of models can’t fit the data.
    * Fix: a more expressive model class.
2. Variance: the class of models could fit the data, but doesn’t because it’s hard to fit.
    * Fix: a less expressive model class. 

### Detecting and handling bias and variance

* High variance can be identified if the model has:
    * Low training error and high test error.
* High Bias can be identified if the model has:
    * High training error and the test error is almost similar to training error.

![](https://i.imgur.com/9IDVGMR.png)

### Avoid overfitting / underfitting

**Overfitting** – High variance and low bias

Techniques to reduce overfitting :
1. Increase training data.
1. Reduce model complexity.
1. Early stopping during the training phase (have an eye over the loss over the training period as soon as loss begins to increase stop training).
1. Ridge Regularization and Lasso Regularization
1. Use dropout for neural networks to tackle overfitting.

**Underfitting** – High bias and low variance

Techniques to reduce underfitting :
1. Increase model complexity
1. Increase number of features, performing feature engineering
1. Remove noise from the data.
1. Increase the number of epochs or increase the duration of training to get better results.

## Metrics

### Confusion matrix 

A confusion matrix is a matrix that plots the amount of correct predictions against the amount of incorrect predictions. For a binary classifier, this would mean the amount of true negatives and true positives (correct predictions) versus the amount of false negatives and false positives (incorrect predictions).

![](https://i.imgur.com/3qudqrq.png)

* You look at precision, when preventing False Positives (Type I errors) are important.
* You look at recall, when preventing False Negatives (Type II errors) are important.

Other than precision, specificity/selectivity/TNR, and sensitivity/recall/TPR, we have accuracy and f-score:

\\[{\text{Accuracy}}={\frac {\text{correct classifications}}{\text{all classifications}}}\\]

#### $$ F $$ score, $$ F_{1} $$ score

The harmonic mean of the precision and recall.

\\[F_{1}={\frac {2}{\mathrm {recall} ^{-1}+\mathrm {precision} ^{-1}}}=2{\frac {\mathrm {precision} \cdot \mathrm {recall} }{\mathrm {precision} +\mathrm {recall} }}={\frac {\mathrm {tp} }{\mathrm {tp} +{\frac {1}{2}}(\mathrm {fp} +\mathrm {fn} )}}\\]

##    ## $$ F_{\beta} $$ score

More generic $$ F_{\beta} $$ score applies additional weights, valuing one of precision or recall more than the other. Two commonly used values for $$ \beta $$ are $$ 2 $$, which weighs recall higher than precision, and $$ 0.5 $$, which weighs recall lower than precision.

\\[{\displaystyle F_{\beta }=(1+\beta ^{2})\cdot {\frac {\mathrm {precision} \cdot \mathrm {recall} }{(\beta ^{2}\cdot \mathrm {precision} )+\mathrm {recall} }}}\\]

### AUC - ROC 

![](https://i.imgur.com/FICMCrT.png)

To combine the TPR and the FPR into one single metric, we first compute the two former metrics with many different threshold (for example $$ 0.00; 0.01,0.02,…,1.00 $$) for the logistic regression, then plot them on a single graph, with the FPR values on the x-axis and the TPR values on the y-axis. The resulting curve is called ROC curve, and the metric we consider is the AUC of this curve, which we call AUROC.

AUC stands for the Area Under the Curve and usually refers to the area under the receiver operator characteristic (ROC) curve.

ROC Curves are used to see how well your classifier can separate positive and negative examples and to identify the best threshold for separating them. 
To use ROC curves, your classifier must be **ranking** - able to rank examples such that ones with higher rank are more likely to be positive. 
- [How to draw ROC curve](https://stats.stackexchange.com/a/105577/331716)

AUC provides an aggregate measure of performance across all possible classification thresholds. One way of interpreting AUC is as the probability that the model ranks a random positive example more highly than a random negative example.

AUC is desirable for these two reasons:
1. scale-invariant : measures how well predictions are ranked, rather than their absolute values.
2. classification-threshold-invariant : measures the quality of the model's predictions irrespective of classification threshold

Caveats, which limit usefulness of AUC in certain cases:
1. scale-invariance  : sometimes we really do need well calibrated probability outputs
2. classification-threshold invariance : cases where there are wide disparities in the cost of false negatives vs. false positives

### RMSE 

## Online and offline model evaluation, A/B Testing

![](https://i.imgur.com/024pXZo.png)

When should you test? 

#### Different Dataset 
* Dataset has been update to include latest fresh data 
* Dataset has been cleaned, normalised or scaled differently 
* Datasets has been resampled to attempt to remove bias or adjust for minority class imbalance 

#### Different Model 
* You are trying a different algorithm architecture 
* You are experimenting with different hyper parameters 
* You are using transfer learning to fine tune a pre-trained model

#### Traditional A/B testing 
1. Assign users at random to either control (A) or experimental treatment (B) 
2. Define the baseline metric e.g. 30% 
3. Define minimum expected improvement on metric e.g. 5% 
4. Define a statistical significance (confidence level) e.g. 95% 
5. Calculate sample size per variation 
6. Run test until sample size obtained, and evaluate if observed true difference

![](https://i.imgur.com/gSc4zc3.png)

* Traditional A/B testing has two distinct periods of exploration and exploitation 
* Multi-Armed Bandit testing is adaptive, and include periods of exploration and exploitation at the same time