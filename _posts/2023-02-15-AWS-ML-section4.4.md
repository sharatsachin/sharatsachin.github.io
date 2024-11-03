---
title: AWS ML Speciality Notes (Part 4.4)
date: 2023-02-15 00:00:00 +0530
comments: true
categories: [aws, ml]
tags: [aws]
math: true
---

This post gives a quick review on recommending and implementing the appropriate machine learning services and features for a given problem.

<!--more-->

# Deploy and operationalize machine learning solutions

* use Inference Recommender to deploy your model to a real-time inference endpoint that delivers the best performance at the lowest cost
* deploy your model to SageMaker hosting services and get an endpoint
    * can be used for inference
    * fully managed and support autoscaling
    * scoped to an individual AWS account, not public
* SageMaker determines the account ID from the authentication token that is supplied by the caller
* to set up and deploy a web service that you can call from a client application that is not within the scope of your account, you can call a SageMaker model endpoint using Amazon API Gateway and AWS Lambda
* S3 bucket where the model artifacts are stored must be in the same region as the model
    * models are stored as model.tar.gz in the S3 bucket specified in `OutputDataConfig` `S3OutputPath` parameter of the `create_training_job` call
    * When model.tar.gz is untarred, it contains `model_algo-1`, which is a serialized Apache MXNet object

## Exposing endpoints and interacting with them

Steps :
1. Create a SageMaker model in SageMaker
2. Create an **endpoint configuration** for an HTTPS endpoint
    * defines names of models in production (variants) and the ML compute instances that you want SageMaker to launch to host each production variant
    * you can configure the endpoint to elastically scale the deployed ML compute instances
    * when you specify two or more instances, SageMaker launches them in multiple AZs
3. Create an HTTPS endpoint

You can:
1. Host a single model
2. Host multiple models/variants in one container behind one endpoint
3. Host multiple models/variants which use different containers behind one endpoint
4. Host models along with pre-processing logic as serial inference pipeline behind one endpoint
    * inference pipeline is a Amazon SageMaker model that is composed of a linear sequence of two to fifteen containers that process requests for inferences on data
    * can combine preprocessing, predictions, and post-processing data science tasks
    * you can use Spark and scikit-learn preprocessors to transform your data
    * fully managed

###  Inference pipeline
* SageMaker model that is composed of a linear sequence of **2 to 15** containers that process requests for inferences on data
* define and deploy any combination of pretrained SageMaker built-in algorithms and your own custom algorithms packaged in Docker containers
* combine preprocessing, predictions, and post-processing data science tasks
* fully managed
* within an inference pipeline model, Amazon SageMaker handles invocations as a sequence of **HTTP** requests

### Sagemaker Neo 
* capability of Amazon SageMaker that enables machine learning models to train once and run anywhere in the cloud and at the edge
* currently supports image classification models exported as frozen graphs from TensorFlow, MXNet, or PyTorch, and XGBoost models
* three advantages of using Amazon Neo with SageMaker models are:
    1. run ML models with up to 2x better performance
    2. reduce framework size by 10x
    3. run the same ML model on multiple hardware platforms

### IoT Greengrass 
* open-source edge runtime and cloud service for building, deploying, and managing device software
* provides pre-built components so you can easily extend edge device functionality without writing code

## ML model versioning

## A/B testing 
* SageMaker enables you to test multiple models or model versions behind the same endpoint using production variants
* you can test ML models that have been trained using different datasets, trained using different algorithms and ML frameworks, or are deployed to different instance type, or any combination of all of these
* you can distribute endpoint invocation requests across multiple production variants by:
    1. providing the traffic distribution for each variant
    ![](https://i.imgur.com/GcXbf0A.png)
    2. you can invoke a specific variant directly for each request
    ![](https://i.imgur.com/Ffeg3X4.png)
    
SageMakerVariantInvocationsPerInstance = (MAX_RPS * SAFETY_FACTOR) * 60
[AWS recommended](https://docs.aws.amazon.com/sagemaker/latest/dg/endpoint-scaling-loadtest.html) SAFETY_FACTOR = 0.5
    
https://docs.aws.amazon.com/sagemaker/latest/dg/deployment-best-practices.html

## Retrain pipelines
## ML debugging/troubleshooting
### Detect and mitigate drop in performance
### Monitor performance of the model
