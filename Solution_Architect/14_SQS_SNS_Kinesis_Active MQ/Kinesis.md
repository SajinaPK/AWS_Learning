# Kinesis

- real time Big data streaming
- Makes it easy to collect, process, and analyze streaming data in real-time
- Ingest real-time data such as: Application logs, Metrics, Website clickstreams, IoT telemetry data...
- **Kinesis Data Streams**: capture, process, and store data streams
- **Kinesis Data Firehose**: load data streams into AWS data stores
- **Kinesis Data Analytics**: analyze data streams with SQL or Apache Flink
- **Kinesis Video Streams**: capture, process, and store video streams

- **Kinesis Data Streams** 
  ![Alt text](images/KinesisDataStream.png)
  - Way to stream big data in your systems.
	- Made up of multiple shards which are numbered. The number of shards should be provisioned ahead of time.
	- Data is split across all shards.
	- **Shards** define the stream capacity in terms of ingestion and consumption rates.
	- Producers send data to Kinesis Data Streams.
	- Producers can be Applications, Client desktops or mobiles, Lower level SDK or higher level KPL library, or Kinesis agent inside the server to stream application logs into Kinesis Data streams.
	- All the **producers** produce records, made of 2 things the partition key and the data blob, the partition key will decide the shard in which this record will go.
	- Data can be send by producers at the rate of **1 MB/sec or 1000 msgs/sec per shard**. Ex for 6 shards its 6MB/s or 6000 msgs/sec
	- **Consumers** are SDK or at high level KCL (Kinesis Client Libraries), Lambda functions (for server less processing), Kinesis Data Firehose, or Kinesis Data Analytics.
	- The record then **consumers** receive have the same partition key + sequence no(where the record was in the shard) + data blob.
	- Throughput is **2MB/sec shared across all consumers in a shard, OR 2MB/sec per shard per consumer** if enhanced consumer mode(or enhanced fan-out mode)
      - By default, the 2MB/second/shard output is shared between all of the applications consuming data from the stream
      - You should use **enhanced fan-out** if you have multiple consumers retrieving data from a stream in parallel
      - With enhanced fan-out developers can register stream consumers to use enhanced fan-out and receive their own 2MB/second pipe of read throughput per shard, and this throughput automatically scales with the number of shards in a stream.
  
  - **Basics**
    - Retention 1 day to 365 days
		- Ability to reprocess or replay data
		- Once data is inserted in the Kinesis, it can’t be deleted (immutability)  
		- Data that shares the same partition goes to the same shard (ordering) 
    - Producers: AWS SDK, Kinesis Producer Library (KPL), Kinesis Agent
    - Consumers:
      - Write your own: Kinesis Client Library (KCL), AWS SDK
      - Managed: AWS Lambda, Kinesis Data Firehose, Kinesis Data Analytics,

  - **Capacity Modes**  
    - **1 Provisioned Mode**:
      - You choose the number of shards provisioned, scale manually or using API
      - Each shard gets 1MB/s in (or 1000 records per sec)
      - Each shard gets 2MB/sec out (classic or enhanced fan-out consumer)
      - You pay per shard provisioned per hour
    - **2 On-demand mode**:
      - No need to provision or manage the capacity
      - Default capacity provisioned (4MB/s in or 4000 records per second)
      - Scales automatically based on observed throughput peak during last 30 days
      - Pay per stream per hour and data in/out per GB

   - If you don’t know your capacity event then go for **in-demand**

  - **Security**
    - Control Access /Authorization using IAM policie
    - Encryption inflight(HTTPS), at rest (KMS), client side(on your own)
    - VPC endpoints available which allows access to Kinesis directly from HTTPS, for instance in a private subject without going through internet.
    - Monitor API calls using CloudTrail.
    ![Alt text](images/KDS_Security.png)

- **Kinesis Data Firehose** 
  ![Alt text](images/KinesisDataFirehose.png)
  - All the producers of the data streams can be producers here + Kinesis data stream + CloudWatch(logs and events) + AWS IoT
	- The producers send **Upto 1MB records** to the firehose.
	- KDF can optionally **transform data using Lambda functions**.
	- Without u writing any code KDF will read data from sources and write in batches to destinations. 
	- 3 kinds of data destinations: 
        - 1 -ASW destinations - S3, Amazon Redshift (warehousing DB, to do so it first writes to S3 and from there issue COPY to write to Redshift), Amazon OpenSearch. 
        - 2 -3rd party partners - Datalog, splunk, New Relic, MongoDB etc 
        - 3 -Custom Destinations, using an API with HTTP endpoint.
	- You have an option to send all the data to an S3 bucket after sending to destinations as a backup OR send the failed data to an S3 bucket.
	- **Fully managed service, server less**. 
	- Pay for the data going through Firehose.
	- **Near Real Time** - meaning since we write data to destinations in batches, there may be 60sec latency minimum for non-full batches. OR wait until at least 1MB of data.
	- Supports many data formats, conversions, transformations, compressions, and can write ur own transformations using Lambda.

  - **Basics**
    - Fully Managed Service, no administration, automatic scaling, serverless
      - AWS: Redshift / Amazon S3 / OpenSearch
      - 3rd party partner: Splunk / MongoDB / DataDog / NewRelic / ...
      - Custom: send to any HTTP endpoint
    - Pay for data going through Firehose
    - **Near Real Time**
      - 60 seconds latency minimum for non full batches
      - Or minimum 1MB of data at a time
    - Supports many data formats, conversions, transformations, compression
    - Supports custom data transformations using AWS Lambda
    - Can send failed or all data to a backup S3 bucket

- **When to use Kinesis Data Streams Vs Kinesis Data Firehose**

| **Kinesis Data Streams**                                       | **Kinesis Data Firehose**                                                              |
|----------------------------------------------------------------|----------------------------------------------------------------------------------------|
| Streaming service to ingest at scale                           | Ingestion service to stream data into S3, RedShift, OpenSearch/Elasticsearch, Splunk, partner or custom HTTP |
| Write custom code (producer/consumer)                           | Fully Managed, no service to manage                                                    |
| Real time (about 200 to 70 milliseconds)                        | Near Real Time                                                                         |
| Manage scaling on your own (Shard splitting and Shard Merging) | Automated scaling                                                                      |
| Pay per capacity provisioned                                   | Pay for data that goes into it.                                                        |
| Data storage for 1 to 365 days                                 | No data storage                                                                        |
| Multiple consumers can read from the same stream               |                                                                                        |
| Replay capability                                              | No replay capability                                                                   |

- Kinesis Firehose cannot be used to process and analyze the streaming data in custom applications (custom HTTP endpoint allowed)
- Kinesis Data Streams **cannot directly write the output to Amazon S3**. Unlike Amazon Kinesis Data Firehose, KDS does not offer a ready-made integration via an intermediary AWS Lambda function to reliably dump data into Amazon S3. You will need to do a lot of custom coding to get the AWS Lambda function to process the incoming stream and then store the transformed output to Amazon S3 with the constraint that the buffer is maintained reliably and no transformed data is lost. 

**Kinesis Vs SQS data ordering**

Ex: 100 trucks on road, each has a unique truck_id, sending GPS data regularly and we want to consume this in AWS to track their movements. How should you send that data into Kinesis?
- Answer : send using a “Partition Key” value of the “truck_id”. The same key will always go to the same shard

  - **Ordering data into SQS**
    - For SQS standard, there is no ordering.
    - For SQS FIFO, if you don’t use a Group ID, messages are consumed in the order they are sent, with only one consumer
    ![Alt text](images/SQSOrdering1.png)
    - You want to scale the number of consumers, but you want messages to be “grouped” when they are related to each other
    - Then you use a Group ID (similar to Partition Key in Kinesis)
    ![Alt text](images/SQSOrdering2.png)

  - **Kinesis Data Stream**
    - For 100 trucks, 5 kinesis shards
    - On average you’ll have 20 trucks per shard
    - Trucks will have their data ordered within each shard
    - Maximum amount of consumers in parallel we can have is 5
    - Can receive upto 5MB/sec of data (high throughput)  

  - **SQS FIFO** 
    - One SQS FIFO queue
    - 100 group IDs each equal to one truck id
    - Upto 100 consumers (hooked to each group ID)
    - 300 msgs/sec (or 3000 if batching is used)  

  ![Alt text](images/KinesisOrdering.png)  
If you want to have dynamic number of consumers based on the number of group ids, then SQS FIFO queue may be better and if you have 10000 trucks and need to send a lot of data, and have data ordering per shard then KDS are good choice.

- **Kinesis Vs SQS**
  - **Kinesis Data Stream - recommended for**  
    - Routing related records to the same record producer (streaming MapReduce)
    - Ordering of records
    - Ability for multiple applications to consume the same stream concurrently
    - Ability to consume records in the same order a few hours later (KDS stores data upto 7 days)
  - **SQS - recommended for**   
    - Messaging semantics (such as ack/fail) and visibility timeout. Apps dont have to maintain a persistent checkpoint/cursor if ack/fail is maintained. 
    - Individual message delay (upto 15 min)
    - Dynamically increasing concurrency/throughput at read time. For ex you have work queue and want to add more reader until backlog is cleared. With KDS you have to provision shards ahead of time and can scale upto sufficient number of shards.
    - Leveraging SQS's ability to scale transparently. If the load changes due to sudden spike or growth of business then the requests are bufferred and request can be processed independently. SQS can then scale transparently to handle the load without any provisioning from you.

**SQS vs SNS vs Kinesis**

|   SQS                                                                 |   SNS                                            |   Kinesis                                                                          |
|-----------------------------------------------------------------------|--------------------------------------------------|------------------------------------------------------------------------------------|
|   Consume pull data                                                   |   Pub-sub. Push model                            |   Standard: Pull data (2MB per shard)                                              |
|   Data is deleted after being consumed.                               |   Data is not persisted(lost if not delivered.)  |   Enhanced-fanout: push data (2MB per shard per consumer)                          |
|   Can have as many workers/consumers as we want.                      |   Upto 12,500,000 subscribers. 100,000 topics    |   Possibility to replay data.[data is persisted]                                   |
|   No need to provision throughput in advance as its managed service.  |   No need to provision throughput                |   Used for real time big data analytics and ETL.                                   |
|   Ordering guarantees only available on FIFO queues.                  |   Combine SNS FIFO + SQS FIFO.                   |   Ordering at shard level.[Shard no. specified in advance] [Scale shard yourself]  |
|   Individual msg delay capability.                                    |   Integrated with SQS for fan-out pattern        |   Data expires after X days [365 days].                                            |
|                                                                       |                                                  |   Provisioned mode or on-demand capacity mode                                                 |

The **capacity limits** of a Kinesis data stream are defined by the number of **shards** within the data stream. The limits can be exceeded by either data throughput or the number of reading data calls. Each shard allows for **1 MB/s incoming** data and **2 MB/s outgoing** data. You should increase the number of shards within your data stream to provide enough capacity.  

**Use case**: You are running an application that produces a large amount of real-time data that you want to load into S3 and Redshift. Also, these data need to be transformed before being delivered to their destination. 
**Kinesis Data Streams + Kinesis Data Firehose**  

Which is **NOT** a supported **subscriber** for **AWS SNS**?  
Kinesis Data Firehose is now supported, but not **Kinesis Data Streams**.  

**Amazon Kinesis Data Streams (KDS)** is a massively scalable and durable real-time data streaming service. It can continuously capture gigabytes of data per second from hundreds of sources such as website clickstreams, database event streams, financial transactions, social media feeds, IT logs, and location-tracking events.

- When an Amazon Kinesis Data Stream is configured as the source of a Kinesis Firehose delivery stream, Firehose’s `PutRecord` and `PutRecordBatch` operations are disabled and Kinesis Agent cannot write to Kinesis Firehose Delivery Stream directly. Data needs to be added to the Amazon Kinesis Data Stream through the Kinesis Data Streams `PutRecord` and `PutRecords` operations instead.