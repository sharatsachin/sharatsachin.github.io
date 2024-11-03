---
title: AWS ML Speciality Notes (Part 3.3)
date: 2023-02-09 00:00:00 +0530
comments: true
categories: [aws, ml]
tags: [aws]
math: true
---

This post gives a quick review on the training of ML models.

<!--more-->

# Train machine learning models

## Train-test-validation split

#### Training Dataset: 
* sample of data used to fit the model
* actual dataset that we use to train the model (weights and biases)
* model sees and learns from this data

#### Validation Dataset
* sample of data used to provide an unbiased evaluation of a model fit on the training dataset while tuning model hyperparameters
* evaluation becomes more biased as skill on the validation dataset is incorporated into the model configuration
* the model occasionally sees this data, but never does it *learn* from this

#### Test Dataset
* sample of data used to provide an unbiased evaluation of a final model fit on the training dataset
* used once a model is completely trained (using the train and validation sets)
* test set is generally what is used to evaluate competing models
* contains carefully sampled data that spans the various classes that the model would face, when used in the real world

Duplicates can accidentally leak into validation and test sets when you split your data.  This can cause artificially better performance on validation and test sets.  You should clean up the data so that all examples are distinct.

## Cross validation

Just read [this](https://scikit-learn.org/stable/modules/cross_validation.html) article.

![](https://i.imgur.com/8xLBLyY.png)

#### KFold 
* divides all the samples in $$ k $$ groups of samples, called folds (if $$ k = n $$, this is equivalent to the Leave One Out strategy), of equal sizes (if possible). The prediction function is learned using $$ k-1 $$ folds, and the fold left out is used for test.

#### StratifiedKFold 
* variation of k-fold which returns stratified folds: each set contains approximately the same percentage of samples of each target class as the complete set.

#### GroupKFold
* ensures that the same group is not represented in both testing and training sets
* if the data is obtained from different subjects with several samples per-subject and if the model is flexible enough to learn from highly person specific features it could fail to generalize to new subjects

## Training

#### Optimizer
* to associate loss function and model parameters together by updating the model, i.e. the weights and biases of each node based on the output of the loss function
* attempt to reach a global minima with respect to loss function
* [different optimizers](https://ml-cheatsheet.readthedocs.io/en/latest/optimizers.html)

#### Activation Functions
* live inside NN layers and modify the data they receive before passing it to the next layer
* allowing NNs to model complex non-linear relationships
* include ReLU and sigmoid
* properties : non linear, continuously differentiable, and have a fixed range
* [different activation functions](https://ml-cheatsheet.readthedocs.io/en/latest/activation_functions.html)

#### Loss/Cost Functions
* tells us “how good” the model is at making predictions for a given set of parameters
* has its own curve and its own derivatives
* slope of this curve tells us how to change our parameters to make the model more accurate
* can take a variety of forms: MSE (L2) and Cross-entropy loss
* [different loss functions](https://ml-cheatsheet.readthedocs.io/en/latest/loss_functions.html)

## Training jobs in Amazon SageMaker

* [source](https://docs.aws.amazon.com/sagemaker/latest/dg/how-it-works-training.html) ![](https://i.imgur.com/driTHqw.png)
* ![](https://i.imgur.com/h55rV1f.png)


* training job includes the following information:
    1. S3 bucket URL with training data
    2. compute resources for model training, managed by SageMaker
    3. S3 bucket URL to store output of the job
    4. ECR path where the training code is stored
* options for a training algorithm:
    1. an algorithm provided by SageMaker
    2. Apache Spark with SageMaker—SageMaker library that you can use in Apache Spark to train models with SageMaker
    3. Submit custom code to train with deep learning frameworks
    4. Use your own custom algorithms
    5. Use an algorithm that you subscribe to from AWS Marketplace
* get predictions in the following ways:
    1. persistent, real-time endpoints -> SageMaker Real-time inference
    2. workloads that have idle periods -> Serverless Inference.
    3. requests with large payload sizes up to 1GB, long processing times, and near real-time latency requirements -> SageMaker Asynchronous Inference
    4. predictions for an entire dataset -> SageMaker Batch Transform

### Attributes required for CreateTrainingJob
[Link](https://docs.aws.amazon.com/zh_tw/sagemaker/latest/dg/API_CreateTrainingJob.html) .. the only "Required: Yes" attributes are:
1. AlgorithmSpecification (in this TrainingInputMode is Required - i.e. File or Pipe)
2. OutputDataConfig (in this S3OutputPath is Required - where the model artifacts are stored)
3. ResourceConfig (in this EC2 InstanceType and VolumeSizeInGB are required)
4. RoleArn (..The Amazon Resource Name (ARN) of an IAM role that Amazon SageMaker can assume to perform tasks on your behalf...the caller of this API must have the iam:PassRole permission.)
5. StoppingCondition
6. TrainingJobName (The name of the training job. The name must be unique within an AWS Region in an AWS account.)

## Running a training job using containers

Links - [\[1\]](https://docs.aws.amazon.com/sagemaker/latest/dg/adapt-training-container.html), [\[2\]](https://sagemaker-examples.readthedocs.io/en/latest/advanced_functionality/tensorflow_bring_your_own/tensorflow_bring_your_own.html), [\[3\]](https://github.com/aws/sagemaker-training-toolkit), [\[4\]](https://sagemaker-workshop.com/custom/containers.html)

File structure for training
```
/opt/ml
|-- input
|   |-- config
|   |   |-- hyperparameters.json
|   |    -- resourceConfig.json
|    -- data
|        -- <channel_name>
|            -- <input data>
|-- code
|   -- <code files>
|-- model
|   -- <model files>
 -- output
    -- failure
```

File structure for inference
```
/opt/ml
`-- model
    `-- <model files>
```

* For inference, containers need to implement a web server that responds to /invocations and /ping on port 8080
* must accept socket connection requests within 250 ms
* must respond to invocations requests within 60 seconds
* request timeout on /ping attempts is 2 seconds
    * container must respond with an HTTP 200 status code and an empty body.

## Compute choice

### CPU's vs GPU's for ML / DL

[Link](https://blog.purestorage.com/purely-informational/cpu-vs-gpu-for-machine-learning/)

### Distributed vs. non-distributed

### platform [Spark vs. non-Spark]

## Model updates and retraining

### Batch vs. real-time/online