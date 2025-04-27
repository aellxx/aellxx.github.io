---
title: "Data Warehouse vs. Data Lake"
layout: post
date: 2025-04-26
categories: [AWS, DEA-C01]
---

# TL;DR

I myself included, we all love a good TL;DR right smack at the start. Here's the one for this post.

> - A **data lake** is the go-to, single collection for accessing all sorts of data from across your organization.
> - A **data warehouse** is the single collection of structured data from across your organization designed for data analysis.

# Contents

- [The Premise of All This](#the-premise-of-all-this)
  - [Data Silos](#data-silos)
  - [The Solution?](#the-solution)
- [Data Lakes](#data-lakes)
  - [AWS Lake Formation](#aws-lake-formation)
- [Data Warehouses](#data-warehouses)
  - [Amazon Redshift](#amazon-redshift)
- [Data Lakes vs. Data Warehouses](#data-lakes-vs-data-warehouses)

# The Premise of All This

Imagine you're working at an e-commerce organization with multiple departments -- sales, marketing, finance, etc. Because each department focuses on its own thing, they collect and store their data independently:

- **Sales**: Tracks customer interactions in their CRM[^1] system
- **Marketing**: Runs campaigns and collects metrics like page views or marketing email click rates in their marketing platform
- **Finance**: maintains financial records in their accounting software

Each of these systems serves its specific purpose well. After all, if you are an accountant working in the finance department, it's not every day you need to know about what's being stored in the sales team's CRM system. However, if you might run into challenges when the company wants you to answer cross-departmental questions or gain organization-wide insights like:

- Which marketing campaigns generate customers with the highest lifetime value?
  - marketing campaign data + sales transaction history + customer retention
- How do shipping delays impact customer retention rates?
  - logistics/operations data + future purchasing behavior

First of all, because data is trapped in separate departmental systems, there is no single view that shows all the cross-organizational data. If you were to create that single view, it's probably going to be challenging to find a common denominator and combine the information. What adds to the challenge is inconsistent formatting: your marketing data might be structured nicely in a table format, while your sales data is in JSON.

Now let's _assume_ you somehow successfully joined all the data across your organization into one place. What if the individual data stores are updated pretty often, all at different times? It's probably going to take forever to just update, transform the original data, and load it into the single source before you can even start extracting insights.

This is going to be your worst nightmare when you're required to do this job every week.

## Data Silos

This fragmentation of data across isolated systems is what technology professionals refer to as _data silos_. In a nutshell, data silos are collections of data that is controlled by one particular department and isolated from the rest of the organization.

## The Solution?

_Data warehouses_ and _data lakes_ emerged as solutions to these problems, each with different approaches to getting data from all over the place, into a single, centralized collection. We'll do deep dives into each of these, but both aim to break down cross-departmental barriers and enable organization-wide analytics and insights.

Many large organizations probably has multiple departments, and every department is probably responsible for a different section of the business. Now, because every department has its own responsibilities, they might have their own data storage solution.

In this post, I'll go over data lakes and data warehouses, how they are different, and (obviously) link them to the corresponding AWS services. I'll probably have separate posts for the AWS services, since explaining the services in this post will make this too long or anyone to read until the end.

Let's start with data lakes.

# Data Lakes

> A data lake is the go-to, single collection for accessing all sorts of data from across your organization.

So why should you even _care_?

Well, I like to think of data lakes as a "dump & go" solution, where you don't need to worry about transforming your data to be in a specific, unified format. Regardless of whether you have document data, relational data, or key-value data, you can just dump it all in your data lake and not worry about unifying the format (which is usually the hard part). Because of this nature, data lakes provide a single source of truth and allow for faster analysis with their ability to store data in their raw formats.

Now we have a way of bringing data from all over the place into one location, and with every problem solved comes yet another problem. The problem with our big data dump is this:

> When all your data lives in one place without strict organizational policies, it becomes a governance/management nightmare.

Here are some of the nightmares you might face with the piles of data:

- How are you going to determine who can/can't access data from each source?
- How are you going to manage conflicts with data sources?
- How do you track data lineage -- where your data came from, how it's been transformed, and where it's being used?
- How do you secure sensitive information like PII[^2] or PHI[^3]?
- How do you maintain data integrity, making sure it is accurate?

So with these questions in mind, let's see what AWS has to provide.

## AWS Lake Formation

What is AWS Lake Formation?

> Lake Formation is a _serverless_ and fully-managed[^4] service that helps you build data lakes based on S3.

So _how exactly_ does Lake Formation help?

> Lake Formation simplifies data ingestion, cataloging, cleaning, and transformation of data from different sources. It facilitates data lake management by using machine learning and policies to automate the processes of securing, organizing, and cataloging data. It addresses governance challenges by integrating with AWS services like Glue, Athena, IAM, and CloudTrail. With these features, you can build a secure and governed data lake, which accelerates the time to extract insights from your data.

As I mentioned in the [premise](#the-premise-of-all-this), I won't get into specifics of AWS Lake Formation as a service in this post, but I promise I'll break apart the TL;DR in a separate, dedicated post really soon.

Let's switch gears to data warehouses now.

# Data Warehouse

> A data warehouse is another single collection of cross organizational data, that is specifically designed and optimized for running data analytics jobs on structured data. This centralized approach reduces the time it takes for businesses to make data-driven decisions.

_What happens if you don't have a data warehouse?_

It might probably take you forever to get a data-driven solution to your business problem that requires data from all over your organization.

Great, now that we have a sense of why we need a data warehouse, why don't we just go ahead and set up an on-premise warehouse?

Well, the short answer is that as with many on-prem systems, you're probably going to incur a bunch of maintenance costs.

As many cloud services help solve the problem, you're highly likely to spend more money on resources (e.g., people, servers) and simply just maintaining the data warehouse. In other words, the warehouse is going to incur costs by simply just _existing_, not even storing or analyzing your data. Additional challenges include limitations to scaling resources when you do/don't need them, and a lack of availability (e.g.,, when there's an earthquake and a data center goes down completely).

So what does AWS suggest you use?

## Amazon Redshift

In a nutshell, Amazon Redshift is a fully managed[^4] data warehouse solution in AWS

Again, for the purpose of this post, I'll only briefly go over how Redshift solves the challenges of setting up data warehouses on-premise. I **will** come back with a detailed post on Redshift.

With that said, here's how Redshift can save your precious time, money, and data:

- **Cost Efficiency**: You save money by paying for the resources you use. For example, the servers that run your queries and the storage capacity for holding your data.
- **Scalability**: Compute and storage servers can scale separately. For example, if the number of concurrent queries increases while your data capacity remains the same, you can just scale the compute resources.
- **Availability & Durability**: Your data will be replicated across multiple servers that span multiple availability zones, keeping away from a single point of failure.

# Data Lakes vs. Data Warehouses

Now that we have a sense of what a data lake and a data warehouse are, let's compare these solutions. I love me a good table structure without too many words, so here it is:

|                   | Lake                                              | Warehouse                                              |
| ----------------- | ------------------------------------------------- | ------------------------------------------------------ |
| Data              | structured, semi-structured, unstructured formats | Only structured data                                   |
| Schema            | Schema-on-read                                    | Schema-on-write                                        |
| Preprocessing     | ELT (extract, load, transform)                    | ETL (extract, transform, load)                         |
| Query Performance | Slow                                              | Fast                                                   |
| Data Quality      | Depends, but not guaranteed to be high            | High                                                   |
| Analytics         | ML, EDA, Data discovery                           | Batch reporting, Business Intelligence, Visualizations |
| Flexibility       | High                                              | Lower than data lakes                                  |

For those of you who understand the table in one go, awesome awesome. You're done reading this post.

If you're like me and need more explanation, then here it is:

## Data

- **Data Lake**: The whole point of data lakes is to have a single collection of your raw data, data lakes can handle storing data in all different formats: structured, semi-structured, and unstructured.
- **Data Warehouse**: Data warehouses require data to be structured before it is stored in the warehouse, since the data needs to be ready for analytics.

## Schema

- Data **Lake** -> **schema-on-read** strategy:
  - The schema -- a.k.a. the structure -- of the data is applied only when the data is being read, rather than when it is first stored. This is what enables data lakes to store data in their original formats.
- Data **Warehouses** -> **schema-on-write** strategy:
  - The data must adhere to a pre-specified schema before it is stored in the warehouse. This ensures the data stored in the warehouse is structured.

## Preprocessing

- **Data Lakes** implement an ELT (extract, load, transform) approach.
- **Data Warehouses** implement an ETL (extract, transform, load) approach.

## Query Performance

- **Data Lakes** prioritize providing a cost-effective storage solution over query performance. You can still access the data, just not as fast as data warehouses.
- **Data Warehouses**, in nature, are designed to optimize database queries.

## Data Quality

- **Data Lakes**: The quality of data in data lakes will depend on the quality of the raw data, since data lakes store data in its original format.
- **Data Warehouses**: The data will be pre-confined to a schema, ready for analysis.

## Analytics

- **Data Lakes**
  - ML (machine learning) workflows often require large and diverse datasets, which data lakes provide.
  - EDA (exploratory data analysis) benefits from data lakes' ability to quickly ingest and analyze new data.
- **Data Warehouses**
  - BI (business intelligence) relies on high-quality data, one that is accurate, consistent, and timely.
  - Batch processing aligns with typical business reporting needs, where periodic updates are sufficient for stability and accuracy.

## Flexibility

- **Data Lakes** offer more flexibility -- due to adopting a schema-on-write and ELT approach.
- **Data Warehouses** are less flexible because the data must be transformed to conform to a set schema before it can be stored.

# Closing

That is it for this post on comparing data lakes and data warehouses. We went over each concept, then compared the two solutions that help organizations collect data in a central repository, allowing for better and more efficient data-driven decision making.

As I mentioned, be on the lookout for my separate posts on AWS Lake Formation and Amazon Redshift! These two AWS services are crucial to know for the exam.

As always, thanks for reading till the end. <br>Ael

# Sources

- [AWS Lake Formation](https://aws.amazon.com/lake-formation/)
- [Amazon Redshift](https://aws.amazon.com/redshift/)
- [AWS Analytics: What’s the Difference Between a Data Warehouse, Data Lake, and Data Mart?](https://aws.amazon.com/compare/the-difference-between-a-data-warehouse-data-lake-and-data-mart/)

[^1]: CRM stands for customer relationship management. It's basically a system for tracking your organization's interactions with potential and current customers to stay connected, find new ones, and maintain a happy relationship.
[^2]: PII stands for Personally Identifiable Information, which is any data that can be used to identify someone specifically.
[^3]: PHI stands for Protected Health Information, which specifically refers to an individual's health-related data.
[^4]: Fully-managed: All the underlying infrastructure (e.g., the servers that run the solution) is handled by AWS, so there's no need for the customer (you) to run infrastructure maintenance jobs and configure machines on-site.

---
