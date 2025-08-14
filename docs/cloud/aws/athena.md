# AWS Athena

## Introduction

Amazon Athena is an interactive query service that lets you analyze data directly in Amazon S3 using standard SQL. It is
serverless, so there is no infrastructure to manage, and you only pay for the queries you run.

## Adding Missing Partitions Automatically

When working with partitioned tables in Athena (for example, daily partitions in `year/month/day` format), you may need
to update the metadata so Athena recognizes newly added data.

    MSCK REPAIR TABLE my_database.my_table;

This will scan the table’s S3 location and add any missing partitions automatically.

## AWS Glue Crawler

- AWS Glue Crawler Console: https://console.aws.amazon.com/glue/home?region=eu-west-1#catalog:tab=crawlers
- After adding data to S3, run the Glue Crawler to update the Athena table schema.

A typical workflow for keeping your Athena tables up to date:

1. **Run AWS Glue Crawler**  
   Update the table schema with new data structure changes.
2. **Repair Partitions in Athena**

        MSCK REPAIR TABLE my_database.my_table;

3. **Re-run the AWS Glue Crawler**  
   If new columns were introduced after the first run, run the crawler again so the new schema is reflected in Athena.

## Adding a Specific Partition Manually

If you want to add a single partition without scanning the whole dataset:

    ALTER TABLE audit_logs ADD IF NOT EXISTS
    PARTITION (year='2025', month='08', day='19', hour='15')
    LOCATION 's3://example-bucket/audit_logs/2025/08/19/15/';

## Example Athena Queries

**Audit Log Search by Object ID**

    SELECT object_id, user_id, log_timestamp, message
    FROM my_database.audit_logs
    WHERE year = '2025' AND month = '06'
      AND environment = 'test'
      AND object_id = 'UPDATE_USER_STATUS'
      AND message LIKE '%123456789%'
    ORDER BY log_timestamp DESC;

**Recent Updates**

    SELECT * FROM company_trace_db.store_product
    WHERE DATE(last_updated_at) > '2025-08-14'
      AND store_code = '1001'
      AND product_code = 'P12345'
    ORDER BY last_updated_at DESC
    LIMIT 1000;

**Top Search Keywords**

    SELECT LOWER(x.name) AS keyword, COUNT(*) AS count  
    FROM company_trace_db.user_action, UNNEST(context_value) AS x  
    WHERE DATE(created_at) BETWEEN '2025-08-10' AND '2025-08-12'  
      AND context IN ('QUICK_SEARCH', 'SEARCH')  
    GROUP BY LOWER(x.name)  
    ORDER BY count DESC  
    LIMIT 100;

## Creating Athena Tables from CSV in S3

If you have raw CSV files in S3 and want to query them directly:

    CREATE EXTERNAL TABLE IF NOT EXISTS default.audit_csv (
      `timestamp` bigint,
      `serverhost` string,
      `username` string,
      `host` string,
      `connectionid` string,
      `queryid` int,
      `operation` string,
      `database` string,
      `object` string,
      `return_code` int
    )
    PARTITIONED BY (year int, month int, day int)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe'
    WITH SERDEPROPERTIES (
      'serialization.format' = ',',
      'field.delim' = ','
    )
    LOCATION 's3://example-audit-logs/sub-directory'
    TBLPROPERTIES ('has_encrypted_data'='false');

    ALTER TABLE default.audit_csv
    ADD PARTITION (year='2025', month='01', day='31')
    LOCATION 's3://example-audit-logs/sub-directory/2025/01/31/';

## Querying Athena from IntelliJ/DataGrip

You can query AWS Athena directly from IntelliJ-based IDEs like DataGrip.  
Official
guide: [Using AWS Athena from IntelliJ-based IDE](https://blog.jetbrains.com/datagrip/2018/05/25/using-aws-athena-from-intellij-based-ide/)

**How to Use:**

1. Open DataGrip or another IntelliJ-based IDE.
2. Add a new Data Source → Amazon Athena.
3. Enter your AWS credentials and select the correct region.
4. Choose the catalog and database you want to query.
5. Write your SQL queries in the editor and run them just like any other database.

This allows you to work with Athena queries directly inside your IDE without switching to the AWS web console.

## Best Practices

- Use **Glue Crawlers** to keep schemas up to date automatically.
- Always **filter partitions** in your queries to reduce costs.
- Use **compressed columnar formats** like Parquet for faster queries.
- Maintain a **clear S3 folder structure** for easier partition management.
- Enable **S3 Lifecycle Policies** to manage storage costs.

## References

- [Official AWS Athena Documentation](https://docs.aws.amazon.com/athena/latest/ug/what-is.html)
- [AWS Glue Crawlers](https://docs.aws.amazon.com/glue/latest/dg/add-crawler.html)
- [Amazon S3 Documentation](https://docs.aws.amazon.com/s3/index.html)
- [Partition Projection in Athena](https://docs.aws.amazon.com/athena/latest/ug/partition-projection.html)
