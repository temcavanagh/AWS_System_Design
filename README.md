# AWS System Design

## The Scenario
Now that you have built an ETL job, the team is looking to design the production system that will take the data from the source system and provide scientists with up-to-date data on a regular basis.

Following a discussion with the architects and scientists, you have discovered the following:
- The source system currently stores its data in AWS Aurora MySQL databases
- The source system currently does not have CDC enabled on those databases, but plan to within the next 12 months. When this happens, the CDC events will be placed onto Kafka.
- The scientists would like to be able to see what their features looked like at different points in time (e.g. one month ago vs today). They appreciate that they will need to make some compromises on how much data we can store, and will take your advice re: keeping old versions of the features.
- The finance and BI teams are also interested in the invoice data, and would like to query it. They are proficient in SQL only.

## Your Submission
Add to this repository documentation that:
- Shows the architecture of a production system that could run your (and similar) ETL jobs on a regular basis, before exposing the resulting datasets to the various consumers.
- Indicates how your architecture would evolve to support near real-time updating of data as the TRS engineering team enables CDC.
- Highlights any key architectural decisions made, and explains them.

# Submission

## Requirements:

TRS (source system)
- Uses AWS Aurora MySQL db storage.
- CDC not enabled currently, but likely within next 12 months.
- CDC when enabled events will be on kafka.

Data scientists will need to view/access features derived from task_one.

Data scientists would like to view features derived from task_one across time (historically).

Finance & BI will query invoice data via sql.

## Thoughts & Considerations

How is data currently being loaded from TRS into Aurora? Is it batched?

Likely changes that will be captured by CDC:

- Invoices issued
- Invoices paid
- Accounts added
- SKU items added, changed removed (including retail prices etc.)


## Proposal:

A production system that can run the ETL pipeline on a regular basis and provide up-to-date data uses the following AWS tools:

1.	Source System (TRS): The TRS stores its data in AWS Aurora MySQL databases, which is the source for the ETL pipeline.
	
2.	task_one ETL pipeline: AWS Glue can be used as the ETL service.
	
3.	Data Warehouse: For storing the transformed data from the task_one pipeline, a data warehouse through Amazon S3 can be used.
	
4.	Versioning and Time Travel: To enable data scientists to view past versions of features, a suitable choice is to use AWS Glue's versioning feature or implement it using table partitioning in Amazon S3. This allows for tracking and querying historical data snapshots.
	
5.	Data Access for Data Scientists: Data scientists can use AWS Athena to run SQL queries directly on the data stored in Amazon S3.

6.	Finance and BI Teams: The finance and BI teams can also use AWS Athena to query the invoice data stored in the data warehouse.

Supporting CDC:

To support CDC and enable near real-time updates, the architecture can be evolved as follows:

1.	CDC with Kafka: When CDC is enabled on Aurora MySQL, the CDC events can be streamed to Apache Kafka using AWS Database Migration Service (DMS).

2.	Streaming ETL: AWS Glue Streaming ETL can be used to process the CDC events from Kafka in real-time. It can transform and enrich the data and load it into the data lake or data warehouse.

3.	Updated Data Warehouse: The output from the stream processing layer can be loaded into Amazon S3 data warehouse.

Key Architectural Decisions:

AWS Glue: Using AWS Glue as the ETL service provides a serverless and scalable solution for data extraction, transformation, and loading.

Amazon S3: Storing transformed data in Amazon S3 provides scalability, durability, and cost-effectiveness for large volumes of data.

AWS Athena: Allows data scientists and other consumers to run SQL queries directly on data in Amazon S3.

AWS Database Migration Service: Enabling CDC with AWS DMS facilitates the streaming of CDC events from Aurora MySQL to Kafka, enabling near real-time updates.