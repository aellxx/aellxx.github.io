---
title: "The Data Engineer's Workflow & AWS Services"
layout: post 
date: 2025-04-15
categories: [AWS, DEA-C01]
---

# The Data Engineer
A data engineer is responsible for collecting data, transforming it into a useful format, then serving it to their stakeholders. They are tasked with building the platforms and infrastructure necessary to extract insights from raw data. 

# The Workflow
The data engineer's workflow can be specified by the following building blocks, which we will explore one by one, associating the AWS services that enables each step: 
- [Ingest](#ingest)
- [Storage](#storage)
- [Catalog](#catalog)
- [Process](#process)
- [Deliver](#deliver)

## Storage
> Why start with storage? Didn't you JUST put ingest first on the list above?

Well, you do need *somewhere* to store your data before you start ingesting, which is why we need to establish a **data lake**.

A **data lake** is a centralized place to put your data whether it’s structured, unstructured, or semi-structured. I like to think of it as a "data dump", a place I don't need to worry about processing the "wild" data that comes in different formats. 

**S3 buckets** are a great choice because it can has virtually unlimited storage, and it can store all kinds of data sources. 

## Ingest
Once we have our data lake set up, we can start collecting data. Here is the non-exhaustive list of data ingestion sources in AWS: 

- **[AWS DMS](https://aws.amazon.com/dms/)** (AWS Database Migration Service): Load data from relational & non-relational databases to target database
- **[Amazon Data Firehose](https://aws.amazon.com/firehose/)**: For ingesting real-time data streams, converting their format (e.g. Parquet, ORC), decompressing the data, or performing custom data transformations
- **[Amazon MSK](https://aws.amazon.com/msk/)** (Managed Streaming for Apache Kafka): Build Apache Kafka clusters for handling real-time streaming data pipelines and applications
- **[IoT Core](https://aws.amazon.com/iot-core/)**: Connect to IoT devices and route messages to a wide range of AWS services
- **[AWS DataSync](https://aws.amazon.com/datasync/)**: Transfer data from your on-prem file shares, object storage systems, and Hadoop clusters to AWS Cloud storage services and periodically sync
- **[AWS Transfer Family](https://aws.amazon.com/aws-transfer-family/)**: Automate *file transfers* to S3 buckets using SFTP, FTPS, SFTP protocols
- **[AWS Snowball](https://aws.amazon.com/snowball/)**: Use when data transfer via network isn’t feasible — it only makes sense to have a physical data transfer machine

## Catalog
After we ingest our data and dump it all in our data lake, it's time to *catalog* our data. I had some trouble grasping my intuition around the idea of catalogging data so here's what I wrote up (feel free to skip this section).

### Data Catalog Analogy

A data catalog is like a library catalog that contains higher-level information about a book — its title, author, genre, ISBN, and publication date. When you want to search for a particular book, you most likely will search where the book is based on the catalog information.

Similar to how a library catalog contains high-level data, i.e. *metadata* about a book, a data catalog captures metadata about your data assets. It might contain information like the name of the data source, a description of the data table, column descriptions, the owner, and the last-updated date.

For example, a clinical researcher working in a hospital might need access to patient lab test results. He/she might use the data catalog to search for “blood tests,” from which the catalog’s search engine will scan the metadata (not the actual data itself) to return a list of potential data

A data catalog will act as the single source for the contents of your data lake. By having a central place for your data assets’ metadata, it will be easier for your organization to understand and keep track of the data you have.

In AWS, you can use **[AWS Glue Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/catalog-and-crawler.html)** to create a data catalog for your data lake. With a data catalog, you’ll be able to efficiently search and locate relevant data based on attributes like the name of the data asset or specific business terms. 

## Process
Cataloging your data will help you find the data you need, but the data may need further processing for you to be able to analyze or extract useful insights from it. AWS offers several options for transforming your data into applicable formats: 

- **[AWS Glue](https://aws.amazon.com/glue/)**: A fully managed ETL service that can prepare and cleanse data from a variety of sources. You can classify your data, extract schemas, and populate data catalogs.
- **[Amazon EMR](https://aws.amazon.com/emr/)**: Run big-data processing frameworks on EC2 clusters, Amazon EKS, or EMR serverless
- **[Amazon Managed Service for Apache Flink](https://aws.amazon.com/managed-service-apache-flink/)**: Use SQL code for filtering, aggregating, joining, and deriving real-time data

With these services, you can convert data types, filter, aggregate, standardize, and normalize your data so it is ready for analysis.

## Deliver
Once you acquire processed data, you can use it to extract and deliver insights to consumers and stakeholders. Here are the services you can use:

- **[Amazon Redshift](http://aws.amazon.com/redshift/)**: A data warehousing service you can use to analyze large sets of structured data across multiple databases and datasets without moving the data
- **[Amazon Athena](https://aws.amazon.com/athena/)**: A serverless query service that allows you to use SQL-syntax queries directly on S3 data lakes
- **[Amazon EMR](https://aws.amazon.com/emr/)**: A managed big data platform where you can run big data analytics frameworks like Apache Spark, Hive, Presto, and Flink on large datasets stored in S3 or DynamoDB
- **[Amazon OpenSearch Service](https://aws.amazon.com/opensearch-service/)**: [OpenSearch](https://opensearch.org/about/) is a distributed search and analytics engine that indexes your data, allowing you to efficiently search, filter, and analyze your data. Amazon OpenSearch Service allows you to deploy and scale OpenSearch clusters in the cloud without the management overhead, enabling data analysis from data sources like S3, Kinesis Data Streams, and DynamoDB
- **[Amazon QuickSight](https://aws.amazon.com/quicksight/)**: You can use SQL queries with Amazon QuickSight to visualize and analyze large datasets by creating charts, graphs, and dashboards.
- **[Amazon SageMaker](https://aws.amazon.com/sagemaker/)**: Amazon SageMaker is a fully-managed, cloud-based ML platform you can use to build, train, and deploy ML models for tasks like predictive analytics

## Security and Governance

The entire ingest → storage → catalog → process → deliver workflow must be implemented with security and governance in mind. Whether in-transit or at-rest, the data must be secure and controlled only by authorized users. Security refers to data being secured and accessible only by authorized users, while governance refers to the policies, procedures, and processes that ensures data is properly managed and used. 

Following are the AWS services that enforce security and governance in the workflow

- **[AWS Lake Formation](https://aws.amazon.com/lake-formation/)**: Simplify the creation, security, and management of data lakes. Configure fine-grained access permissions and ensure data confidentiality when sharing data within and outside your organization.
- **[AWS IAM](https://aws.amazon.com/iam/)**: Manage fine-grained access and permissions for users, roles, groups, etc.
- **[AWS KMS](https://aws.amazon.com/kms/)**: Create and manage data encryption keys for data in-transit and at-rest
- **[Amazon Macie](https://aws.amazon.com/macie/)**: Discover and protect sensitive data in AWS, such as PII (Personal Identifiable Information)
- **[Amazon DataZone](https://aws.amazon.com/datazone/)**: Catalog, discover, share, and govern data stored in the AWS cloud, on-prem, and/or 3rd party sources
- **[AWS Audit Manager](https://aws.amazon.com/audit-manager/)**: Audit AWS usage to facilitate rick and compliance evaluation

# Closing
Ok that was a loooooooong list of just me spilling out a bunch of AWS services. I'll admit it's not always the most interesting thing to read a list of services, but when it comes to taking the test, you do kinda *have to* know the services. I won't say there's a need to *memorize* these services, but aim for a state where if you see a service as one of the multiple choice options, you can at least get an idea of what it is (cert tip of the day). 

That's it for today!
As always, thank you if you made it to the end. 