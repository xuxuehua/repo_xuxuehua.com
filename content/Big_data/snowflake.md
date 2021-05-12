---
title: "snowflake"
date: 2020-05-20 15:37
---
[toc]





# Snowflake

Snowflake is an analytic data warehouse provided as software-as-a-service (SaaS). Snowflake provides a data warehouse that is faster, easier to use and far more flexible than traditional data warehouse offerings.



## 特点

支持多租户、支持事务、高度安全，高度可扩展并且可以按需扩展，对ANSI SQL有着完整的支持，而且对半结构化和无结构的数据有着内置的支持

Snowflake对JSON, Avro等等半结构化数据提供了内置的支持。而且因为采用了自动的schema发现以及面向列的存储使得对非结构化数据的操作跟普通结构化数据的操作性能损失很小。







## Preparing data load



### General File Sizing Recommendations

we recommend aiming to produce data files roughly 10 MB to 100 MB in size **compressed**. Aggregate smaller files to minimize the processing overhead for each file. Split larger files into a greater number of smaller files to distribute the load among the servers in an active warehouse. 







## load data

### Unencrypted files

128-bit or 256-bit keys

When staging unencrypted files in a Snowflake internal location, the files are automatically encrypted using 128-bit keys. 256-bit keys can be enabled (for stronger encryption); however, additional configuration is required.





### Already-encrypted files

User-supplied key

Files that are already encrypted can be loaded into Snowflake from external cloud storage; the key used to encrypt the files must be provided to Snowflake.





### Bulk Loading from Amazon S3

Use the [COPY INTO ](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) command to load the contents of the staged file(s) into a Snowflake database table.



Snowflake uses Amazon S3 Gateway Endpoints in each of its Amazon Virtual Private Clouds.

If the S3 bucket referenced by your external stage is in the same region as your Snowflake account, your network traffic does not traverse the public Internet. The Amazon S3 Gateway Endpoints ensure that regional traffic stays within the AWS network.



#### whitelist s3 bucket

1. Contact [Snowflake Support](https://community.snowflake.com/s/article/How-To-Submit-a-Support-Case-in-Snowflake-Lodge) to obtain the Snowflake VPC ID for the AWS region in which your account is deployed.
2. Whitelist the Snowflake VPC ID by creating an [Amazon S3 policy for a specific VPC](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies-vpc-endpoint.html?shortFooter=true#example-bucket-policies-restrict-access-vpc).
3. Provide an AWS IAM role to Snowflake to access the whitelisted Amazon S3 bucket instead of the AWS key and secret.







### local files internally

![image-20200521084922064](snowflake.assets/image-20200521084922064.png)

> @csvfiles comes from `>CREATE STAGE csvfiles;`
>
> COPY those staged files into WAREHOURSE table contacts 



![image-20200522105144045](snowflake.assets/image-20200522105144045.png)





# Privileges



## other roles

Enabling Account Usage for Other Roles
By default, the SNOWFLAKE database is available only to the ACCOUNTADMIN role.
To enable other roles to access the database and schemas, and query the views, a user with the ACCOUNTADMIN role must grant the following data sharing privilege to the desired roles:

IMPORTED PRIVILEGES

```
use role accountadmin;

grant imported privileges on database snowflake to role sysadmin;
grant imported privileges on database snowflake to role customrole1;

use role customrole1;

select * from snowflake.account_usage.databases;
```



# Terminology



## Stage

staging files into internal location called Stage

each table has stage 



### external cloud 

let named stage point to external location

![image-20200521084406853](snowflake.assets/image-20200521084406853.png)





#### aws s3

When you create a named stage, you can specify any part of a path. For example, create an external stage using one of the above example paths:

```
create stage my_stage url='s3://mybucket/United_States/California/Los_Angeles/' credentials=(aws_key_id='1a2b3c' aws_secret_key='4x5y6z');
```





## Storage Integration

Integrations are named, first-class Snowflake objects that avoid the need for passing explicit cloud provider credentials such as secret keys or access tokens. Integration objects store an AWS identity and access management (IAM) user ID.

![image-20200526163023630](snowflake.assets/image-20200526163023630.png)

1. An external (i.e. S3) stage references a storage integration object in its definition.
2. Snowflake automatically associates the storage integration with a S3 IAM user created for your account. Snowflake creates a single IAM user that is referenced by all S3 storage integrations in your Snowflake account.
3. An AWS administrator in your organization grants permissions to the IAM user to access the bucket referenced in the stage definition. Note that many external stage objects can reference different buckets and paths and use the same storage integration for authentication.



## Role management

All Snowflake users are automatically assigned the predefined PUBLIC role, which enables login to Snowflake and basic object access.

Roles can be assigned at user creation or afterwards.

The account administrator (ACCOUNTADMIN) role is the most powerful role in the system.
Assign this role to at least two users. We follow strict security procedures for resetting a forgotten or lost password for users with the ACCOUNTADMIN role.


The security administrator (SECURITYADMIN) role includes the privileges to create and manage users and roles.

The system administrator (SYSADMIN) role includes the privileges to create warehouses, databases, and all database objects (schemas, tables, etc.).





## NDR

NDR是SaaS/PaaS公司衡量增长可持续性最常用到的指标，NDR=老客户在今年的月均recurring revenue/老客户在去年的月均recurring revenue

而当我们用NDR这个视角去看待Snowflake时候，却发现Snowflake的NDR与其他的企业有很大的不同。没有其他高NDR SaaS标的的新客户收入占比如此低，不到10%。

这样的差别是来自于产品和落地所带来的爬坡周期不同：

对于大多数SaaS，“今年的客户增长”代表的就是“今年的收入增长”。1-2年后的老客户，很难再提供高速的收入增长。就好比Zoom这样的产品，三个月内就可以让所有员工形成远程开会的习惯。打个比方客户可能是第一年花了100块，第二年花了150块，第三年花了180块。但对于Snowflake这样的产品，，“今年的客户增长”代表的可能是”两年后的收入增长”。新客户可能第一年花了100块，第二年花了300块，第三年花了500块。客户的增速被延迟了，报表的收入满足感也被延迟了。

而带来这一变化的是OLAP产品的特性，以及企业上云整体的复杂安排，使得客户的爬坡周期能延长到接近2年。

这也使得OLAP客户，可以很快地将存储打满到60-70%水平，但在计算的迁移上总是很缓慢。而在迁移完成后，因为解放了数据算力，BI分析师扩招后还会提供长期的增长。所以当Snowflake的CFO Scarpelli在业绩会上提到“未来几个季度的NDR都会非常稳定”时候也就不难理解了，独特的老客户增长特点使得这家公司的收入增速降得更慢，也能保持更长期的收入快速增长。这也让Snowflake更有机会完成10年20倍甚至30倍这样的高速收入增长。当我们拉长到25年维度看PS和PE的时候，估值就不一样了。



# Snowflake Architecture 

![image-20210321012734429](snowflake.assets/image-20210321012734429.png)





![image-20201207084439652](snowflake.assets/image-20201207084439652.png)

services communicate through RESTful interfaces and fall into three architectural layers: 







## Query processing

Query execution is performed in the processing layer. Snowflake processes queries using “virtual warehouses.” Each virtual warehouse is an MPP compute cluster composed of multiple compute nodes allocated by Snowflake from Amazon EC2 or Azure Virtual Machines.

Each virtual warehouse is an independent compute cluster that does not share compute resources with other virtual warehouses. As a result, each virtual warehouse has no impact on the performance of other virtual warehouses.





## Cloud Services 

The cloud services layer is a collection of services that coordinate activities across Snowflake. These services tie together all of the different components of Snowflake in order to process user requests, from login to query dispatch. The cloud services layer also runs on compute instances provisioned by Snowflake. 

Among the services in this layer: 

* Authentication  
* Infrastructure management 
* Metadata management 
* Query parsing and optimization 
* Access control



跟Virtual Warehouses层面每个客户都是资源独占不同的是，Cloud Services层面的资源是所有租户共用的，只是在逻辑层面做了隔离，这也很好理解，这一层面没有特别大的CPU Intensive的计算，所有租户共用可以提高资源利用率

并发控制、事务完全是在Cloud Services这一层实现的，因为Snowflake服务的是分析类的查询，因此事务是通过 Snapshot Isolation的方式来实现的，所谓的Snapshot Isolation, 指的是一个查询能看的数据是这个查询开始时整个系统的一个快照，跟类似系统一样，Snowflake也是通过MVCC来实现Snapshot Isolation的



Snowflake系统里面的数据文件都是只读的，当用户对数据进行更新的时候，系统会产生新的文件，把老的文件替换掉，但是每个文件本身是只读的，这样的模式特别适合S3这种存储的工作原理，同时也方便实现MVCC -- 只要让每个查询始终读查询开始时的对应的版本的文件就好了。

  在查询剪枝上，Snowflake采用的目前常用的技术：在每个数据文件上保存数据的一些统计数据: min, max之类的，通过对这些元数据进行扫描可以判断是否要扫整个文件，从而可以避免扫描所有的文件。论文同时也论述了一下为什么这种数仓的系统用索引技术不合适:

- - 索引依赖对文件随机读，而这是S3这种系统不擅长的。
    - 索引会降低查询、加载的效率，数仓数据量都特别的大，降低了加载的效率在需要做数据恢复的时候是很大的问题。
    - 索引需要用户手动创建，而这跟Snowflake SaaS的口号，让用户用的爽的目标相背离。







## Shared-nothing architectures

Shared-nothing architectures have become the dominant system architecture in high-performance data warehousing, for two main reasons: scalability and commodity hardware. In a shared-nothing architecture, every query processor node has its own local disks. Tables are horizontally partitioned across nodes and each node is only responsible for the rows on its local disks. This design scales well for star-schema queries, because very little bandwidth is required to join a small (broadcast) dimension table with a large (partitioned) fact table. And because there is little contention for shared data structures or hardware resources, there is no need for expensive, custom hardware.



In a pure shared-nothing architecture, every node has the same responsibilities and runs on the same hardware.

A pure sharednothing architecture has an important drawback though: it tightly couples compute resources and storage resources, which leads to problems in certain scenarios.



## STORAGE VERSUS COMPUTE

Snowflake separates storage and compute.

though in principle any type of blob store would suffice (Azure Blob Storage [18, 36], Google Cloud Storage [20]). To reduce network traffic between compute nodes and storage nodes, each compute node caches some table data on local disk.

local disk is used exclusively for temporary data and caches, both of which are hot (suggesting the use of high-performance storage devices such as SSDs). So, once the caches are warm, performance approaches or even exceeds that of a pure shared-nothing system. We call this novel architecture the multi-cluster, shared-data architecture



## Data Storage

When data is loaded into Snowflake, Snowflake reorganizes that data into its internal optimized, compressed, columnar format. Snowflake stores this optimized data using Amazon Web Service’s S3 (Simple Storage Service) cloud storage or Azure Blob Storage.

The data objects stored by Snowflake are not directly visible or accessible by customers; they are accessible only through SQL query operations run using Snowflake.



using S3 or developing our own storage service based on HDFS or similar

its performance could vary, its usability, high availability, and strong durability guarantees were hard to beat.

Compared to local storage, S3 naturally has a much higher access latency and there is a higher CPU overhead associated with every single I/O request, especially if HTTPS connections are used. But more importantly, S3 is a blob store with a relatively simple HTTP(S)-based PUT/GET/DELETE interface.



These properties had a strong influence on Snowflake’s table file format and concurrency control scheme

Tables are horizontally partitioned into large, immutable files which are equivalent to blocks or pages in a traditional database system. 



It also uses S3 to store temp data generated by query operators (e.g. massive joins) once local disk space is exhausted, as well as for large query results. Spilling temp data to S3 allows the system to compute arbitrarily large queries without out-ofmemory or out-of-disk errors.



到底是在EC2上自建存储还是直接使用S3服务，论文的作者发现S3实现的非常好，要实现类似的特性很困难，因此Snowflake把更多的时间精力花在了如果做本地文件的缓存以及对于查询倾斜场景的优化上

保存在S3的数据有两类：一是用户表里面的数据；另外就是计算过程中产生的临时数据，Snowflake计算引擎支持把查询的中间结果保存到S3上，这样使得Snowflake可以支持任意规模的大查询。

而用户的元数据，比如数据库里面有哪些表，表对应到哪些数据文件，文件的统计信息，事务日志等等都是保存在一个可扩展的事务性KV存储里面，而这个是属于最上层 Cloud Services那一层的组件。





## Virtual Warehouses

The Virtual Warehouses layer consists of clusters of EC2 instances. Each such cluster is presented to its single user through an abstraction called a virtual warehouse (VW).



When a new query is submitted, each worker node in the respective VW (or a subset of the nodes if the optimizer detects a small query) spawns a new worker process. Each worker process lives only for the duration of its query

A worker process by itself—even if part of an update statement—never causes externally visible effects, because table files are immutable

each VW in turn may be running multiple concurrent queries. Every VW has access to the same shared tables, without the need to physically copy data.

It is common for Snowflake users to have several VWs for queries from different organizational units, often running continuously, and periodically launch on-demand VWs, for instance for bulk loading.



Snowflake选择在多个VW间构建Consistent Hashing的缓存层，来管理S3上的表文件和对应node节点（真正的计算节点）之间的关系；同时优化器感知这个缓存层，在做物理执行计划时，将query中的扫表算子按表文件名分派到对应的node上，从而实现缓存的高命中率；同时，因为存储计算分离+share data架构，计算上并不强耦合缓存层，所以node节点的增删并不需要立即做缓存数据的shuffle，而是基于LRU，在多个后续Query中摊还的替换表文件，完成缓存层的Lazy Replacement，平滑过渡。



VW的节点之间可能存在很大差异（比如同样是4core8G，但可能底层的CPU性能不一样），同一个Query中分配给多个process之间的TableScan算子真正的工作负载也不一定均衡（比如一个SQL需要扫描一个超大的表，包含了大量的Partition文件，优化器如果按照文件数来做分片切分出多个PartitionTableScan算子，很可能这些算子拿到相同的文件数量但真正要处理的字节数差异巨大），导致SQL整体执行上的严重长尾效应。Snowflake提出了file stealing的思想，与[Work stealing](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Work_stealing)相似，和Java的ForkJoinPool做法类似，窃取同一个query中其他node上分配到的表文件扫描任务，经过同意后转移所有权，从而快速达到自平衡。







### Local Caching and File Stealing

Each worker node maintains a cache of table data on local disk. The cache is a collection of table files i.e. S3 objects that have been accessed in the past by the node. To be precise, the cache holds file headers and individual columns of files, since queries download only the columns they need.

It just sees a stream of file and column requests, and follows a simple least-recently-used (LRU) replacement policy, oblivious of individual queries. This simple scheme works surprisingly well, but we may refine it in the future to better match different workloads.

query optimizer assigns input file sets to worker nodes using consistent hashing over table file names. Subsequent or concurrent queries accessing the same table file will therefore do this on the same worker node

Snowflake relies on the LRU replacement policy to eventually replace the cache contents.

If a peer finds that it has many files left in its input file set when such a request arrives, it answers the request by transferring ownership of one remaining file for the duration and scope of the current query. The requestor then downloads the file directly from S3, not from its peer.



计算和存储分离之后，把数据从远程S3加载过来的速度还是会比原来直接从本地磁盘读要慢，因此Snowflake给每台Worker节点都配备了高速的SSD硬盘，这个SSD硬盘的作用不是保存原始数据，而是保存被之前Query请求过的热数据，这样保证以有限的空间保存尽可能热的数据。为了提高这种缓存文件的磁盘命中率，Snowflake的查询优化器在调度TableScan的时候会根据表对应的底层文件名以一致性hash的算法把数据加载的请求分到这些worker节点上，从而保证对同样一个文件的请求可以尽量落到同一个worker节点上去，可以提高命中率。



大数据计算里面经常会出现的一种常见是查询倾斜(skew), snowflake的应对办法是他们称为 `File Stealing` 的一种做法，这种做法主要针对的 TableScan 的算子，当一个worker读取完分配给它的所有的文件读取任务之后，它会问它的兄弟节点有没有需要帮助的，如果有它会帮兄弟节点去加载数据，从而改善查询倾斜的问题。

  在执行引擎方面Snowflake不是在新的关系型数据库或者是Hadoop体系上修改得来的，而是他们基于用户对于大数据场景最好的性价比打造出来的，他们对这个执行引擎给的三个关键词是: 列存、向量化执行(加延迟物化)以及Pushed-based执行机制。前两个关键词比较好理解，这里重点说一下Pushed-based执行机制，我们常用的Volcano-style的模型是pull-based的也就是下游主动找上游要数据，而push-based则是反过来，上游主动给下游推数据。pushed-based执行引擎能够提高缓存的效率。





###  Execution Engine 

give users the best price/performance of any database-as-a-service offering on the market, so we decided to implement our own stateof-the-art SQL execution engine. The engine we have built is columnar, vectorized, and push-based.

**Columnar** storage and execution is generally considered superior to row-wise storage and execution for analytic workloads, due to more effective use of CPU caches and SIMD instructions, and more opportunities for (lightweight) compression

**Vectorized** execution means that, in contrast to MapReduce for example, Snowflake avoids materialization of intermediate results. Instead, data is processed in pipelined fashion, in batches of a few thousand rows in columnar format. This approach, pioneered by VectorWise (originally MonetDB/X100 [15]), saves I/O and greatly improves cache efficiency.

**Push-based** execution refers to the fact that relational operators push their results to their downstream operators, rather than waiting for these operators to pull data (classic Volcano-style model [27]). Push-based execution improves cache efficiency, because it removes control flow logic from tight loops [41]. It also enables Snowflake to efficiently process DAG-shaped plans, as opposed to just trees, creating additional opportunities for sharing and pipelining of intermediate results.



allow all major operators (join, group by, sort) to spill to disk and recurse when main memory is exhausted. We found that a pure main-memory engine, while leaner and perhaps faster, is too restrictive to handle all interesting workloads. Analytic workloads can feature extremely large joins or aggregations.





# hello world



## connector

```
from snowflake import connector
conn = connector.connect(
    autocommit=False,
    user='',
    password='',
    account='',
    role='',
    warehouse='',
    database='',
    schema='',
    session_parameters={
        'OPERATION_TAG': "test",
    }
)
cursor = conn.cursor()
sql = f"""
COPY INTO 
@bucket_name/__temp__/{urn.identifier}/{interface['table']}/      # replace with your target s3 path 
FROM (
select 
    a,
    b,
    c
from "A"."B"."C"
where a = 2
)
file_format=(type=parquet)
include_query_id=true
HEADER = TRUE
MAX_FILE_SIZE = 1000;
"""
print(sql)
cursor.execute(sql)
conn.commit()
result = cursor.fetchall()
```



# FAQ



## Lower performance

更新数据会导致在Snowflake的部分数据重组，出现明显性能降级

整表sort之后更新数据



# Appendix

http://pages.cs.wisc.edu/~yxy/cs839-s20/papers/snowflake.pdf

https://zhuanlan.zhihu.com/p/56745552

https://zhuanlan.zhihu.com/p/55577067

https://docs.snowflake.com/en/user-guide/tables-clustering-micropartitions.html

https://guides.snowflake.com/?cat=resource+optimization

