---
title: AWS ML Speciality Notes (Part 1.2 and 1.3)
date: 2023-02-03 00:00:00 +0530
comments: true
categories: [aws, ml]
tags: [aws]
math: true
---

This post gives a quick review on identifying and implementing data ingestion and transformation solutions.

<!--more-->

# Identify and implement data ingestion and transformation solutions.

## Data job types (batch load, streaming)

### Batch processing
* wait for a certain amount of raw data to pile up before running an ETL job
* you should lean towards batch processing when:
    1. data freshness is not mission-critical
    2. working with large datasets and are running a complex algorithms
    3. you get access to the data in batches rather than in streams
    4. you are joining tables in relational databases
* tools:
    1. Hadoop frameworks for such as Spark and MapReduce
    2. relational databases such as Redshift and Google BigQuery

### Stream processing
* process data as it arrives, sub-second timeframes
* would typically not be stateful
* usually involve a relatively simple transformation
* stream processing is the right approach when:
    1. data is being generated in a continuous stream and arriving at high velocity
    2. sub-second latency is crucial
* tools:
    1. Spark Streaming, AWS Kinesis
    2. Apache Flink and Apache Flume


## Data ingestion pipelines (Batch-based ML workloads and streaming-based ML workloads)

[\[Course on AWS Kinesis by Johnny Chivers\]](https://youtu.be/_t3k6oX2mfc)

### Kinesis Data Streams 

[\[AWS FAQ\]](https://aws.amazon.com/kinesis/data-streams/faqs/)

![](https://i.imgur.com/bXdx4WN.png)

* you can build custom applications that process or analyze streaming data for specialized needs, streaming data from hundreds of thousands of sources
* manages the infrastructure, storage, networking, and configuration needed
* replicates data across three AZ's
* Shard - a sequence of data records in a stream
    * it supports 1 MB/second and 1,000 records per second for writes and 2 MB/second for reads or 5 API calls across all consumers
    * a producer puts data records into shards and a consumer gets data records from shards
    * consumers use shards for parallel data processing and for consuming data in the exact order in which they are stored
    * if writes and reads exceed the shard limits, the producer and consumer applications will receive throttles, which can be handled through retries
    * number_of_shards = $$ max (\frac{incoming\_write\_bandwidth_{KB/sec}}{1000}, \frac{outgoing\_read\_bandwidth_{KB/sec}}{2000}) $$
* Record - unit of data stored in a KDS stream, consists of:
    1. sequence number - unique identifier for each record, assigned by KDS
    2. partition key - used to segregate and route records to different shards of a data stream, specified by producer
    3. data blob - max size 1 MB
* modes- switch between them upto twice a day:
    1. provisioned - you specify the number of shards for the data stream
    2. on-demand - AWS manages the shards to provide the necessary throughput
* add data to a Kinesis data stream through PutRecord and PutRecords (up to 500 operations), Amazon Kinesis Producer Library (KPL), or Amazon Kinesis Agent
* consumer types :
    1. shared fan-out - all share a shard’s 2 MB/second of read throughput
    2. enhanced fan-out consumer types - each gets its own 2 MB/second allotment of read throughput, allowing parallel reads
* use managed services such as Lambda, Kinesis Data Analytics, and Glue to process data stored in Kinesis Data Streams
* also deliver data stored in Kinesis Data Streams to EC2, EMR clusters, Amazon S3, Amazon OpenSearch Service, Amazon Redshift, and custom HTTP endpoints using its prebuilt integration with Kinesis Data Firehose
* also build custom applications using Amazon Kinesis Client Library, a prebuilt library, or the Amazon Kinesis Data Streams API
* retention:
    * default - 24 hours
    * extended - upto 7 days
    * long term - upto 365 days
* comparison of using [KPL, KCL and Kinesis API](https://www.mlexam.com/kinesis-kpl-vs-api/)

### Kinesis Video Streams 

[\[AWS FAQ\]](https://aws.amazon.com/kinesis/video-streams/faqs/)

![](https://i.imgur.com/7VZ4Agh.png)

* stream media from connected devices to AWS for storage, analytics, machine learning (ML), playback, and other processing
* quickly build applications through integration with Rekognition Video, SageMaker, and libraries such as Apache MxNet, TensorFlow, and OpenCV
* also allows for time-encoded data like audio, RADAR, and LIDAR signals
* has only one producer publishing data into it
* Video playback capability
* Store data for 1 hour to 10 years
* can have multiple consuming applications processing the contents of the video stream
* one producer can generate one or more video streams
* publish via the PutMedia operation via the KVS SDK's
* use the GetMedia API to retrieve media content
* for live and on-demand playback, KVS provides capabilities for HTTP Live Streaming (HLS) and Dynamic Adaptive Streaming over HTTP (DASH)
* also supports ultra-low latency two-way media streaming with WebRTC

### Kinesis Data Analytics 

[\[AWS FAQ\]](https://aws.amazon.com/kinesis/data-analytics/)

![](https://i.imgur.com/vXEYZBg.png)

* transform and analyze streaming data in real time with Apache Flink
* scales automatically to match the volume and throughput of your incoming data
* Each application consists of three primary components:
    1. Input – The streaming source for your application
    2. Application code – series of Apache Flink operators or SQL statements that process input and produce output
    3. Output – one or more in-application streams to store intermediate results, then optionally configure an application output to persist data from specific in-application streams to an external destination
* Streaming data sources: Amazon MSK, KDS
* Destinations, or sinks: KDS, KDF, Amazon DynamoDB, Amazon Elasticsearch, and S3
* code in the notebook in your preferred language of SQL, Python, or Scala using Apache Flink’s Table API
* support two types of inputs: streaming data sources and reference data sources
* perform advanced analytics such as:
    1. RANDOM_CUT_FOREST function - anomaly detection, assign an anomaly score to each record based on values in the numeric columns
    2. RANDOM_CUT_FOREST_WITH_EXPLANATION - also gives explanation
    3. HOTSPOTS function - locate and return information about relatively dense regions in your data


### Kinesis Data Firehose 

[\[AWS FAQ\]](https://aws.amazon.com/kinesis/data-firehose/faqs/)

![](https://i.imgur.com/70icTRV.png)

* near real time ETL solution (processing and movement of real-time data from one place to another)
* buffers incoming data before delivering it - configure values for buffer size (1 MB to 128 MB) or buffer interval (60 to 900 seconds) whichever happens first.
* sources  - EC2 instances, Kinesis Data Stream, AWS Cloudwatch, AWS EventBridge, AWS IOT, AWS Pinpoint, AWS Lambda (write your Lambda function to send traffic from S3 or DynamoDB to KDF)
* destinations - S3, Redshift, OpenSearch, Splunk, Datadog, NewRelic, Dynatrace, Sumologic, LogicMonitor, MongoDB, and HTTP End Points
    * doesn’t deliver data to DynamoDB
* scales elastically, synchronously replicates data across AZs
* delivery stream stores data records for up to 24 hours
* maximum size of a record sent to Kinesis Data Firehose, before base64-encoding, is 1,000 KiB
* each delivery stream can intake up to 2,000 transactions/second, 5,000 records/second, and 5 MB/second
* add data to an Kinesis Data Firehose delivery stream through Kinesis Agent or Firehose’s PutRecord and PutRecordBatch operations
* PutRecord API uses the name of the delivery stream and the data record
* supports built-in format conversion from data raw or JSON into formats like Parquet/ORC
* transformations through Lambda (ex: CSV =\> JSON) 
* lets you to dynamically partition your streaming data before delivery to S3 using static or dynamically defined keys like 'customer_id'
* lets you compress your data before delivering it to Amazon S3 (supports GZIP, ZIP, and SNAPPY) (only GZIP for Redshift)
* can back up all un-transformed records to your S3 bucket concurrently
* not meant to be used for batch processing use cases and it **cannot** write data in RecordIO-Protobuf format

#### Summary 
* KDS can store data internally
* KDS and KDA cannot write directly to storage
* KDF and KVS can write directly to storage
* KDS and KDF can process data using Lambda
* KDF and KDA can change the data

### MSK – Amazon Managed Streaming for Apache Kafka 

[\[AWS FAQ\]](https://aws.amazon.com/msk/faqs/)

* streaming data service that manages Apache Kafka infrastructure and operations
* for developers and DevOps managers to run Apache Kafka applications and Kafka Connect connectors on AWS

Kafka is a streaming data store that decouples applications producing streaming data (producers) into its data store from applications consuming streaming data (consumers) from its data store.

### Glue

[\[AWS Glue course by Johnny Chivers\]](https://youtu.be/dQnRP6X8QAU) [\[AWS FAQ\]]()

* serverless data integration service, consists of:
    1. Data Catalog which is a central metadata repository (an Apache Hive Metastore, one in each region)
    2. ETL engine that can automatically generate Scala or Python code
    3. flexible scheduler that handles dependency resolution, job monitoring, and retries
    4. DataBrew for cleaning and normalizing data with a visual interface
        * 250 built-in transformations to combine, pivot, and transpose, filtering anomalies, correcting invalid, incorrectly classified, or duplicate data, normalizing data to standard date and time values, or generating aggregates for analyses
* AWS Glue Studio - graphical interface, after you define the flow of your data sources, transformations and targets, Glue Studio will generate Apache Spark code on your behalf
* supports data stored in Aurora, RDS, Redshift, DynamoDB and S3, as well as databases in VPC running on EC2, **supports data streams** from Amazon MSK, Kinesis Data Streams, and Apache Kafka, but not AWS Timestream
* you can have a scheduled trigger, on-demand trigger, or job completion trigger
* Glue crawler connects to a data store (that can be Amazon S3, RDS, Redshift, DynamoDB, or JDBC), progresses through a list of classifiers to extract the schema and statistics, then populates the Data Catalog
    * classifier reads data, if it recognizes the format of the data, it generates a schema, also returns a certainity number
* Glue's **FindMatches ML Transform** makes it easy to find and link records that refer to the same entity but don’t share a reliable identifier
* Glue vs Kinesis Data Analytics:
    * Glue - ETL use cases, run jobs on serverless Apache Spark-based platform
    * Kinesis Data Analytics - analytics use cases, run jobs on a serverless Apache Flink-based platform
* **cannot** write the output in RecordIO-Protobuf format
* a user can write custom Scala or Python code and import custom libraries and Jar files into Glue ETL jobs to access data sources not natively supported by AWS Glue, like ElastiCache

### Athena 

[\[AWS FAQ\]](https://aws.amazon.com/athena/faqs/)

* an interactive query service that makes it easy to analyze data in Amazon S3 using standard SQL
* serverless, so there is no infrastructure to manage, and you pay only for the queries that you run
* can run queries in parallel to analyze unstructured, semi-structured, and structured data stored in S3
* you get query results in seconds, leverages Hive for partitioning data
* two types of cost controls in Athena:
    * per-query limit (only 1 in a workgroup)
        * specifies the total amount of data scanned per query
    * per-workgroup limit (multiple possible for different time periods)
        * specifies the total amount of data scanned for all queries that run in this workgroup during the specified time period
* integrates with QuickSight for easy data visualization 

### EMR

[\[EMR course by Johnny Chivers\]](https://youtu.be/v9nk6mVxJDU) [\[AWS FAQ\]](https://aws.amazon.com/emr/faqs/)

* EMR:
    * uses the Hadoop data processing engine for computations in MapReduce programming model, customer defines `map()` and `reduce()` functions
    * service starts a customer-specified number of EC2 instances running Hadoop
    * master node divides input data into blocks, and distributes the processing of the blocks to the other nodes
    * each node then runs the map function on the data it has been allocated, generating intermediate data
    * intermediate data is then sorted and partitioned and sent to processes which apply the reducer function to it locally on the nodes
    * the output from the reducer tasks is collected in files
    * single “cluster” may involve a sequence of such MapReduce steps
* big data platform for data processing, interactive analysis, and machine learning using open source frameworks such as Spark, Hive, Presto
* deploy workloads to EMR using EC2, EKS, or Outposts
* orchestrate them using Amazon Managed Workflows for Apache Airflow (MWAA) or Step Functions
* develop, visualize and debug data science and data engineering applications written in R, Python, Scala, and PySpark in Amazon EMR Studio
* also develop a data processing job on your desktop, for example, using Eclipse, Spyder, PyCharm, or RStudio, and run it on Amazon EMR
* select JupyterHub or Zeppelin in the software configuration when spinning up a new cluster
* fully managed application with single sign-on, fully managed Jupyter Notebooks, automated infrastructure provisioning, and the ability to debug jobs without logging into the AWS Console or cluster
* debug clusters and jobs using as few clicks as possible with native applications interfaces such as the Spark UI and the YARN Timeline service
* EMR Notebooks is like Sagemaker Notebook
* EMR Studio is like Sagemaker Studio
* EMR Cluster - a collection of EC2 instances, called a node, three types:
    1. Master node: manages the cluster, coordinates distribution of data and tasks among other nodes for processing, every cluster has a master node
    1. Core node: run tasks and store data in the Hadoop Distributed File System (HDFS), multi-node clusters have at least one core node.
    1. Task node: only runs tasks and does not store data in HDFS, optional (can use spot instances)
* launch EMR cluster (version \>=5.23) with 3 master nodes and support high availability
* EMR is fault tolerant for node failures and continues job execution if a node goes down, provision a new node when a core node fails, will not replace nodes if all nodes in the cluster are lost
* while a cluster is running you may increase the number of core nodes and you may either increase or decrease the number of task nodes
* core nodes host persistent data in HDFS and cannot be removed, core nodes should be reserved for the capacity that is required until your cluster completes
* EMR Serverless is a new deployment option in EMR that allows you to run Spark and Hive without managing clusters
* Kinesis connector - lets EMR directly read and query data from Kinesis streams, perform batch processing of Kinesis streams using existing Hadoop ecosystem tools such as Hive, Pig, MapReduce, Hadoop Streaming, and Cascading

### Hadoop Ecosystem

* Hadoop - collection of software utilities that operate over a network of computers with software frameworks on a distributed storage environment in order to process the big data applications in the Hadoop cluster
* core components of Hadoop:
    1. MapReduce - data processing model designed in Java
        a. map - data divided into chunks such that they are converted into a new format which would be in the form of a key-value pair
        b. reduce - key/value pairs are reduced to tuples
    2. HDFS - primary storage unit in the Hadoop ecosystem, components:
        a. Name/Master node: centralized piece, stores the metadata
        b. Data/Slave Node: unit which stores the data
        c. Secondary Name Node: buffer to the Name Node
        * supports both vertical and horizontal scalability
        * has this unique replication factor
    3. YARN (Yet Another Resource Negotiator) - resource management and job scheduling
        a. Resource Manager / Master
        b. Node Manager / Slave  - to monitor the Status of the Container and App Manager.
        c. App Manager: manages data processing in the Container
        d. Container: Container is where the actual data processing takes place
    4. Common Utilities
* other components:
    1. Spark - in-memory cluster computing framework with lightning-fast agility,
        * it can perform real-time data streaming and ETL
        * can also be used for micro-batch processing
        * reusable in batch-processing environment
        * supports 80 high-level operators
        * EMR features Amazon EMR runtime for Apache Spark
            * a performance-optimized runtime environment for Apache Spark that is active by default on Amazon EMR clusters
        * [Spark also integrates with Sagemaker](https://docs.aws.amazon.com/sagemaker/latest/dg/apache-spark.html)
    2. Hive - data warehouse project, designed to provide SQL like queries to the databases (Hive QL)
        * is also used in performing ETL operations, HIVE DDL and HIVE DML
        * stores schema in a database and processed data into HDFS
        * designed for OLAP
    3. Impala - in-memory query processing engine
        * designed to integrate itself with Hive meta store and share table information between the components.
        * interactive, low-latency analytics
        * Impala executes SQL queries using a massively parallel processing (MPP) engine, while Hive executes SQL queries using MapReduce
    4. HBase - open-source, non-relational distributed database designed to provide random access to a huge amount of distributed data
        * modeled after Google's BigTable
    6. Pig - high-level scripting language
        * is operated by a SQL-like language called Pig Latin, which allows users to structure, summarize, and query data sources stored in Amazon S3
    7. Spark Streaming is basically an extension of Spark API
        * designed to provide scalable, high-throughput and fault-tolerant stream processing of live data streams
    8. Kafka - open source data stream processing software designed to ingest and move large amounts of data with high agility
        * uses Publish, Subscriber and Consumer model
        * can handle failures with the Masters and Databases
    9. Presto - open source, distributed SQL query engine, designed from the ground up for fast analytic queries against data of any size


### AWS Database Migration Service

[\[AWS FAQ\]](https://aws.amazon.com/dms/faqs/)

* designed to transfer data between databases, since it can also output data to S3, DMS can be used as a data ingestion tool
* source database can be on RDS, EC2 instance, on premises
* transfer is by transactions, hence reliable, rollback in case of failure
* can read and write from and to encrypted databases
* works in conjunction with AWS Schema Conversion Tool (SCT) 
* can be used for once off migration, continuous data replication, scheduled migration

### AWS Batch 

[\[AWS FAQ\]](https://aws.amazon.com/batch/faqs/)

* set of batch management capabilities that enables developers to run hundreds of thousands of batch computing jobs on AWS
* dynamically provisions the optimal compute resources based on the volume and specific resource requirements of the batch jobs submitted
* no need to install and manage batch computing software or server clusters
* used EC2 (with Spot) and AWS Fargate (with Fargate Spot)

### AWS Data Pipeline 

[\[AWS FAQ\]](https://aws.amazon.com/datapipeline/faqs/)

* provides a managed orchestration service that gives you greater flexibility in terms of the execution environment, access and control over the compute resources that run your code, as well as the code itself that does data processing
* launches compute resources in your account allowing you direct access to the EC2 instances or EMR clusters

### Amazon Simple Workflow Service 

[\[AWS FAQ\]](https://aws.amazon.com/swf/faqs/)

* build applications that coordinate work across distributed component
* managing intertask dependencies, scheduling, and concurrency in accordance with the logical flow of the application
* you implement workers to perform tasks
* SWF stores tasks and assigns them to workers when they are ready, tracks their progress, and maintains their state, including details on their completion

### AWS Quicksight 

[\[AWS FAQ\]](https://aws.amazon.com/quicksight/resources/faqs/)

Machine Learning Insights:
* **Anomaly detection**
* **Forecasting**
* Auto-narratives 

### AWS Step Functions 

[\[AWS FAQ\]](https://aws.amazon.com/step-functions/faqs/)