---
title: "kinesis_data_stream"
date: 2020-12-25 15:35
---
[toc]





# Kinesis Data Stream

The *producers* continually push data to Kinesis Data Streams, and the *consumers* process the data in real time. 

Consumers (such as a custom application running on Amazon EC2 or an Amazon Kinesis Data Firehose delivery stream) can store their results using an AWS service such as Amazon DynamoDB, Amazon Redshift, or Amazon S3.



A *Kinesis data stream* is a set of [shards](https://docs.aws.amazon.com/streams/latest/dev/key-concepts.html#shard). Each shard has a sequence of data records. Each data record has a [sequence number](https://docs.aws.amazon.com/streams/latest/dev/key-concepts.html#sequence-number) that is assigned by Kinesis Data Streams.



![img](kinesis_data_stream.assets/architecture.png)







