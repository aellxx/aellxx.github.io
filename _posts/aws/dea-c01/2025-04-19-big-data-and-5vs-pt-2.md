---
title: "Big Data & 5Vs - Part 2"
layout: post 
date: 2025-04-19
categories: [AWS, DEA-C01]
---

# ...Continuing 
This is part 2 of the Big Data & 5Vs series, where I explain the challenges of big data. You can find part 1 [here](/aws/dea-c01/2025/04/19/big-data-and-5vs-pt-1).

# Contents
- [3. Velocity](#3-velocity)
- [4. Veracity](#4-veracity)
- [5. Value](#5-value)
- [Summary](#summary)

Now let's continue from where we left off, starting with data *velocity*.

# 3. Velocity

Traditional data processing systems like RDBMS (relational database management systems), batch processing frameworks, and ETL (Extract, Transform, Load) pipelines were designed to handle predictable, structured data that arrived in manageable volumes. However, the digital landscape has shifted to having countless systems constantly generating massive amounts of data, such as IoT devices and social media platforms. This transformation has introduced a critical challenge: how can organizations process this unprecedented influx of data quickly enough to extract valuable insights?

To help organizations with handling the data velocity challenge, AWS offers some solutions. Let's dive into a few: 

## [Amazon EMR](https://aws.amazon.com/emr/)
Amazon EMR (Elastic MapReduce) is a cloud-based big data platform that simplifies running distributed data processing frameworks like Apache Hadoop, Apache Spark, and Presto on AWS. It allows users to focus on data analysis and processing the data without having to manage the underlying infrastructure. If you see any mention of processing massive amounts of data on parallelized compute clusters (e.g. EC2 instances, Amazon EKS clusters), there's a good chance that the answer is going to be something related to Amazon EMR. It helps address data velocity challenges in the following ways: 

- **Elastic & Scalable Infrastructure**: Amazon EMR automatically provisions resources to handle high-throughput real-time data processing.
- **Integration with Streaming Services**: Integrating with streaming services like [Kinesis](#amazon-kinesis) and [Amazon MSK](#amazon-msk-managed-streaming-for-apache-kafka) allow organizations to continuously process large volumes of incoming data with minimum latency. 

## [Amazon MSK](https://aws.amazon.com/msk/) (Managed Streaming for Apache Kafka)

Now I don't know about you, but when I first read what MSK stood for, the first thought that popped up in my head was,

> What is Apache Kafka?

If I start a new series called *Things-I-should-know-but-I-don't*[^1], all the Apache-x services would make it in there, but for the sake of simplicity, here's a sentence that captures the essence of what Apache Kafka does: 

> Apache Kafka is a distributed data store[^2] that enables organizations to publish, store, and process large volumes of real-time streaming data

So basically, it's something that uses a bunch of computers to handle data that's live-streaming into your organization. 

Enough said (for now), let's see how Amazon MSK helps address data velocity challenges: 
- **Fully Managed Service**: As with many AWS services, Amazon MSK manages the operational burden of running Kafka clusters, allowing users to focus on processing and analyzing data streams.
- **Apache Kafka**: Kafka handles real-time processing at scale with fault tolerance.
- **Real-Time Architecture**: Data is processed as it arrives. 
- **Support for Stream Analysis**: MSK can process high-velocity data that requires immediate processing.

## [Amazon Kinesis](https://aws.amazon.com/kinesis/)
Amazon Kinesis is a managed service for collecting, processing, and analyzing real-time streaming data at scale[^3]. As with many AWS-provided services, it is fully managed. Kinesis ingests and stores data from real-time streams, reducing the time between data generation and analysis. 

From an exam perspective, there are three data streaming services in AWS that you should be able to distinguish between. The three services are **Amazon Kinesis Data Streams**, **Amazon Kinesis Data Firehose**, and **Amazon Managed Service for Apache Flink**.

| -- | Amazon Kinesis Data Streams | Amazon Kinesis Data Firehose | Amazon Managed Service for Apache Flink |
| Core Functionality | real-time data streaming for custom processing | Simplify delivering streaming data to destinations | Advanced stream processing with Apache Flink | 
| Data Retention | 24 hrs - 1 year | None | None |
| Use Cases | low-latency, ordered processing | loading data into destinations like S3 or Redshift | Analytics, aggregations, and stream pattern detection | 
| Processing Timing | Real-time | Near real-time | Real-time analytics |

> The biggest flag determining the answer between Kinesis Data Streams and Kinesis Data Firehose is *processing timing*, with real-time processing indicating Kinesis Data Streams and near-real-time processing indicating Kinesis Data Firehose. 


## [AWS Lambda](https://aws.amazon.com/lambda/)
AWS Lambda is a serverless[^4] computing service that automatically runs your code without having to provision or manage servers. Typically, Lambdas immediately run in response to events, which makes them suitable for use cases where some business logic needs to respond instantly to a data event (e.g., new data arrived, data changes). Given the event-driven characteristic, Lambdas are ideal solutions for use in real-time data processing, analytics, and data transformations. 

# 4. Veracity
According to the Google Oxford Languages Dictionary, "veracity" is defined as "conformity to facts; accuracy." Its synonyms include "truthfulness," "accuracy," and "correctness." Data veracity is grounded on data integrity, which is the assurance that data remains complete, accurate, and reliable throughout its lifecycle. 

The data veracity challenge addresses the need to ensure data integrity at all phases of the data lifecycle.

## [Amazon EMR](https://aws.amazon.com/emr/)
Amazon EMR can handle data velocity, but it also addresses veracity challenges. Because it is a fully managed service, it enables you to focus on implementing data quality checks on data transformation and analysis operations. EMR also allows for comprehensive data quality management across the entire data pipeline, given that it can integrate seamlessly with other AWS services. 

## [AWS Glue](https://aws.amazon.com/glue/)
AWS Glue is a serverless ETL (Extract, Transform, Load) service. It addresses data veracity challenges in these specific ways: 
- **Cleaning & Normalization**: Clean and normalize data directly from sources like data lakes, warehouses, and databases
    - Data cleaning identifies, corrects, and/or removes inaccurate, incomplete, or irrelevant data points 
    - Data normalization helps maintain consistency across different sources by standardizing data formats and structures
    - Cleaning and normalization directly at the sources establishes data quality at the earliest possible stages of the data lifecycle
- **Real-time Data Quality**: Clean and transform streaming data in-flight before it enters the analytics phase
- **Glue Data Catalog**: AWS Glue Data Catalog serves as a centralized metadata repository for your data assets, such as their location, schema, and formats, across different data sources
    - The catalog can be used to track where the data originated and its transformation history
- **Glue Data Quality**: This is a feature of AWS Glue that specifically analyzes datasets and recommends data quality rules to ensure accuracy and reliability


## [AWS Glue DataBrew](https://aws.amazon.com/glue/features/databrew)
In a nutshell, **AWS Glue DataBrew is a visual, no-code data preparation tool** that enables users to clean, normalize, and transform data for analytics and machine learning using an interactive interface with over 250 prebuilt transformations.

Here are some ways AWS Glue DataBrew helps address the data veracity challenge: 

- **Visual Data Lineage Mapping**: Glue DataBrew provides transparency into data origins and transformation history all within a visual interface.
- **Prebuilt Transformations**: Offers 250 ready-made, no-code data transformation tools that can automate data cleaning tasks like anomaly filtering and invalid value correction.
- **Direct Source Connection**: Minimize how much your data is copied or moved around by directly connecting to your data lake, warehouse, and databases. 
    - Moving or copying data can introduce errors
- **Automated Preparation**: Automated data cleaning and normalization reduce the chance of introducing errors often associated with manual preparation. 

# 5. Value

Ok, I promise this is the last one. Well, it *is* the last one.

The last "V" of big data is *value*, and this has to do with the insights generated from the data. After all, that's the whole point of the entire data flow -- to extract valuable insights from data that drive business decisions. 

With massive amounts of data, we need to ensure that they actually lead to meaningful business benefits and actionable insights. There are three important processes that directly connect big data to business value by extracting insights from raw data: 

1. **Query**(ing) is the process of extracting specific information from large datasets, enabling you to focus on the relevant data that addresses your business problems or questions. 
2. **Reporting**: The relevant data is then organized into structured formats like tables or dashboards, which summarize key patterns or metrics. This makes it easier for stakeholders to monitor performance, track progress, and make informed decisions based on consistent, reliable data outputs.
3. **Visualization**: The final process of delivering value is visualizing the data into graphical representations (charts, graphs, maps, dashboards). After all, looking at a visual graph makes it way easier to identify trends/patterns than staring at a CSV with a bunch of numbers. In other words, effective visualization makes it easier to grasp insights and eventually leads to faster and more intuitive decision-making.

Given these three steps, the AWS services that help deliver value are as follows.

## [Amazon QuickSight](https://aws.amazon.com/pm/quicksight/)
Amazon QuickSight is AWS's business intelligence and data visualization service that helps users gain insight from data through analytics.

In a nutshell, QuickSight helps deliver value from data by:

- Providing a visual interface for users to easily create interactive dashboards and visualizations
- Connecting to both AWS and on-premise data sources
- Integrating with SageMaker to use ML models directly in visualizations

## [Amazon SageMaker](https://aws.amazon.com/sagemaker/)
Amazon SageMaker is a fully managed ML service on AWS that provides a suite of tools for building, training, and deploying ML models at scale. In the scope of delivering value, you can use your data as input to the models configured in SageMaker and end up with machine learning-driven data insights. 

## [Amazon Athena](https://aws.amazon.com/athena/)
Amazon Athena is a serverless interactive query service that allows users to run SQL queries directly against data in S3 buckets, without having to load it into a formal database. It brings data-driven value to organizations by simplifying the data analysis process on data lakes and reducing the extra time required to set up a database and/or configure complex ETL jobs. 

# Summary
You might have felt like you were drinking from a firehose.

Yes, that was a bunch of information, but you still made it!

I'm all about understanding things *intuitively*, and I hate it when I read some definition and just end up memorizing it word-for-word because I can't wrap my head around the idea. That was the case for me with my earlier certifications, and to be completely honest, I had to look up a lot of the services to write an explanation that (finally) stuck with me. To those who are cert-prepping or determined to internalize AWS concepts, I hope this helped at least a little. 

As always, thanks for reading till the end, and please don't hesitate to leave any questions, comments, feedback, or critiques in the comments below or email/LinkedIn messages. 

Until next time!

---


[^1]: Now that I think about it, I probably should...!

[^2]: Kafka stores and manages data across multiple servers (called brokers) in a cluster. Data is divided into topics and further split into partitions that are distributed and replicated among brokers, which ensures high scalability, fault tolerance, and parallel processing. This architecture enables data to be ingested, stored, and accessed in real time by many producers and consumers simultaneously, and the system can continue operating even if individual servers fail, since copies of the data exist on other brokers.

[^3]: If a system can handle data "at scale", it means a couple of things -- 
    - the system can handle <u>massive volumes</u> of data simultaneously
    - <u>automatically scale up</u> resources as data volume increases, without you having to manually provision resources.
    - maintain <u>reliable performance</u> even under heavy loads without failing
    - process and make data available for real-time analytics within <u>milliseconds</u>

[^4]: Lambda handles all the infrastructure management, so you don't have to spend time provisioning servers. It also automates scaling and monitoring, allowing you to just focus on implementing whatever business logic you need to write.

