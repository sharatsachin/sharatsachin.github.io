---
title: AWS ML Speciality Notes (Part 3.4)
date: 2023-02-10 00:00:00 +0530
comments: true
categories: [aws, ml]
tags: [aws]
math: true
---

This post gives a quick review on guidelines for hyperparameter optimization.

<!--more-->

# Perform hyperparameter optimization
* some methods:
    * manual trial and error
    * grid-search
    * random search
    * bayesian-optimization
* some libraries
    * Hyperopt
    * Scikit Optimize
    * Optuna

### [Parameters vs Hyperparameters](https://machinelearningmastery.com/difference-between-a-parameter-and-a-hyperparameter/)

##  Regularization

### Drop out
* technique where randomly selected neurons are ignored during training
* contribution to the activation of downstream neurons is temporarily removed on the forward pass and any weight updates are not applied to the neuron on the backward pass

### Early stopping
* technique used to reduce overfitting without compromising on model accuracy
* idea behind early stopping is to stop training before a model starts to overfit
* approaches: 
    1. Training model on a preset number of epochs
    2. Stop when the loss function update becomes small
    3. Validation set strategy
* only used during the training of a model and is not used when evaluating the model

### L1 regularization

* regression model that uses $$ L1 $$ regularization -> Lasso Regression
* $$ Loss_{L1} = Error(Y - \widehat{Y}) + \lambda \sum_1^n |w_i| $$
* if $$ \lambda = 0 $$ then loss functions becomes unregularized whereas very large value makes the coefficients (weights) zero hence it under-fits

### L2 regularization

* regression model that uses $$ L2 $$ regularization -> Ridge Regression
* $$ Loss_{L1} = Error(Y - \widehat{Y}) +  \lambda \sum_1^n w_i^{2} $$
* if $$ \lambda = 0 $$ then loss functions becomes unregularized whereas if lambda is very large then it will add too much weight and it leads to under-fitting
* scale the data before using Ridge regression

#### Note:
- $\lambda$ is a hyperparameter: bigger results in flatter and smoother model, more regularization 
- Lasso tends to completely eliminate the weights of the least important features (i.e., setting them to 0) and it automatically performs feature selection
- Last way to constrain the weights is Elastic net, a combination of Ridge and Lasso
- When to use which?
    * Ridge is a good default
    * If you suspect some features are not useful, use Lasso or Elastic
    * When features are more than training examples, prefer Elastic


##  Cross validation
* most authors, and empirical evidence, suggest that 5- or 10- fold cross validation should be preferred

##  Neural network architecture (layers/nodes), learning rate, activation functions

##  Tree-based models (# of trees, # of levels)

##  Linear models

### Learning rate
* controls how quickly the model is adapted to the problem
* smaller learning rates require more training epochs given the smaller changes made to the weights each update
    * too small can cause the process to get stuck, or take too long to converge
* larger learning rates result in rapid changes and require fewer training epochs
    * training may not converge or even diverge, after overshooting the minimum

### Batch size
* Smaller batch sizes can work their way out of local minima more easily, can also lead to faster updates in the model weights
* Batch sizes that are too large can end up getting stuck in the wrong solution
* Random shuffling at each epoch can make this look like very inconsistent results from run to run
* Small batch sizes add regularization while large batch sizes add less
* [Link](https://aws.amazon.com/blogs/machine-learning/the-importance-of-hyperparameter-tuning-for-scaling-deep-learning-training-to-multiple-gpus/) says batch size and learning rate should be adjusted by the same factor.
    * To minimize the effect of large batches, when increasing batch size, you also need to increase the learning rate by the same factor.  Similarly, if you decrease the batch size, you need to reduce the learning rate by the same factor.