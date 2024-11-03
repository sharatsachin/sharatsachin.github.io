---
title: AWS ML Speciality Notes (Part 3.2)
date: 2023-02-08 00:00:00 +0530
comments: true
categories: [aws, ml]
tags: [aws]
math: true
---

This post gives a quick review on the selecting the appropriate model(s) for a given machine learning problem

<!--more-->

# Select the appropriate model(s) for an ML problem

The four aspects of a problem used for model selection:
1. Data types and format
2. Learning paradigm or domain
3. Problem type
4. Use case examples

## [Linear regression](https://en.wikipedia.org/wiki/Linear_regression)

* linear approach for modelling the relationship between a scalar response and one or more explanatory variables (also known as dependent and independent variables)
    * only one input variable -> simple linear regression
    * more than one input variable -> multiple linear regression
* Cost function optimizes the regression coefficients or weights by measuring how a linear regression model is performing (finding the accuracy of the mapping function / hypothesis function). Eg : Mean Squared Error (MSE) cost function
* use gradient descent to update the coefficients of the line by reducing the cost function
* $$ R^{2} $$ ([co-efficient of determination](https://byjus.com/maths/coefficient-of-determination/)) is a statistical measure of fit that indicates how much variation of a dependent variable is explained by the independent variable(s) in a regression model
    * 0 to 1 (or 0 to 100%)
* Assumptions of Linear Regression
    * Linear relationship between the features and target
    * Small or no multicollinearity between the features
    * [homoscedasticity](https://www.statisticssolutions.com/free-resources/directory-of-statistical-analyses/homoscedasticity/): errors are of the same variance throughout
    * Normal distribution of error terms:
        * checked using the q-q plot. If the plot shows a straight line without any deviation, which means the error is normally distributed
    * No autocorrelations: occurs if there is a dependency between residual errors.
* polynomial regression : the original features are converted into polynomial features of required degree $$ 2,3,..,n $$ and then modeled using a linear model

## [Logistic regression](https://en.wikipedia.org/wiki/Logistic_regression)
* used for predicting the categorical dependent variable using a given set of independent variables
* uses logistic/sigmoid function $$ h_ \theta (x) =  \frac{\mathrm{1} }{\mathrm{1} + e^{-\theta^Tx}} $$
* it gives the probabilistic values which lie between 0 and 1
* Assumptions of Logistic Regression
    * The dependent variable must be categorical in nature.
    * The independent variable should not have multi-collinearity.

## [K-nearest neighbour](https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm)
* non-parametric, lazy learner algorithm
* for classification, the output is a class membership, object being assigned to the class most common among its k nearest neighbors
* for regression, the output is average of the values of k nearest neighbors
* larger values of k reduces effect of the noise on the classification, but make boundaries between classes less distinct
* Advantages : simple, robust, more effective for large training data
* Disadvantages : needs to determine the value of K, and computation cost is high because of distance calculation



## [Support Vector Machines](https://en.wikipedia.org/wiki/Support_vector_machine)
* maps training examples to points in space so as to maximise the width of the gap between the two categories
* new examples are then mapped into that same space and predicted to belong to a category based on which side of the gap they fall
* best decision boundary is called a hyperplane
* extreme points/vectors that help in creating the hyperplane are called support vectors
* kernel functions : linear, polynomial, gaussian radial basis functions, sigmoid ...

## [Naïve Bayes](https://en.wikipedia.org/wiki/Naive_Bayes_classifier)
* family of simple "probabilistic classifiers" based on applying Bayes' theorem with strong (naive) independence assumptions between the features
* advantage of naive Bayes is that it only requires a small number of training data to estimate the parameters necessary for classification

## K-means



## [Decision Trees](https://en.wikipedia.org/wiki/Decision_tree)

![](https://i.imgur.com/GYTYxxF.png)

* tree is built by splitting the source set, constituting the root node of the tree, into subsets—which constitute the successor children
* splitting is based on a set of splitting rules based on classification features
* process is repeated on each derived subset in a recursive manner called recursive partitioning
* recursion is completed when the subset at a node has all the same values of the target variable, or when splitting no longer adds value to the predictions
* Entropy is a measure of disorder or uncertainty
    * $$ E(S) = - \sum_{i=1}^{c} p_i \log_2 p_i $$
* we need a metric to measure the reduction of this disorder in our target variable/class given additional information (features / independent variables) about it. This is Information Gain
    * $$ IG(Y,X) = E(Y) - E(Y|X) $$

## Ensemble

### [Stacking](https://machinelearningmastery.com/stacking-ensemble-machine-learning-with-python/) 
* architecture of a stacking model involves two or more base models, often referred to as level-0 models, and a meta-model that combines the predictions of the base models, referred to as a level-1 model
* meta-model is trained on predictions made by base models on out-of-sample data
* data not used to train the base models is fed to the base models, predictions are made, and these predictions, along with the expected outputs, provide the input and output pairs of the training dataset used to fit the meta-model
* common approach to preparing the training dataset for the meta-model is via k-fold cross-validation of the base models, where the out-of-fold predictions are used as the basis for the training dataset for the meta-model
* appropriate when multiple different machine learning models have skill on a dataset, but have skill in different ways
* meta-model is often simple, such as linear and logistic

### [Blending](https://machinelearningmastery.com/blending-ensemble-machine-learning-with-python/)
* to describe the specific application of stacking where the meta-model is trained on the predictions made by base-models on a hold-out validation dataset. In this context, stacking is reserved for a meta-model that is trained on out-of fold predictions during a cross-validation procedure

### [Bagging](https://en.wikipedia.org/wiki/Bootstrap_aggregating)

* objective is to create several subsets of data from training sample chosen randomly with replacement
* each collection of subset data is used to train their decision trees
* we get an ensemble of different models
* average of all the predictions from different trees are used which is more robust than a single decision tree classifier
* Advantages: reduces over-fitting, handles higher dimensionality data, maintains accuracy for missing data
* Disadvantages: since final prediction is based on the mean predictions from subset trees, might be imprecise
* Random forests : bagging done on decision trees

### [Boosting](https://en.wikipedia.org/wiki/Boosting_(machine_learning))

* used to create a collection of predictors
* learners are learned sequentially with early learners fitting simple models to the data and then analysing data for errors
* consecutive trees are fit and at every step, the goal is to improve the accuracy from the prior tree
* when an input is misclassified by a hypothesis, its weight is increased so that next hypothesis is more likely to classify it correctly
* process converts weak learners into better performing model
* Advantages : supports different loss function, works well with interactions
* Disadvantages : prone to over-fitting, requires careful tuning of different hyperparameters

#### Gradient boosting 

* objective here is to minimize the loss function by adding weak learners using gradient descent. Since it is based on loss function, we’ll have loss functions like Mean squared error for regression and log-likelihood for classification.

### [XGBoost](https://xgboost.readthedocs.io/en/stable/tutorials/model.html)
* open-source implementation of the gradient boosted trees algorithm
* XGBoost minimizes a regularized (L1 and L2) objective function that combines a convex loss function (based on the difference between the predicted and target outputs) and a penalty term for model complexity (in other words, the regression tree functions)
* training proceeds iteratively, adding new trees that predict the residuals or errors of prior trees that are then combined with previous trees to make the final prediction. It's called gradient boosting because it uses a gradient descent algorithm to minimize the loss when adding new models

## [RNN](https://en.wikipedia.org/wiki/Recurrent_neural_network) 
* class of artificial NNs where connections between nodes can create a cycle -> allowing output from some nodes to affect subsequent input to the same nodes -> exhibit temporal dynamic behavior
* can use their internal state (memory) to process variable length sequences of inputs
* recurrent networks can have additional stored states, and the storage can be under direct control by the neural network -> referred to as gated state or gated memory, part of LSTM's and GRUs

## [CNN](https://en.wikipedia.org/wiki/Convolutional_neural_network)
* class of artificial NNs that use a mathematical operation called convolution in place of general matrix multiplication in at least one of their layers
* specifically designed to process pixel data and are used in image recognition and processing

## [Transfer learning](https://neptune.ai/blog/transfer-learning-guide-examples-for-images-and-text-in-keras)
* is about leveraging feature representations from a pre-trained model, so you don’t have to train a new model from scratch
* pre-trained models usually trained on massive datasets that are a standard CV benchmark
* including the pre-trained models in a new model leads to lower training time and lower generalization error
* useful when you have a small training dataset
    * you can use the weights from the pre-trained models to initialize the weights of the new model
* can also be applied to NLP problems

## [Incremental training](https://en.wikipedia.org/wiki/Incremental_learning)

* use the artifacts from an existing model and use an expanded dataset to train a new model
* saves both time and resources
* use it to:
    * train a new model using an expanded dataset that contains an underlying pattern that was not accounted for in the previous training and which resulted in poor model performance
    * use the model artifacts or a portion of the model artifacts from a popular publicly available model in a training job
    * resume a training job that was stopped
    * train several variants of a model, either with different hyperparameter settings or using different datasets
* built-in algorithms currently support incremental training: Object Detection, Image Classification, and Semantic Segmentation
* Recognition does not support incremental training


## [Collaborative filtering](https://en.wikipedia.org/wiki/Collaborative_filtering)
* is a technique used by recommender systems
* based on (user, item, rating) tuples
* unlike content-based filtering, it leverages other users’ experiences
* concept behind collaborative filtering is that users with similar tastes (based on observed user-item interactions) are more likely to have similar interactions with items they haven’t seen before
* provides better results for:
    * diversity (how dissimilar recommended items are)
    * serendipity (a measure of how surprising the successful or relevant recommendations are)
    * novelty (how unknown recommended items are to a user)