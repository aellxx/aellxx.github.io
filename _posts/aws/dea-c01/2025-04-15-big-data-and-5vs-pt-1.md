---
title: "Big Data & 5Vs - Part 1"
layout: post 
date: 2025-04-19
categories: [AWS, DEA-C01]
---

# Intro

When I hear “big data” I get flashbacks to 2016, when I was preparing for a high school interview[^1]. Every week, the teachers in our grade took turns gathering the 10-ish students who were prepping for the same school in a classroom after our “cleaning” period, and we spent 45 minutes tackling mock interview questions. 

At one point, our mock question was: 

> What is Big Data?

Now at the time I new nothing of the tech-world I’m in now. I had no idea what coding was, and the only place I heard the term “data” was in my elementary school/6th grade math class when I was drawing graphs and identifying the mean/median/mode for homework. I don’t exactly recall what the collective answer was, but I do remember I did not understand one bit of what big data was. 

Fast forward 9 years, here I am writing a post about big data and its characteristics. 

# So What is Big Data?

According to my course materials at AWS Skill Builder, big data is data that is rapidly stored from a variety of sources, that is massive in size, and too complex to directly extract insights from. 

So given the job as a data engineer who’s responsible for collecting data and gaining actionable insights from it, you might have already sensed the challenges working with big data. Conveniently enough, these challenges all start with “V”s, and there are 5 of them, which explains the term “5Vs of Big Data”

# Contents
- [Intro to the 5Vs of Big Data](#intro-to-the-5vs-of-big-data)
- [1. Volume](#1-volume)
- [2. Variety](#2-variety)

# Intro to the 5Vs of Big Data

Let’s go back to the definition of big data that we defined in the previous section.

>  Data that is rapidly stored from a variety of sources, that is massive in size, and too complex to directly extract insights from

For me the following keywords stood out:

- rapidly stored
- variety of sources
- massive in size
- too complex

These keywords address the challenges with big data, which become the 5Vs. Just to keep myself in sync with my learning materials, I am going to switch up the order a little. Here are the challenges of big data — the 5Vs: 

- [Volume](#1-volume)
- [Variety](#2-variety)
- Velocity
- Veracity
- Value

Now let’s deep dive into each of these, and link them to the AWS services[^2] that helps address each V-challenge.

# 1. Volume

Volume refers to the *amount* of data that you have in your system. An organization can have terabytes to exabytes of data, and when we’re talking *that much data*, we need to be smart about managing it efficiently. 

A good data infrastructure should be scalable, secure, durable, flexible, and cost-effective. Select AWS services can help an organization achieve data infrastructure that inherits these characteristics.

## [Amazon S3](https://aws.amazon.com/pm/serv-s3/)

As mentioned in my last post about the Data Engineer’s Workflow & AWS Services, S3 is an **object storage solution that has virtually unlimited storage**. It is often the solution for establishing data lakes in AWS, since it can handle storing any type of data (structured, semi-structured, and unstructured). S3 is also the go-to for storing data for moving it in and out of AWS, given the AWS services that enable data transfers to and from the cloud. 

S3 approaches the big data volume challenge in the following ways: 

- **Unlimited Scalability**: Without the need to pre-provision resources, you can get virtually unlimited scalable storage.
- **Intelligent Tiering**: S3 has multiple storage tiers that optimize costs based on access patterns.
- **Parallelization**: S3 buckets can be accessed in parallel from any process without processes impacting each other’s performances.

## [Amazon Redshift](https://aws.amazon.com/redshift/)

While S3 excels at storing massive amounts of data, Amazon Redshift provides the **analytical capabilities needed to derive insights** from these volumes.

Amazon Redshift is a fast, fully-managed, and scalable cloud data warehouse[^3] service that enables high-performant, large-volume data analytics through massively parallel processing (MPP) and columnar storage.

Redshift tackles the data volume challenges in the following ways: 

- **Columnar Storage format[^4]**: Unlike row-based storage, columnar storage enables storing data on disk by compressing similar data elements together. This reduces storage requirements while optimizing queries to only scan specific columns needed, leading to a significantly faster analytical processing for large-volume datasets.
- **Massively Parallel Processing (MPP)[^5]**: The massively parallel processing architecture of Redshift splits data workloads across multiple compute nodes that process their own chunks simultaneously, which speeds up query execution time for complex analytics on large-scale data. 

## [AWS Lake Formation](https://aws.amazon.com/lake-formation/)

AWS Lake Formation is a service that **facilitates the process of creating, securing, and governing data lakes**[^6] by automating data ingestion, cataloging, and securing access to your data. 

AWS Lake Formation addresses the challenges of data volume in the following ways:
- **Centralized Repository**: AWS Lake Formation acts as a centralized repository for storing all types of data storage at any scale.
- **Comprehensive Management**: Lake Formation specifically helps manage massive data volumes by offering tools to
    - Ingest data from various sources
    - Clean and catalog the data
    - Transform the data for analysis
    - Secure the data appropriately
    - Make the data available for analysis and machine learning
- **Streamlined Migration**: The AWS Lake Formation console allows users to discover data sources, set up transformation jobs, and efficiently move data to Amazon S3 data lakes
- **Centralized Control**: Job orchestration and monitoring are centralized, which helps manage processing of large volumes of data.
- **Automated Compliance**: The system automatically configures underlying AWS services to ensure compliance with defined policies, streamlining the management of large-scale data.

# 2. Variety

Data variety refers to the diversity of data types and sources. For example an organization can collect data from multiple different sources, and such data will most likely have different formats. 

## Key Aspects of Data Variety
There are a few key aspects of the variety of data:
- **Different Data Types:** Data variety includes
	- **structured** data organized in databases and spreadsheets
	- **semi-structured** data like JSON, XML, or log files
	- **unstructured** data such as images, videos, and text
- **Multiple Sources:** Data can originate from a wide range of sources, such as external databases, and IoT sensors

Now because data is collected and formatted in so many different ways, Big data is prone to format discrepancies. In other words, the way that data is formatted can differ significantly across business sectors and systems, leading to challenges in integration and analysis.

## [Amazon RDS](https://aws.amazon.com/rds/)
Amazon RDS (Relational Database Service) is a service that **facilitates the setup, operation, and scaling of relational databases in the cloud**.

Here are some ways of how RDS addresses the challenges of data variety: 

- **Vendor Diversity**: RDS supports multiple RDBMS such as Amazon Aurora, MySQL, and PostgreSQL. This diversity allows organizations to maintain their preferred database technologies when migrating to the cloud.
- **Scalability**: RDS offers cost-efficient and resizable capacity, and can scale beyond single database instance constraints for read-heavy workloads. This addresses the variety challenge by providing flexible scaling options for different database needs.
- **Performance**: RDS implements row-based indexing to achieve the right performance for transactional workloads, allowing it to handle various data processing requirements efficiently.

By supporting multiple database vendors while providing scaling and performance optimization capabilities, Amazon RDS allows organizations to maintain necessary database variety while taking care of the heavy-lifting of managing a diverse set of database technologies.

## [Amazon Redshift](https://aws.amazon.com/redshift/)
Not only does Redshift address the [volume](#1-volume) issue of big data, it can also help tackle the challenges with data variety.

- **Cross-Departmental Analysis**: Redshift enables querying and analyzing data across all company departments, breaking down data silos.
- **Diverse Data Type Support**: Analyzes both structured data in the warehouse and semi-structured data in S3 data lakes through Redshift Spectrum.
- **Unified Visibility**: Makes all data visible and available for reports and dashboards, creating a single source of truth.
- **Advanced Analytics Integration**: Allows the use of all data lake content for machine learning and real-time analytics to predict outcomes.
- **Comprehensive Solution**: Connects databases, data lakes, and data warehouses into a cohesive analytics environment regardless of data format.

## [Amazon OpenSearch Service](https://aws.amazon.com/opensearch-service/)
Amazon OpenSearch Service is a fully managed **AWS service that runs and scales OpenSearch clusters without having to manage underlying infrastructure** (thus fully managed). It provides a fast, scalable system for search, analytics, and visualization of a diverse set of data. 

- **Search Versatility**: Amazon OpenSearch Service provides an open-source search suite that can handle diverse data types, making it ideal for organizations with high data variety.
- **Multiple Data Sources**: Supports multiple data integration points from various AWS services like Amazon S3 buckets, Amazon Kinesis Data Streams, and DynamoDB Streams, allowing organizations to process data from a varied data sources. 
- **Analytical Flexibility**: The service supports interactive log analytics, real-time application monitoring, and website search, allowing organizations to derive insights from different data varieties.
- **Visualization Tools**: The Dashboard feature provides visualization capabilities for different types of data, making it easier for users to understand and access diverse datasets. 
- **Managed Service**: Amazon OpenSearch Service eliminates the operational complexity required to handle diverse data types, allowing organizations to focus on analytics rather than managing infrastructure.

## [Amazon DynamoDB](https://aws.amazon.com/dynamodb/)
Amazon DynamoDB is a **fully managed NoSQL database service** that delivers fast, predictable performance with scalability for any type of semi-structured data.

- **Flexibility**: Amazon DynamoDB solves NoSQL database variety challenges by providing a fully managed service designed for semi-structured data storage, accommodating the diverse data formats that traditional RDBMS struggle with.
- **Scalability**: DynamoDB eliminates the scaling limitations of on-premises NoSQL systems by allowing companies to create database tables that can store and retrieve any amount of data and serve any level of request traffic without infrastructure concerns.
- **Consistency**: As the amount or type of data changes, DynamoDB maintains fast and predictable performance, ensuring that diverse data types don't impact system responsiveness.

# An Interlude...
At the risk of this post becoming too long, I decided to split the 5Vs of big data between two posts. In this post, we looked through the first 2 Vs of big data -- *volume* and *variety*. In part 2, we'll go over the last 3 -- *velocity*, *veracity*, and *value*. 

For those of you who made it to the end, thanks for reading, and please do not hesitate to reach out (or use the comments below!) with any feedback, critique, questions, or comments. 

I'll see you in the next post!

---

[^1]: For those of you who are curious, I didn’t get accepted to that high school 😂. Looking back I’m kind of glad I was rejected because I wouldn't have met my closest friends if it wasn't for the high school I graduated from.

[^2]: This is an AWS cert related post after all…

[^3]: A **data warehouse** is a centralized repository to store, integrate, and analyze large volumes of structured (and semi-structured) data from multiple sources. They enable businesses to perform efficient analysis, leading to informed, data-driven decisions. 

[^4]: Here’s the take:
    
    |  | Row-based | Columnar |
    | --- | --- | --- |
    | Storage | sequentially-stored rows | Separately stored columns |
    | Use Case | OLTP (transactions) | OLAP (analytics) |
    | Query Speed | Fast for full-row access | Fast for queries only needing specific columns |
    | Compression | least efficient | highly efficient |
    | I/O Efficiency | Read entire rows | Read only required columns |
    | Example | ATM withdrawls | Sales trend analysis |
    
    In summary, row-based storage is used for operations that focus on individual records (rows), whereas columnar storage focuses on aggregating values of a specific column.

[^5]: **Massively Parallel Processing** is a computing architecture that handles massive data volumes by splitting tasks across multiple compute nodes to process data chunks simultaneously. This process dramatically accelerates large-scale analytics.

[^6]: A **data lake** is a storage system that holds massive amounts of raw, unprocessed data in its original format. I like to think of it as a “data dump,” where you can dump your data without having to worry about structuring it or processing it so it adheres to a particular schema. 


