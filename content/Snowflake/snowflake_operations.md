---
title: "snowflake_operations"
date: 2021-04-23 10:57
---
[toc]









# Warehouse 



## create warehouse

```
CREATE OR REPLACE WAREHOUSE RXU_TEST_QUERY_WH WAREHOUSE_SIZE = 'LARGE'
WAREHOUSE_TYPE = 'STANDARD' AUTO_SUSPEND = 60 AUTO_RESUME = true MIN_CLUSTER_COUNT = 1 MAX_CLUSTER_COUNT = 2 INITIALLY_SUSPENDED = true
SCALING_POLICY = 'STANDARD'
COMMENT = 'Training WH for completing hands on labs queries';



CREATE OR REPLACE WAREHOUSE RXU_TEST_LOAD_WH WAREHOUSE_SIZE = 'XSMALL'
WAREHOUSE_TYPE = 'STANDARD'
AUTO_SUSPEND = 60 AUTO_RESUME = true MIN_CLUSTER_COUNT = 1 MAX_CLUSTER_COUNT = 2 INITIALLY_SUSPENDED = true SCALING_POLICY = 'STANDARD'
COMMENT = 'Training WH for completing hands on lab data movement';
```



## scale out warehouse

```
CREATE OR REPLACE WAREHOUSE RXU_TEST_SCALE_OUT_WH WAREHOUSE_SIZE = 'SMALL'
AUTO_SUSPEND = 60
AUTO_RESUME = true
MIN_CLUSTER_COUNT = 1 MAX_CLUSTER_COUNT = 3
INITIALLY_SUSPENDED = true
SCALING_POLICY = 'STANDARD'
COMMENT = 'Training WH for completing concurrency tests';
```

> Maximum Clusters > than the Minimum Clusters, you will be config uring the Warehouse in “Auto-Scale Mode” and allowing Snowflake to scale the Warehouse as needed to handing fluctuating workloads. If you set the Maximum Clusters and Minimum Clusters to the same number, you would be running in "Maximized Mode’, which you might do for a stable workload.





## Auto suspend / resume

Use AUTO-SUSPEND and AUTO-RESUME features to pay only for running workloads and to optimize compute savings.

```
-- All warehouses should have an appropriate setting for automatic suspension for the workload.

-- For Tasks, Loading and ETL/ELT,  warehouses set to immediate suspension.

-- For BI and SELECT query warehouses set to 10 minutes for suspension to keep data caches warm for end users

-- For DevOps, DataOps and Data Science warehouses set to 5 minutes for suspension as warm cache is not as important to ad-hoc and highly unique queries.

ALTER WAREHOUSE rxu_test_wh_xs SET AUTO_SUSPEND = 120;

-- Example: for data loading, set virtual warehouse to immediate suspension
ALTER WAREHOUSE rxu_test_wh_xs SET AUTO_SUSPEND = 1;

```



```
-- Make sure all warehouses are set to auto resume. 

-- If you are going to implement auto suspend and proper timeout limits, this is a must or users will not be able to query the system.

ALTER WAREHOUSE rxu_test_wh_xs SET AUTO_RESUME = TRUE;
```





## statement timeout

```
-- Example:  STATEMENT_TIMEOUT_IN_SECONDS
-- Can be set for Session and Object (for warehouses)
-- Show setting at account level, default=172800/s, 48hours
show parameters like 'STATEMENT_TIMEOUT_IN_SECONDS' in account;

-- Show setting at session level, default=172800/s, 48hours
show parameters like 'STATEMENT_TIMEOUT_IN_SECONDS' for session;


-- Show setting at warehouse (object) level, default=172800/s, 48hours
show parameters like 'STATEMENT_TIMEOUT_IN_SECONDS' for warehouse rxu_test_wh_xs;

-- Change parameter setting at warehouse (object) level
-- Example: No query should run longer than 1 hour when using this query warehouse
ALTER WAREHOUSE rxu_test_wh_xs SET STATEMENT_TIMEOUT_IN_SECONDS = 3600;

-- Example: No loading job should run longer than 30mins when using load warehouse
ALTER WAREHOUSE rxu_test_wh_xs SET STATEMENT_TIMEOUT_IN_SECONDS = 1800;

```



```
SHOW WAREHOUSES;
SHOW PARAMETERS IN WAREHOUSE RXU_TEST_QUERY_WH; 
SHOW WAREHOUSES LIKE '%QUERY_WH';
```





# Tables



## create micro-partition

```
USE WAREHOUSE rxu_test_wh_xs;

Use schema instructor1_db.public;

CREATE FILE FORMAT MYPIPEFORMAT
  TYPE = CSV
  COMPRESSION = NONE
  FIELD_DELIMITER = '|'
  FILE_EXTENSION = 'tbl'
  ERROR_ON_COLUMN_COUNT_MISMATCH = FALSE;

LS @TRAINING_DB.TRAININGLAB.ED_STAGE/load/TCPH_SF10/;

CREATE OR REPLACE TABLE lineitem LIKE "TRAINING_DB"."TPCH_SF1"."LINEITEM";

COPY INTO instructor1_DB.PUBLIC.LINEITEM
FROM @TRAINING_DB.TRAININGLAB.ED_STAGE/load/TCPH_SF10/LINEITEM/
FILE_FORMAT = (FORMAT_NAME = instructor1_DB.PUBLIC.MYPIPEFORMAT)
ON_ERROR = 'CONTINUE';

select * from instructor1_DB.PUBLIC.LINEITEM limit 10;

--Ask how well the table has been clustered by our specified column now.
-- Use the clustering system function with a column to check clustering quality of table based on that filter column
SELECT SYSTEM$CLUSTERING_INFORMATION('instructor1_db.public.lineitem', '(l_shipdate)');
     
-- Check the query profile. How many micro-partitions were required?
SELECT count(*)
FROM instructor1_db.public.lineitem
WHERE l_shipdate = dateadd(day, -90, to_date('1996-12-01'));
```





## specifying clustering

```
use schema instructor1_db.public;

SELECT count(*)
FROM instructor1_db.public.lineitem_natural_clustering_clone
WHERE l_shipdate = dateadd(day, -90, to_date('1996-12-01'));

-- Make a clone of the table before adding the clustering key, so the clone can be used as a baseline for comparison 
create or replace table lineitem_natural_clustering_clone clone lineitem_natural_clustering;

-- We can turn on Auto-clustering to let the serverless service automatically improves the clustering further in the background
alter table lineitem_natural_clustering cluster by (l_shipdate);

select hll(l_shipdate) from lineitem_natural_clustering;

--Ask how well the table has been clustered by our specified column now.
-- Use the clustering system function with a column to check clustering quality of table based on that filter column
SELECT SYSTEM$CLUSTERING_INFORMATION('instructor1_db.public.lineitem_natural_clustering', '(l_shipdate)');
    
-- Check the query profile. How many micro-partitions were required?

alter session set use_cached_result = false;
SELECT count(*)
FROM instructor1_db.public.lineitem_natural_clustering
WHERE l_shipdate = dateadd(day, -90, to_date('1996-12-01'));


-- With Automatic Clustering enabled, you may query for Automatic Clustering history 
-- Snowflake will provide historical aggregated values over one hour intervals
-- Querying using Information Schema 
SELECT 
start_time, end_time, credits_used, num_bytes_reclustered, num_rows_reclustered
FROM
TABLE(information_schema.automatic_clustering_history(
    date_range_start=>dateadd(d, -1, current_timestamp()), 
    date_range_end=>current_timestamp(),
    table_name=>'LINEITEM_NATURAL_CLUSTERING')
    );
--  Get bytes clustered, credits consumed from Account Usage Share data
SELECT 
start_time, end_time, credits_used, num_bytes_reclustered, num_rows_reclustered
FROM
snowflake.account_usage.automatic_clustering_history where 
   start_time >= dateadd(d, -1, current_timestamp()) and  
   end_time < current_timestamp() and
    table_name ='LINEITEM_NATURAL_CLUSTERING';


-- Ingesting more data to the clustered table

COPY INTO lineitem_natural_clustering 
FROM @TRAINING_DB.TRAININGLAB.ED_STAGE/ingestion_natural_clustering/1997/05
PATTERN='.*[.]tbl[.]gz'
FILE_FORMAT = (FORMAT_NAME = training_db.traininglab.MYGZIPPIPEFORMAT);
```





## PermanentTables 

```
USE SCHEMA RXU_TEST_DB.Public;
CREATE OR REPLACE TABLE RXU_TEST_TBL ( ID NUMBER(38,0)
, NAME STRING(10)
, COUNTRY VARCHAR (20) , ORDER_DATE DATE
)
DATA_RETENTION_TIME_IN_DAYS = 10 COMMENT = 'CUSTOMER INFORMATION';
```



## TemporaryTables / TransientTables

The key idea for transient and temporary tables is that they cannot have their retention period last longer than one (1) day. This ensures storage (and cost) savings and is intended for use cases where clients can benefit from the storage savings.

```
USE SCHEMA RXU_TEST_DB.Public;
CREATE OR REPLACE TEMPORARY TABLE TEMP_TBL( C1 INTEGER, C2 INTEGER
) DATA_RETENTION_TIME_IN_DAYS = 0;
INSERT INTO TEMP_TBL VALUES (1,1),(2,2); 
SELECT * FROM TEMP_TBL;
```

> this TEMP_TBL cannot be queryed from second worksheet
>
> 





```
USE SCHEMA RXU_TEST_DB.Public;
CREATE OR REPLACE TRANSIENT TABLE TRAN_TBL( C1 INTEGER, C2 INTEGER);
INSERT INTO TRAN_TBL VALUES (3,3),(4,4); 
SELECT * FROM TRAN_TBL;
```















# query



## Caching query result

```
-- Example:  USE_CACHED_RESULT
-- Can be set for Account » User » Session
-- Show setting at account level
show parameters like 'USE_CACHED_RESULT' in account;

-- Show setting at user level
show parameters like 'USE_CACHED_RESULT' for user cheetah;

-- Show setting at session level
show parameters like 'USE_CACHED_RESULT' for session;

-- Change parameter setting at session level
ALTER SESSION SET use_cached_result = FALSE;
```





## small query workload

```
use schema snowflake_sample_data.tpcds_sf10tcl;

use warehouse rxu_test_wh_xs;

ALTER SESSION SET USE_CACHED_RESULT = FALSE;
-- Interactive (1-3 months of data scanned) — Simple star-join queries
-- Smaller queries requiring less compute resource

-- TPC-DS_query19
select  i_brand_id brand_id, i_brand brand, i_manufact_id, i_manufact,
  sum(ss_ext_sales_price) ext_price
 from date_dim, store_sales, item,customer,customer_address,store
 where d_date_sk = ss_sold_date_sk
   and ss_item_sk = i_item_sk
   and i_manager_id=8
   and d_moy=11
   and d_year=1999
   and ss_customer_sk = c_customer_sk 
   and c_current_addr_sk = ca_address_sk
   and substr(ca_zip,1,5) <> substr(s_zip,1,5) 
   and ss_store_sk = s_store_sk 
 group by i_brand
      ,i_brand_id
      ,i_manufact_id
      ,i_manufact
 order by ext_price desc
         ,i_brand
         ,i_brand_id
         ,i_manufact_id
         ,i_manufact
limit 100 ;


SELECT 
QUERY_LOAD_PERCENT,
EXECUTION_TIME
FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY 
WHERE query_id = '019b93d0-0600-533c-002d-538300058b32';  

-- TPC-DS_query42
select  dt.d_year
  ,item.i_category_id
  ,item.i_category
  ,sum(ss_ext_sales_price)
 from   date_dim dt
  ,store_sales
  ,item
 where dt.d_date_sk = store_sales.ss_sold_date_sk
  and store_sales.ss_item_sk = item.i_item_sk
  and item.i_manager_id = 1    
  and dt.d_moy=12
  and dt.d_year=1998
 group by   dt.d_year
    ,item.i_category_id
    ,item.i_category
 order by       sum(ss_ext_sales_price) desc,dt.d_year
    ,item.i_category_id
    ,item.i_category
limit 100 ;

-- TPC-DS_query52
select  dt.d_year
  ,item.i_brand_id brand_id
  ,item.i_brand brand
  ,sum(ss_ext_sales_price) ext_price
 from date_dim dt
     ,store_sales
     ,item
 where dt.d_date_sk = store_sales.ss_sold_date_sk
    and store_sales.ss_item_sk = item.i_item_sk
    and item.i_manager_id = 1
    and dt.d_moy=12
    and dt.d_year=2002
 group by dt.d_year
  ,item.i_brand
  ,item.i_brand_id
 order by dt.d_year
  ,ext_price desc
  ,brand_id
limit 100 ;

-- TPC-DS_query55
select  i_brand_id brand_id, i_brand brand,
  sum(ss_ext_sales_price) ext_price
 from date_dim, store_sales, item
 where d_date_sk = ss_sold_date_sk
  and ss_item_sk = i_item_sk
  and i_manager_id=90
  and d_moy=12
  and d_year=1998
 group by i_brand, i_brand_id
 order by ext_price desc, i_brand_id
limit 100 ;

-- TPC-DS_query81
with customer_total_return as
 (select cr_returning_customer_sk as ctr_customer_sk
        ,ca_state as ctr_state, 
  sum(cr_return_amt_inc_tax) as ctr_total_return
 from catalog_returns
     ,date_dim
     ,customer_address
 where cr_returned_date_sk = d_date_sk 
   and d_year =2000
   and cr_returning_addr_sk = ca_address_sk 
 group by cr_returning_customer_sk
         ,ca_state )
  select  c_customer_id,c_salutation,c_first_name,c_last_name,ca_street_number,ca_street_name
                   ,ca_street_type,ca_suite_number,ca_city,ca_county,ca_state,ca_zip,ca_country,ca_gmt_offset
                  ,ca_location_type,ctr_total_return
 from customer_total_return ctr1
     ,customer_address
     ,customer
 where ctr1.ctr_total_return > (select avg(ctr_total_return)*1.2
        from customer_total_return ctr2 
                      where ctr1.ctr_state = ctr2.ctr_state)
       and ca_address_sk = c_current_addr_sk
       and ca_state = 'VA'
       and ctr1.ctr_customer_sk = c_customer_sk
 order by c_customer_id,c_salutation,c_first_name,c_last_name,ca_street_number,ca_street_name
                   ,ca_street_type,ca_suite_number,ca_city,ca_county,ca_state,ca_zip,ca_country,ca_gmt_offset
                  ,ca_location_type,ctr_total_return
 limit 100;
```









## Large query workflow

really large query

```
use schema snowflake_sample_data.tpcds_sf10tcl;

use warehouse RXU_TEST_WH_L;

ALTER WAREHOUSE RXU_TEST_WH_L SET STATEMENT_TIMEOUT_IN_SECONDS = 360;

ALTER SESSION SET USE_CACHED_RESULT = FALSE;

-- TPC-DS_query78
-- Join large tables, thereby exercising large hash joins, 
-- and produce large intermediate result sets and large sorts.

with ws as
  (select d_year AS ws_sold_year, ws_item_sk,
    ws_bill_customer_sk ws_customer_sk,
    sum(ws_quantity) ws_qty,
    sum(ws_wholesale_cost) ws_wc,
    sum(ws_sales_price) ws_sp
   from web_sales
   left join web_returns on wr_order_number=ws_order_number and ws_item_sk=wr_item_sk
   join date_dim on ws_sold_date_sk = d_date_sk
   where wr_order_number is null
   group by d_year, ws_item_sk, ws_bill_customer_sk
   ),
cs as
  (select d_year AS cs_sold_year, cs_item_sk,
    cs_bill_customer_sk cs_customer_sk,
    sum(cs_quantity) cs_qty,
    sum(cs_wholesale_cost) cs_wc,
    sum(cs_sales_price) cs_sp
   from catalog_sales
   left join catalog_returns on cr_order_number=cs_order_number and cs_item_sk=cr_item_sk
   join date_dim on cs_sold_date_sk = d_date_sk
   where cr_order_number is null
   group by d_year, cs_item_sk, cs_bill_customer_sk
   ),
ss as
  (select d_year AS ss_sold_year, ss_item_sk,
    ss_customer_sk,
    sum(ss_quantity) ss_qty,
    sum(ss_wholesale_cost) ss_wc,
    sum(ss_sales_price) ss_sp
   from store_sales
   left join store_returns on sr_ticket_number=ss_ticket_number and ss_item_sk=sr_item_sk
   join date_dim on ss_sold_date_sk = d_date_sk
   where sr_ticket_number is null
   group by d_year, ss_item_sk, ss_customer_sk
   )
 select 
ss_customer_sk,
round(ss_qty/(coalesce(ws_qty+cs_qty,1)),2) ratio,
ss_qty store_qty, ss_wc store_wholesale_cost, ss_sp store_sales_price,
coalesce(ws_qty,0)+coalesce(cs_qty,0) other_chan_qty,
coalesce(ws_wc,0)+coalesce(cs_wc,0) other_chan_wholesale_cost,
coalesce(ws_sp,0)+coalesce(cs_sp,0) other_chan_sales_price
from ss
left join ws on (ws_sold_year=ss_sold_year and ws_item_sk=ss_item_sk and ws_customer_sk=ss_customer_sk)
left join cs on (cs_sold_year=ss_sold_year and cs_item_sk=cs_item_sk and cs_customer_sk=ss_customer_sk)
where coalesce(ws_qty,0)>0 and coalesce(cs_qty, 0)>0 and ss_sold_year=2001
order by 
  ss_customer_sk,
  ss_qty desc, ss_wc desc, ss_sp desc,
  other_chan_qty,
  other_chan_wholesale_cost,
  other_chan_sales_price,
  round(ss_qty/(coalesce(ws_qty+cs_qty,1)),2)
limit 100;

SET $qid = last_query_id();

SELECT 
QUERY_LOAD_PERCENT,
PARTITIONS_SCANNED,
PARTITIONS_TOTAL,
BYTES_SPILLED_TO_LOCAL_STORAGE, // the aggregate disk space of ssdrive of your compute cluster -> yellow warning
BYTES_SPILLED_TO_REMOTE_STORAGE,  // cloud native storage (.e.g S3) - red warning
BYTES_SENT_OVER_THE_NETWORK,
EXECUTION_TIME
FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY 
WHERE query_id = '019b8db8-0600-5279-002d-5383000403d2';  

```







## RESULT_SCAN

The RESULT_SCAN function returns the result set of a previous command (provided the result is still in the cache, which typically is within 24 hours of when you executed the query) as if the result were a table. This is useful if you need to process command output, such as the SHOW command and other metadata query statements

```
use warehouse rxu_test_wh_xs;
use database training_db;
USE SCHEMA TRAININGLAB;
SHOW TABLES;
SELECT "database_name", "schema_name", "name" AS table_name, "rows", "created_on" FROM TABLE(result_scan(last_query_id()))
WHERE "created_on" < dateadd(day, -1, current_timestamp())
ORDER BY "created_on";
```





# LOCK



## LOCK_TIMEOUT

Explore the transaction-related parameter, LOCK_TIMEOUT, which controls the number of seconds to wait while trying to lock a resource, before timing out and aborting the waiting statement:



```
SHOW PARAMETERS LIKE '%lock%';
```

> ```
> key	value	default	level	description	type
> LOCK_TIMEOUT	43200	43200		Number of seconds to wait while trying to lock a resource, before timing out   and aborting the statement. A value of 0 turns off lock waiting i.e. the   statement must acquire the lock immediately or abort. If multiple resources   need to be locked by the statement, the timeout applies separately to each   lock attempt.	NUMBER
> ```
>
> 



## Release transaction

An account administrator can use the system function SYSTEM$ABORT_TRANSACTION to release any lock on any user’s transactions by executing the function using the transaction ID from the SHOW LOCKS output.

```
SELECT SYSTEM$ABORT_TRANSACTION(<transaction_id>);
```









# Table storage & retention

```
USE WAREHOUSE rxu_test_wh_xs;
--  Run the following command to see all the parameters that are
--         available at the account level:
SHOW PARAMETERS FOR ACCOUNT;
--         Review the available parameters and their default values.
--  Check the default time travel retention time that is set at the
--         account level:
SHOW PARAMETERS LIKE 'DATA_RETENTION_TIME_IN_DAYS' IN ACCOUNT;
--  Create two new tables - one with the default retention time, and
--         another with a different retention time:
USE SCHEMA rxu_test_db.public;
CREATE
OR REPLACE TABLE tt_default_raw (col1 INT);
CREATE
OR REPLACE TABLE tt_set30_target (col2 INT) DATA_RETENTION_TIME_IN_DAYS = 30;
--  Verify the retention time for the two tables:
SHOW PARAMETERS LIKE 'DATA_RETENTION%' FOR TABLE tt_default_raw;
SHOW PARAMETERS LIKE 'DATA_RETENTION%' FOR TABLE tt_set30_target;
```







# Optimizer & Metadata Operations



```
USE ROLE TRAINING_ROLE;                       
USE WAREHOUSE RXU_TEST_WH_XS;               
USE SCHEMA SNOWFLAKE_SAMPLE_DATA.TPCH_SF100;

ALTER SESSION SET USE_CACHED_RESULT = FALSE;


-- Snowflake always maintains accurate statistics 
-- The optimizer can rely on the statistics to optimize a query:
-- Example 1: Certain Aggregation like min/max/count

SELECT MIN(l_orderkey), MAX(l_orderkey), COUNT(*) FROM lineitem;
-- Click the Query ID at the top of the result pane, then click the
-- link to open the profile. Click the profile tab. It should show that 100% of the result came from metadata operation
-- This outcome is possible because all the SELECT expressions can be constant-folded from reading the min/max statistics of the 
-- micro-partitions of the table

-- Example 2: NULL predicate 
-- The following query with additional filter is still a metadata operation because the filter (WHERE clause condition) can be constant-folded to true or false
SELECT MIN(l_orderkey), MAX(l_orderkey), COUNT(*) FROM lineitem
WHERE l_orderkey IS NOT NULL;

-- Example 3: constant folding for predicate
-- Run the following query to find product item that has order key greater than 6000000000  
SELECT  l_linenumber 
FROM lineitem
where l_orderkey > 6000000000;
-- Since the MAX(l_orderkey) = 6000000000, the SQL Pruner knows that and perform constant folding on 'l_orderkey > 6000000000' to true and the above query becomes a metadata operation, saving compute.  

-- Example 4: Pruning based on scalar subquery optimization
SELECT count(*) FROM orders
WHERE o_orderdate = 
  (SELECT min(l_shipdate) FROM lineitem);
-- Click the Query ID at the top of the result pane, then click the
-- link to open the profile. Click the profile tab. 
-- It should show good pruning for ORDERS table acccess assisted by the constant (mininum shipdate) derived from the scalar subquery
```







# DDL commands

## Cloning command

```
USE ROLE TRAINING_ROLE;                       
USE WAREHOUSE RXU_TEST_WH_XS;               
USE SCHEMA SNOWFLAKE_SAMPLE_DATA.TPCH_SF100;

ALTER SESSION SET USE_CACHED_RESULT = FALSE;


-- Example 6:  DDL commands
-- Create a view
CREATE OR REPLACE VIEW rxu_test_db.public.v1 AS SELECT * FROM SNOWFLAKE_SAMPLE_DATA.TPCH_SF100.lineitem;

SELECT COUNT(*) FROM  rxu_test_db.public.v1;

-- Example 7: Cloning and Add new column with default value to the cloned table 
-- Add a new column to a big table
CREATE OR REPLACE TABLE rxu_test_db.public.lineitem_clone clone "TRAINING_DB"."TPCH_SF1000"."LINEITEM";

SELECT count(*) FROM rxu_test_db.public.lineitem_clone;

ALTER TABLE rxu_test_db.public.lineitem_clone add column my_col int default 0;

SELECT * from rxu_test_db.public.lineitem_clone limit 1;
```



```
L_ORDERKEY	L_PARTKEY	L_SUPPKEY	L_LINENUMBER	L_QUANTITY	L_EXTENDEDPRICE	L_DISCOUNT	L_TAX	L_RETURNFLAG	L_LINESTATUS	L_SHIPDATE	L_COMMITDATE	L_RECEIPTDATE	L_SHIPINSTRUCT	L_SHIPMODE	L_COMMENT	MY_COL
3116014914	161924278	1924279	2	33.00	42707.94	0.05	0.00	R	F	1992-06-25	1992-06-06	1992-07-11	DELIVER IN PERSON	AIR	osits sleep idly	0
```





## insert overwrite

```
Insert overwrite into table <table_name>
SELECT
    product_key, product_type_code, product_type_key,
    array_agg(tag_key) AS tag_key 
FROM MY_SCHEMA.MY_TABLE
GROUP BY product_key, product_type_code, product_type_key
```





# DML commands



## autocommit

```
SHOW PARAMETERS LIKE 'AUTOCOMMIT' IN SESSION;
```

> ```
> key	value	default	level	description	type
> AUTOCOMMIT	true	true		The autocommit property determines whether is statement should to be implicitly  wrapped within a transaction or not. If autocommit is set to true, then a   statement that requires a transaction is executed within a transaction   implicitly. If autocommit is off then an explicit commit or rollback is required  to close a transaction. The default autocommit value is true.	BOOLEAN
> ```
>
> 



```
ALTER SESSION SET AUTOCOMMIT = FALSE;
```







# Streams and Tasks

https://community.snowflake.com/s/article/ELT-Data-Pipelining-in-Snowflake-Data-Warehouse-using-Streams-and-Tasks





# Commands



## SHOW

```
USE ROLE TRAINING_ROLE;
USE WAREHOUSE rxu_test_wh_xs;
USE DATABASE rxu_test_db;
USE SCHEMA public;

show parameters in account;
```

> these parameters can be overridden and customized at session or object level, depending on the use cases.



```
SHOW PARAMETERS IN SESSION;
```

> parameters available at the session level



```
SHOW PARAMETERS IN DATABASE SNOWFLAKE_SAMPLE_DATA;
```

> ```
> key	value	default	level	description	type
> AUTO_REFRESH_MATERIALIZED_VIEWS_ON_SECONDARY	false	false		allow refresh of materialized views on a secondary database	BOOLEAN
> DATA_RETENTION_TIME_IN_DAYS	1	1		number of days to retain the old version of deleted/updated data	NUMBER
> DEFAULT_DDL_COLLATION				Collation that is used for all the new columns created by the DDL statements (if not specified)	STRING
> MAX_DATA_EXTENSION_TIME_IN_DAYS	14	14		Maximum number of days to extend data retention beyond the retention period to prevent a stream becoming stale.	NUMBER
> QUOTED_IDENTIFIERS_IGNORE_CASE	false	false		If true, the case of quoted identifiers is ignored	BOOLEAN
> ```
>
> DATA_RETENTION_TIME_IN_DAYS, and is also available for schemas and tables. This parameter plays an important role in Time Travel; allowing data analysts to perform the following actions within a defined period of time
>
> 



```
SHOW PARAMETERS IN TABLE TRAINING_DB.TRAININGLAB.LINEITEM;
SHOW COLUMNS IN TABLE TRAINING_DB.TRAININGLAB.LINEITEM; 
DESC TABLE TRAINING_DB.TRAININGLAB.LINEITEM;
```





### objects

```
USE SCHEMA INFORMATION_SCHEMA; 
SHOW views;
SHOW terse views;
SHOW views LIKE 'tables';
SHOW objects;
SHOW functions;
SHOW user functions;
SHOW pipes;
SHOW stages;
SHOW transactions;
SHOW variables;
SHOW shares;
```



### time and timezone

```
SHOW PARAMETERS LIKE '%TIME%' IN ACCOUNT; SHOW PARAMETERS LIKE '%TIME%' IN SESSION;
SHOW PARAMETERS LIKE '%TIME%' IN USER [login];
```



```
SELECT CURRENT_TIME();
ALTER SESSION SET TIMEZONE='America/Chicago';
SELECT CURRENT_TIME();
SELECT CURRENT_DATE();
SELECT CURRENT_TIMESTAMP();
```



get multiple timestamp

```
SELECT
CURRENT_TIMESTAMP()::TIMESTAMP_TZ AS TZ
, CURRENT_TIMESTAMP()::TIMESTAMP_NTZ AS NTZ
, CURRENT_TIMESTAMP()::TIMESTAMP_LTZ AS LTZ;
```





# time travel

The activities of taking a physical backup can now be replaced by making a clone, which is significantly faster, requires less storage cost ($0 at best). Combined with Snowflake’s Time Travel feature, daily backups are no longer needed as Snowflake makes the normally hard task of doing Point In Time Recovery (PITR) a simple matter.

Time Travel can restore tables that are accidentally dropped. In other database platforms, dropping a database, schema or table is disastrous and requires the database be restored from backup (if one exists). But, in Snowflake, since dropping objects is only a metadata-based operation, restoring from a DROP statement can be completed in seconds by using the UNDROP command.



## UNDROP

The UNDROP command must be executed with the data retention time (DATA_RETENTION_TIME_IN_DAYS) parameter value for the table. For Standard/Premier Editions this can be 0 or 1 day (default 1). For Enterprise Edition and above this can be 0 to 90 days for permanent table and 0 or 1 day for temporary and transient tables (default 1 unless a different default value was specified at the schema, database, or account level).



```
SHOW TABLES;
SHOW PARAMETERS LIKE '%DATA_RETENTION_TIME_IN_DAYS%' IN ACCOUNT;
```

> ```
> key	value	default	level	description	type
> DATA_RETENTION_TIME_IN_DAYS	1	1		number of days to retain the old version of deleted/updated data	NUMBER
> ```
>
> Retention time is one day



```
UNDROP TABLE schema.table;
show tables history;
```



undrop also support for schema and db

```
SHOW SCHEMAS;
DROP SCHEMA HR;
SHOW SCHEMAS;
SHOW SCHEMAS HISTORY; 
UNDROP SCHEMA HR; 
SHOW SCHEMAS;

SHOW DATABASES STARTS WITH 'RXU';
DROP DATABASE RXU_LAB9_DB;
SHOW DATABASES HISTORY STARTS WITH 'RXU'; 
UNDROP DATABASE RXU_LAB9_DB;
SHOW DATABASES HISTORY STARTS WITH 'RXU';
```





## Zero Copy Clone

Use the CLONE option to restore a table to the point in time immediately prior to an errant update.





# functions



## get_ddl

use get_ddl funtion to retrieve DDL statement

```
SELECT get_ddl('table', 'training_db.weather.isd_2019_total');
```

> ```
> create or replace TABLE ISD_2019_TOTAL (   V VARIANT,   T TIMESTAMP_NTZ(9)  );
> ```
>
> 



## ARRAY_CONSTRUCT

```
SELECT ARRAY_CONSTRUCT('E1','E2','E3','E4','E5','E6');
```

> ```
> ARRAY_CONSTRUCT('E1','E2','E3','E4','E5','E6')
> [    "E1",    "E2",    "E3",    "E4",    "E5",    "E6"  ]
> ```
>
> 





## ARRAY_TO_STRING

```
SELECT ARRAY_TO_STRING(ARRAY_CONSTRUCT('E1','E2','E3','E4'),'|'); 
```

> ```
> ARRAY_TO_STRING(ARRAY_CONSTRUCT('E1','E2','E3','E4'),'|')
> E1|E2|E3|E4
> ```
>
> 





## ARRAY_SLICE

```
SELECT ARRAY_SLICE(ARRAY_CONSTRUCT('E1','E2','E3','E4','E5','E6'),3,5);
```

> ```
> ARRAY_SLICE(ARRAY_CONSTRUCT('E1','E2','E3','E4','E5','E6'),3,5)
> [    "E4",    "E5"  ]
> ```
>
> 



## CAST (convert data types)

to cast and convert between data types

```
SELECT CAST(12.12345 AS FLOAT), CAST(12.12345 AS INTEGER), CAST(12.12345 AS
STRING);
```

> ```
> CAST(12.12345 AS FLOAT)	CAST(12.12345 AS INTEGER)	CAST(12.12345 AS  STRING)
> 12.12345	12	12.12345
> ```
>
> 





## TO_NUMBER

```
SELECT TO_NUMBER('11.543', 6, 2);
```

> ```
> TO_NUMBER('11.543', 6, 2)
> 11.54
> ```
>
> 



## DATEDIFF

```
SELECT DATEDIFF(DAY, CURRENT_DATE(),TO_DATE('2019-07-04'));
```

> ```
> DATEDIFF(DAY, CURRENT_DATE(),TO_DATE('2019-07-04'))
> -670
> ```
>
> 















