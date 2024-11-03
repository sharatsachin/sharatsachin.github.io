---
title: AWS ML Speciality Notes (Part 4.1)
date: 2023-02-12 00:00:00 +0530
comments: true
categories: [aws, ml]
tags: [aws]
math: true
---

This post gives a quick review on building machine learning solutions for performance, availability, scalability, resiliency, and fault tolerance

<!--more-->

# Build machine learning solutions for performance, availability, scalability, resiliency, and fault tolerance

* highly available : system will continue to function even when any component of the architecture stops working
    * fault tolerance : applications will continue to function without degradation in performance, despite the complete failure of any component of the architecture
* have loose coupling : making sure application components are independent of each other
    * decouple your storage layer with your compute layer
    * helps turn off the compute resources when they are not needed
* use a queue service like Amazon SQS or workflow service like AWS Step Functions to create a workflow between various components

## AWS environment logging and monitoring

### CloudTrail and CloudWatch

* CloudTrail
    * captures API calls and related events made by or on behalf of your AWS account
    * delivers the log files to an Amazon S3 bucket that you specify
    * identify which users and accounts called AWS, source IP address from which the calls were made, and when the calls occurred
* CloudWatch
    * monitor your system, storing all the logs and operational metrics
    * used to keep a history of the model metrics for a specific amount of time, visualize model performance metrics with a CloudWatch dashboard
    * SageMaker has out-of-the-box integration with Amazon CloudWatch
        * collects near-real-time utilization metrics for the training job
        * CPU, memory, and GPU utilization of the training job container

### Build error monitoring
* training algorithm computes several metrics, such as training error and prediction accuracy
    * writes the values of these metrics to logs, which SageMaker monitors and sends to Amazon CloudWatch in real time
* you can view graphs of these metrics in CloudWatch
* when training job has completed, you can also get a list of the metric values that it computes in its final iteration by calling the `DescribeTrainingJob` operation.
* If the value of the objective metric for the current training job is worse than the median value of running averages of the objective metric for previous training jobs up to the same epoch, Amazon SageMaker stops the current training job.

## Multiple regions, Multiple AZs

## Docker containers

[Intro to Docker and containers](https://www.freecodecamp.org/news/a-beginner-friendly-introduction-to-containers-vms-and-docker-79a9e3e119b/)

## AMI/golden image
* Amazon Machine Image (AMI)
* supported and maintained image provided by AWS
* provides the information required to launch an instance
* can launch multiple instances from a single AMI when you require multiple instances with the same configuration
* use different AMIs to launch instances when you require instances with different configurations
* **golden** image is simply an image that you have customized to your liking with all necessary software/data/configuration information ready to go and then saved as a personal AMI from which you can launch instances
* **Deep learning containers** : Docker images preinstalled and tested with the latest versions of popular DL frameworks
    * lets you deploy custom ML environments quickly without building and optimizing your environments from scratch
    * available as Docker images in Amazon ECR, built for training or inference on a specific DL framework version, python version, with CPU or GPU support

[Comparison of file formats](https://www.adaltas.com/en/2020/07/23/benchmark-study-of-different-file-format/)

## Auto Scaling groups
* you define and apply a scaling policy that uses Amazon CloudWatch metrics and target values that you assign
* scaling plan uses dynamic scaling and predictive scaling to automatically scale your application’s resources
* optimize for availability, for cost, or a balance of both
* SageMaker automatically spreads instances across multiple AZs
* for production workloads, use at least two instances:
    * ensures high availability and provides individual fault tolerance
* use AWS Lambda and Amazon API Gateway to format the input request and invoke the endpoint from the web


## Rightsizing
* process of matching instance types and sizes to your workload performance and capacity requirements at the lowest possible cost
* process of looking at deployed instances and identifying opportunities to eliminate or downsize without compromising capacity or other requirements

### Provisioned IOPS
Provisioned IOPS volumes, backed by SSDs are the highest performance Elastic Block Store (EBS) storage volumes

## AWS best practices

* client application sends requests to the SageMaker HTTPS endpoint to obtain inferences from a deployed model
    * can also send requests to this endpoint from your Jupyter notebook during testing
* deploy multiple variants of a model to the same SageMaker HTTPS endpoint
    * useful for testing variations of a model in production
    * test a variation of the model by directing a small amount of traffic, say 5%, to the new model
    * create an endpoint configuration that describes both variants of the model
    * specify the `ProductionVariant` in your request to the `CreateEndPointConfig`
* configure a `ProductionVariant` to use Application Auto Scaling
* modify an endpoint without taking models that are already deployed into production out of service
    * add new model variants, update the ML Compute instance configurations of existing model variants, or change the distribution of traffic among model variants
    * to modify an endpoint, you provide a new endpoint configuration. SageMaker implements the changes without any downtime
* changing or deleting model artifacts or changing inference code after deploying a model produces unpredictable results
    * modify the endpoint by providing a new endpoint configuration
    * then change or delete the model artifacts corresponding to the old endpoint configuration
* to get inferences on entire datasets, consider Batch Transform
* reduce the latency for real-time inferences with SageMaker using an AWS PrivateLink deployment
    * keeps all inference traffic within your VPC
    * keeps invocation traffic in the same AZ as the client that originated it when using SageMaker : avoids the “hops” between AZs
* Elastic Inference (EI) is a service that allows you to attach low-cost GPU-powered acceleration to Amazon EC2 and Amazon SageMaker instances to reduce the cost of running deep learning inference by up to 75%
* use spot instances to train deep learning models using AWS Batch
    * [link](https://aws.amazon.com/blogs/hpc/ml-training-with-aws-batch-and-amazon-fsx/)