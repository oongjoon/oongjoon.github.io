---
title : "Data Loading into Bigquery"



excerpt: "GCP Bigquery"

categories:
  - Bigquery
tags:
  - [Google Cloud,GCP,Bigquery ]
# classes : wide
toc: true
toc_sticky: true
---
For this content, listen to the Data Warehouse of Part 2 of the Course [Preparing for Google Cloud Certification: Cloud Data Engineer specialized course](https://www.coursera.org/specializations/gcp-data-engineering) and organize the lecture contents in my own expression. Write it in a Closed Book and fill in the missing content while reviewing it again.

## Data Loading

To make a query request to Bigquery, you must be able to access data. There are several methods, among which there is a method of loading data into Bigquery.. 

## EL ,ELT , ETL

![image](https://user-images.githubusercontent.com/50165842/145668839-a3d8344f-6835-4ae6-8b7f-17d2edbdbced.png)

There are three data loading methods. There are EL, ELT and ETL. EL is loading without a separate transformation. Not going through transformation means that the data is already in usable form. ELT and ETL are methods used when transformation is required. It is the difference between Transformation loading and before loading. In the case of Bigquery, it is a data warehouse specialized for reading. In other words, it can be said that it is not specialized for UPDATE. Not sure how it is now, but as far as I know, Bigquery doesn't allow more than 1500 updates per table. If you think you need transformation before loading, you can use ETL.

## EL in Bigquery

![image](https://user-images.githubusercontent.com/50165842/145669017-055de43f-204b-4969-a4a5-f8dcd25e294a.png)

As mentioned earlier, EL in Bigquery does not require transformation, and if data is usable, just load it.

### supported format

![image](https://user-images.githubusercontent.com/50165842/145669064-c14bc602-606d-481b-996a-418bce1f5bf9.png)

Supported format supports various formats such as CSV, JSON, PARQUET, etc. CSV and JSON automatically determine the schema with the autodetect schema option. However, it is recommended that you perform manual verification. AVR, ORC, and PARQUET are self-describing formats. No need to use auto-detection. You don't need to provide an inline-schema. Bigquery can determine the schema. In addition to this, the user can manually define the schema. However, this is not recommended.

Bigquery supports the gzip format. However, Bigquery's performance is good when loading uncompressed files. Similarly, Cloud Storage shows better performance when uploading multiple large files than uploading multiple small files.

There are cases where you have to choose between a time-sensitive scenario and a scenario where there are bandwidth and time constraints and loading uncompressed files. You should do a quick loading test to see which alternative works best.

### DTS(data transfer system)

![image](https://user-images.githubusercontent.com/50165842/145670070-629fca04-5d9e-4632-aa5a-15a22f703793.png)

Big query supports data transfer system. Loading files in various formats has been confirmed above. Big query can load data through API. You can load data into Bigquery from anywhere you can run your code. For example, in Google Cloud, we have compute engine, container on kubernetess engine, app engine, and cloud function. You can load by using the API of these services. However, this requires you to create a custom data preprocess function. Therefore, in reality, Bigquery loads data using APIs in GCP's dataproc and datflow. DataProc and Dataflow are services that process data. 

Accroding to  what I know,  Hadoop, Spark corrspond to Dataproc, and Apache Airflow correspond to Dataflow. 


DataProc and Dataflow provide a process function so we don't have to rewrite the process function. (The description may be inaccurate. If there are any inaccuracies, please give feedback) When loading through other SaaS (Software as a service), the pre-built transformation function performs transformation before loading. (I thought it was ETL, but in lectures, I classify it as EL category.)

#### From Cloud Storage

![image](https://user-images.githubusercontent.com/50165842/145670137-56392e2a-880a-495f-b83b-9f89a96eeedc.png)

Upload from other on-premises to Cloud Storage. Then , You can load from Cloud Storage to Bigquery.

```sql
gsutil -m cp *.csv gs:://mybucket 
bq load ...
```



![image](https://user-images.githubusercontent.com/50165842/145670226-69d071ad-af5e-453a-83d1-f7068ec0e1df.png)

The process of loading with Bigquery can be automated as a scheduled query. The query should be written in standard SQL. This SQL includes data definition language (DDL) and data manipulation language (DML). Query string and destination table are parameterized and performed regularly every day.



![image](https://user-images.githubusercontent.com/50165842/145670572-7fae669e-2b1a-46a6-ae90-aea5ab58e11d.png)



. Bigquery keeps query history in cache for 7 days. Therefore, you can query a snapshot of data for a specific time period. In other words, you can easily revert to previous data. Data can be recovered only when there is no table with the same ID in the dataset. In the case of the Streaming Pipeline, you cannot recover because you can create an empty table.



#### From Saas

![image](https://user-images.githubusercontent.com/50165842/145670641-61d4ca37-daf5-450c-b2f9-85ec0c5c5909.png)

DTS regularly provides transformation, scheduling

### Backfill in DTS



For example, let's assume a scenario where a banker sends a receipt to a data warehouse.

The bank transmits data periodically, but there may be cases where the banker cannot send the record by that time.

In this case, it should be possible to detect that not all records have been entered into the data. , Requests to fill in these missing data are called data backfills.

### DTS in Bigquery 

![image](https://user-images.githubusercontent.com/50165842/145670681-cced6f0f-a4d0-4bef-9031-cc0f3f29f7a7.png)

Since DTS is a managed service, there is no need to worry about overhead maintaining, secure, etc. The old data transfer service required a lot of code, but DTS in Bigquery doesn't. The core of DTS is that automatic and scheduled transfer is possible. Using this, you can transfer data from other on-premises.



### How DTS work?

![image](https://user-images.githubusercontent.com/50165842/145670962-0a10ba8d-6f39-4587-92bc-acaa33f2b577.png)



Bigquery can perform data transfers from over 100 different SaaS applications. 

![image](https://user-images.githubusercontent.com/50165842/145671008-70f14c70-6fb6-4846-84bb-7ce1b19a512d.png)

You do not need to use Cloud Storage and you can transfer from Saas using DTS.

## ELT in Bigquery



![image](https://user-images.githubusercontent.com/50165842/145671067-d1496979-58c2-4fca-a8ed-60a729322f30.png)

This time, let's look at ELT in Bigquery. 

### Loading data and Transforming in Bigquery

![image](https://user-images.githubusercontent.com/50165842/145671078-335bb8e5-1ea9-4c91-86d1-ff06cdb2b6ee.png)

The Transformation process has been added to the photo shown  from EL . Loading from other on-premises or other cloud services to Cloud storage remains the same.

![image](https://user-images.githubusercontent.com/50165842/145671173-3cf84e68-0115-450f-a6e6-5b38758927d9.png)

You can transform data with DML. However, as mentioned above, DML has a daily quota of 1500 per table.



![image](https://user-images.githubusercontent.com/50165842/145671167-7fb961c0-5a90-45ed-86c0-56b8562a5301.png)

 

Bigquery can also perform transformations by creating a new table with CREATE or REPLACE TABLE.

![image](https://user-images.githubusercontent.com/50165842/145671232-87c9a380-d07e-48bf-9cba-043096b181a7.png)

Fedearated query is possible in Bigquery, but it is better in terms of performance to load query with Bigquery. However, it has the disadvantage of incurring additional storage cost.

It can also be loaded from other cloud services in GCP. However, export is possible only for cloud storage. (According to the GCP PDE test standards, yes, but I am not sure now.)





### Custom Transformation

![image](https://user-images.githubusercontent.com/50165842/145671303-51f49872-c2ac-4a16-933f-7fd8e6ec9329.png)

What if the transformation requires more transformation than Bigquery provides? All we have to do is write a Transformation function. Bigquery provides user defined function (UDF) functionality. Because Bigquery optimizes standard sql to perform faster, it is recommended to write standard sql. Except for Sql, only Javascript is possible. (I am not familiar with JavaScript, so I will pass it.)

## ETL in Bigquery

The lecture did not deal with the relevant examples separately. In my opinion, ETL is similar to EL except for the process of T (Transformation).
