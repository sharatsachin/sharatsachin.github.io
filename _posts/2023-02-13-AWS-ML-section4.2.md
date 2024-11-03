---
title: AWS ML Speciality Notes (Part 4.2)
date: 2023-02-13 00:00:00 +0530
comments: true
categories: [aws, ml]
tags: [aws]
math: true
---

This post gives a quick review on recommending and implementing the appropriate machine learning services and features for a given problem.

<!--more-->

## ML on AWS (application services)

![](https://i.imgur.com/YMd5Qz9.png)

### Amazon Comprehend
* to identify the language of the text, extract key phrases, places, people, brands, or events, understand sentiment about products or services, and identify the main topics from a library of documents
* feed Amazon Comprehend a set of text documents, and it will identify topics (or group of words) that best represent the information in the collection
* Custom entity recognition extends the capability of Amazon Comprehend by enabling you to identify new entity types not supported as one of the preset generic entity types
    * in addition to identifying entity types such as LOCATION, DATE, PERSON, and so on, you can analyze documents and extract entities like product codes or business-specific entities that fit your particular needs


### AWS Deep Learning AMIs (DLAMI)
* customized machine instance preconfigured with NVIDIA CUDA and NVIDIA cuDNN, as well as deep learning frameworks, optionally
* options:
    1. DLAMI Type: CUDA versus Base versus Single-Framework versus Multi-Framework (Conda DLAMI)
    2. Compute Architecture: x86-based versus Arm-based AWS Graviton
    3. Processor Type: GPU versus CPU versus Inferentia versus Habana
    4. SDK: CUDA versus AWS Neuron versus SynapsesAI
    5. OS: Amazon Linux versus Ubuntu

### AWS DeepLens
* deep-learning enabled video camera for developers
* optimized to run machine learning models and perform inference on the device
* sample projects:
    1. Object Detection
    2. Hot Dog Not Hot Dog
    3. Cat and Dog
    4. Artistic Style Transfer
    5. Activity Detection
    6. Face Detection
    7. Bird Classification

### Amazon Forecast
* fully managed service that uses machine learning to deliver highly accurate forecasts, given any :
    1. historical set of time series data
    2. meta-data available for each of the time series

### Amazon Fraud Detector
* fully managed service that makes it easy to identify potentially fraudulent online activities such as online payment fraud and fake account creation
    1. define the event you want to assess for fraud
    2. upload your historical event dataset to S3
    3. select a fraud detection model type

### Amazon Lex
* service for building conversational interfaces using voice and text
* **speech recognition** and language understanding capabilities
* can handle both speech-to-text and handling the chatbot logic

### Amazon Polly
* text into lifelike speech
* Neural Text-to-Speech (NTTS) voices with new machine learning approach

### Amazon Rekognition
* Images : image recognition service that detects objects, scenes, and faces; extracts text; recognizes celebrities; and identifies inappropriate content in images
* Video : video recognition service that detects activities; understands the movement of people in frame; and recognizes objects, celebrities, and inappropriate content in videos stored in Amazon S3 and live video streams from Acuity

### Amazon SageMaker
* fully managed service to build, train, and deploy machine learning (ML) models for any use case with fully managed infrastructure, tools, and workflows

### Amazon Textract
* document analysis service that detects and extracts printed text, handwriting, structured data (such as fields of interest and their values) and tables from images and scans of documents
* confidence score and bounding boxes also returned
* three features – Forms, Tables and Queries
* English, Spanish, Italian, Portuguese, French, German
* handwriting, Invoices and Receipts, Identity documents and Queries processing are in English only


### Amazon Transcribe
* speech-to-text
* with Comprehend to perform sentiment analysis or extract entities and key phrases
* with Amazon Translate and Amazon Polly, customers can accept voice input in one language, translate it into another and generate voice output, effectively enabling multi-lingual conversations 
* with Amazon Transcribe with Amazon Kendra or Amazon Opensearch (successor to Amazon Elasticsearch Service) to index and perform text based search across audio/video library
* supports automatic content redaction or PII redaction for both batch and streaming APIs

### Amazon Translate
* translating text between supported languages
    * BLEU (bilingual evaluation understudy) is an algorithm for evaluating the quality of the text which has been machine-translated from one natural language to another. The range of this metric is from 0.0 (a perfect translation mismatch) to 1.0 (a perfect translation match).

### Amazon Kendra
* enterprise search service that’s powered by machine learning
* includes data from manuals, research reports, FAQs, human resources (HR) documentation, and customer service guides, which may be found across various systems

### Amazon Macie
* security service that uses machine learning to automatically discover, classify, and protect sensitive data in AWS
* recognizes sensitive data such as personally identifiable information (PII) or intellectual property and provides you with dashboards and alerts that give visibility into how this data is being accessed or moved
* continuously monitors data access activity for anomalies and generates detailed alerts when it detects the risk of unauthorized access or inadvertent data leaks
* protect data stored in Amazon S3.

## Infrastructure: (spot, instance types), cost considerations 
* fast launch instances are optimized to start under 2 mins.
* defaults: ml.t3.medium (CPU, f.l.), ml.g4dn.xlarge (GPU, f.l.)
* general purpose (no GPUs)
    * t3 / m5 / m5d
* compute optimized (no GPU)
    * c5
* memory optimized (no GPU)
    * r5
* accelerated computing (1+ GPUs)
    * p3 / p3dn / g4dn / g5
