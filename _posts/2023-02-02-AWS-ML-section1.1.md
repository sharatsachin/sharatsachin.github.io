---
title: AWS ML Speciality Notes (Part 1.1)
date: 2023-02-02 00:00:00 +0530
comments: true
categories: [aws, ml]
tags: [aws]
math: true
---

This post gives a quick review on creating data repositories for machine learning.

<!--more-->

# Create data repositories for machine learning

## AWS Lake Formation

[\[Video from AWS Events\]](https://www.youtube.com/watch?v=Aj5T5fcZZr0) [\[Documentation\]](https://docs.aws.amazon.com/lake-formation/latest/dg/what-is-lake-formation.html)

![](https://i.imgur.com/UX8ADcu.png)

- centralized, curated, and secured repository - stores data both in original form and prepared for analysis
- data warehouse can only store structured data whereas a data lake can store structured, semi-structured and unstructured data
- data lakes provides schema on read access, whereas data warehouse provides schema on write

## S3 (as storage for a data lake)

[\[S3 playlist by Be a Better Dev\]](https://www.youtube.com/playlist?list=PL9nWRykSBSFgTXMWNvNufDZnwhHrwmWtb) [\[Documentation\]](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)

![](https://i.imgur.com/801ZWtP.png)

* a key-based object store, stored in buckets with globally unique name
* objects have a key, their full file path
* objects range in size from minimum 0 bytes to a maximum **5 TB**
* bucket can have different objects in different storage classes:
    1. S3 Standard - general purpose storage for active, frequently accessed data
    2. S3 Intelligent-Tiering - automatic cost savings by auto-tiering data with any access pattern
    3. S3 Standard-IA - for data accessed less frequently, but requiring rapid access when needed
    4. S3 One Zone-IA - data with lower resiliency requirement, backup copies, disaster recovery copies, or other easily recreatable data, kept in a single AZ for cost savings
    1. S3 Glacier Instant Retrieval - data that is rarely accessed (once a quarter) and requires milliseconds retrieval times
    1. S3 Glacier Flexible Retrieval - data that does not require immediate access but needs the flexibility to retrieve large sets of data at no cost, with retrieval options from minutes to hours
    1. S3 Glacier Deep Archive - data that is accessed once or twice in a year
    2. S3 Outposts - for data residency or latency requirements
* on creation, only you have access to S3 buckets that you create
* you can set
    1. lifecycle **transition** policy to automatically migrate objects stored in the S3 Standard storage class to the S3 Standard-IA, S3 One Zone-IA, and/or S3 Glacier storage classes based on the age of the data
    2. lifecycle **expiration** policies to automatically remove objects based on the age of the data
* number of mechanisms for controlling access to S3 resources:
    1. **User based** - IAM - grant IAM users fine-grained control to their S3 bucket or objects
    2. **Resource Based** - Bucket Policies - bucket wide rules from the S3 console - allows cross account
        a. Object Access Control List (ACL) – finer grain
        b. Bucket Access Control List (ACL) – less common
    3. Query String Authentication - create a URL to an S3 object which is only valid for a limited time
    4. Amazon VPC - attach an **endpoint policy** to **Amazon VPC endpoint** that controls access to the S3 resources to which they are connecting
    5. S3 Block Public Access - provides settings for access points, buckets, and accounts to manage public access to S3 resources
    6. Tags (key / value pair – up to 10) – useful for security / lifecycle
* S3 Bucket Policies
    1. JSON based policies : 
        a. Resources: buckets and objects
        b. Actions: Set of API to Allow or Deny 
        c. Effect: Allow / Deny 
        d. Principal: The account or user to apply the policy to 
    2. Use S3 bucket for policy to: 
        a. Grant public access to the bucket
        b. Force objects to be encrypted at upload
        c. Grant access to another account (Cross Account)
*  securely upload/download your data to S3 via SSL endpoints using the **HTTPS** protocol
*  can use the Server-Side Encryption (SSE) option to encrypt data stored at rest using:
    1. SSE-S3 - Amazon handles key management and key protection
    2. SSE-C - lets S3 perform the encryption and decryption of your objects while you retain control of the keys used to encrypt objects
    3. SSE-KMS - lets AWS Key Management Service (AWS KMS) manage your encryption keys, provides an audit trail, helps comply with industry requirements
    4. S3 Encryption Client (Client side encryption) - you full end-to-end control of the encryption and decryption of objects
* you can create up to 100 buckets in each of your AWS accounts.
* can’t change its region after creation.
* you can host static websites by configuring your bucket for website hosting
* you can’t delete:
    * bucket using S3 console if bucket contains >= 100,000 objects
    * bucket using AWS CLI if versioning is enabled

### Amazon VPC Endpoint for S3
* logical entity within a VPC that allows connectivity to S3 over the Amazon global network. Two types:
    1. **gateway endpoints** are a gateway that you specify in your route table to access S3 from your VPC over the Amazon network
    2. **interface endpoints** extend the functionality of gateway endpoints by using private IPs to route requests to S3 from within your VPC, on-premises, or from a different AWS Region

## Amazon FSx for Lustre

[\[AWS FAQ\]](https://aws.amazon.com/fsx/lustre/faqs/)

![](https://i.imgur.com/WE8fa9c.png)

* fully managed shared storage built on Lustre, a popular high-performance parallel distributed file system, generally used for large-scale cluster computing
* ML use case is for serving massive training data to SageMaker - multiple compute instances can work on the data concurrently, data is lazy loaded only once
* low latency
* high throughput, IOPS
* integrates with EC2 (Sagemaker), ECS, EKS
* HDD storage option, with choice for different thoughput levels
* It is deployed Single-AZ - Persistent or Scratch modes

## Amazon EBS

[\[AWS FAQ\]](https://aws.amazon.com/ebs/faqs/)

![](https://i.imgur.com/Q01eowg.png)
* block-level storage service for use with EC2
* can deliver performance for workloads that require the lowest-latency access to data from a single EC2 instance
* two major categories of storage: 
    1. SSD-backed storage for transactional workloads (performance depends primarily on IOPS, latency, and durability)
    2. HDD-backed storage for throughput workloads (performance depends primarily on throughput, measured in MB/s)
* volume data is replicated across multiple servers in an AZ
* also possible to set up RAID configurations
* data can be backed via a snapshot for durability

## Amazon EFS

[\[AWS FAQ\]](https://aws.amazon.com/efs/faq/)

![](https://i.imgur.com/7tjQ9nj.png)
* file storage service for use with Amazon compute (EC2, containers, serverless) and on-premises servers
* concurrently accessible storage for up to thousands of EC2 instances
* four storage classes: 
    1. EFS Standard
    2. EFS Standard-IA
    3. EFS One Zone
    4. EFS One Zone-IA
* automatically moves your data to IA storage class according to the lifecycle policy you choose
* EFS Intelligent-Tiering also available, same as S3
* access EFS from EC2, ECS, EKS, **Lambda**, Sagemaker

## Amazon RDS (Relational Database Service)

[\[AWS RDS Hands on from Be A Better Dev\]](https://youtu.be/Ng_zi11N4_c) [\[AWS FAQ\]](https://aws.amazon.com/rds/faqs/)

![](https://i.imgur.com/ZeDw0Mw.png)

* managed service to set up, operate, and scale a relational cloud database 
* manages database administration tasks automatically
* works with PostgreSQL, MySQL, MariaDB, Oracle, SQL Server databases
* Amazon Aurora has its own versions of MySQL and PostgreSQL
* uses replication to enhance database availability and durability
* provides automated backups and database snapshots
* OLTP workloads (require quickly querying specific information, and support for transactions such as insert, update, and delete)

## Amazon DynamoDB

[\[AWS DynamoDB Intro from Be A Better Dev\]](https://youtu.be/2k2GINpO308) [\[AWS FAQ\]](https://aws.amazon.com/dynamodb/faqs/)


![](https://i.imgur.com/KyhNMtL.png)

* fast and flexible nonrelational database service
* automatically scales throughput capacity to meet workload demands
* partitions and repartitions your data as your table size grows
* synchronously replicates data across three facilities for high availability and durability
* supports GET/PUT operations by using a user-defined primary key
* tables, items, and attributes are the core components : 
    * table is a collection of items
    * item is a collection of attributes
* uses primary keys to uniquely identify each item in a table and secondary indexes to provide more querying flexibility. 

## Amazon Redshift

[\[Video from AWS Online Tech Talks\]](https://youtu.be/dfo4J5ZhlKI) [\[AWS FAQ\]](https://aws.amazon.com/redshift/faqs/)

![](https://i.imgur.com/bCDBe8z.png)

* fully managed, scalable cloud data warehouse using industry-standard SQL
* Redshift Serverless - serverless option of Redshift
    * makes it easy to run and scale analytics in seconds without the need to set up and manage data warehouse infrastructure
* Redshift Spectrum - feature of Redshift that lets you run queries against your data lake in S3, no data loading or ETL required
    * To access the data, it must be represented in a Catalog
* no such thing as a ~~LOAD~~ command, COPY is much faster than INSERT
* UNLOAD is used to write the results of a Redshift query to one or more text files on S3
* There are two services for in-place query with S3: Athena and Redshift Spectrum
    * Athena: ad-hoc data discovery and SQL querying.
    * Redshift Spectrum: more complex queries in data warehouse
* Only GZIP is supported if the data is loaded to Redshift. 

## Amazon Timestream

[\[AWS FAQ\]](https://aws.amazon.com/timestream/faq/)

![](https://i.imgur.com/qvZmiJ0.png)

* fast, scalable, and serverless time series database service for IoT and operational applications
* data is stored and queried by time intervals
* identify trends and patterns in your data in near real-time
* use SQL to query your time series data
* near real-time latencies for data ingestion

## Amazon DocumentDB

[\[AWS FAQ\]](https://aws.amazon.com/documentdb/faqs/)

![](https://i.imgur.com/nsoaPuM.png)

* fast, scalable, highly available, and fully managed enterprise document database service that supports MongoDB workloads
* store, query, and index JSON data
* use AWS Database Migration Service (DMS) to easily migrate on-premises or EC2 MongoDB databases to DocumentDB with virtually no downtime