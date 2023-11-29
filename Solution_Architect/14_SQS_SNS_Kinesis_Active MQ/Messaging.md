# Decoupling applications: SQS, SNS, Kinesis, Active MQ

**1. SQS**
  - Simple queue service 
  - Oldest AWS offering 
  - used to decouple applications  
  - by sending all application from front end to the queue and the back end can process later ex video processing. 
  - **CloudWatch metric** 
        - Queue Length 
        - **ApproximateNumberOfMessges** 
        - Can be used as a Cloudwatch metric and an alarm set for EC2 auto scaling group (ASG).
  - **Security**
        - Encryption (HTTPS API, KMS keys(at-rest), client side, default is SSE-SQS) 
        - IAM policies for the SQS APIs 
        - SQS Access policies (for cross account access to the queues, allowing other services likes SNS or S3 to write to SQS queue)
  - **Standard Queue** (At least once delivery meaning duplicates, best effort ordering), **FIFO**(Exactly once processing)
  - Max size **256KB**, **4-14 days message retention**
  - **Message visibility timeout is 30 sec**.
         - means 30 sec to be processed 
         - No consumer will receive message during the timeout period 
         - after the timeout, message will be returned to any consumer which polls. 
         - **ChangeMessageVisibility** API can be used to change this timeout. 
         - If visibility is too high, and consumer crashes then re-processing will take time. If it’s **too low, we may get duplicates**. Default 30sec. [0sec- 12 hr]
  - **Long Polling** 
        - wait for msgs to arrive if the queue is empty. 
        - This decreases the number of API calls made hence increasing efficiency and latency of the application.
		- 1-20 sec. Long polling preferable over short. 
        - Can be set at queue level or at the consumer (using **WaitTimeSeconds**)
  - **FIFO queue** 
        - limited throughput (300 msgs/s without batching, 3000 msg/s with)
        - exactly-once send, messages are processed in order by consumer. 
        - Queue name must end with a .fifo .. for ex DemoQueue.fifo.
		- If **Content-based deduplication** is explicitly enabled on the FIFO queue, **MessageDeduplicationId** will be automatically generated using a SHA-256 hash of the message body (content only, not attributes).  
  
- **SQS** - **use case** as buffer to database writes where load is very high… So SQS sits in the middle of the front end application and the Database. 
  - SQS is infinitely scalable.
  - One Auto scaling group will **Enqueue** the msgs (sendMessage) to the SQS and then another auto scaling group with **Dequeue** (receiveMessages) and then insert in the DB.
  - to decouple between application tiers - request from front end applications auto scaling group and back end processing applications auto scaling group.

  
**2. SNS** 
  - Simple Notification service
  - can publish messages to emails, sms, mobile notifications, HTTP endpoints OR SQS, Lambda, S3, Kinesis Data Firehose.
  - can receive msgs from CloudWatch Alarms, ASGs, CloudFormation(state changes), AWS buckets, S3, DMS (new replica), Lambda, DynamoDB, RDS events.
  - Topic Publish(SDK) to Direct Publish(Mobile SDK)
  - **Security** 
        - with In Flight encryption(HTTPS), At rest(KMS), Client side. 
        - Access controls using IAM policies - SNS Access policies (for cross account or for other services to write to an SNS topic) 

  - **Standard SNS** - Best effort msg ordering, At least once message delivery, Highest throughput in publishes/sec, subscription protocols: SQS, Lambda, HTTP, SMS, email, mobile application endpoints
  - **FIFO** - strictly preserved message ordering, exactly once message delivery, Highest throughput upto 300 publishes/sec. Subscription protocol : SQS


**3. SNS + SQS : Fan-out pattern** 
  - **Use case 1**: 
    - Push once in SNS, receive in all SQS that are subscribers
	- Fully decoupled, no data loss
	- SQS allows for: data persistence, delayed processing and retries of work
	- Ability to add more SQS subscribers over time
	- Make sure SQS queue access policy allows for SNS to write.
	- Cross-Region Delivery: works with SQS queues in other regions

	- **Use case 2**: 
        - For the same combination of event type (e.g object create) and prefix (eg images/), you can only have one S3 Event rule.
	    - If you want to send the same S3 event to many SQS queues, use fan out
	    - S3 object created -> Amazon S3 (event) -> SNS Topic -> Fan-out (multiple SQS + Lambda functions + ..)

	- **Use case 3**: 
        - SNS can send to Kinesis Data Firehose and then to S3 or any supported KDF destination

	- **Use case 4**: 
        - Producer send messages to SNS FIFO-> receive messages sent to SQS FIFO
	    -  hence message Ordering by group ID.
	    - Deduplication using ID or content based deduplication
	    - Limited throughput (same as SQS FIFO)

	 - **Use case 5**: 
        - SNS FIFO + SQS FIFO : Fan Out
	    - In case you need fan out + ordering + deduplication

	- **Use case 6**: 
        - Message Filtering
	    - JSON policy used to filter messages sent to SNS topics subscription. For ex if a new transaction messages has a field called state then we can create a filter policy for state as placed (for placed orders), another policy for cancelled, declined etc which all go to different SQS queue and may have another SQS queue to have all the messages unfiltered.

**4. Kinesis** 
  - real time Big data streaming
  - Managed service to collect, process, and analyze real time streaming data at any scale.
  - Sub services -**Kinesis Data streams, Data Firehose, Data analytics and Video streams** are there
  - Ingest real time data such as Application logs, Metrics, Website clickstreams, IoT telemetry data …

- **Kinesis Data Streams** 
    - Way to stream big data in your systems.
	- Made up of multiple shards which are numbered. The number of shards should be provisioned ahead of time.
	- Data is split across all shards.
	- **Shards** define the stream capacity in terms of ingestion and consumption rates.
	- Producers send data to Kinesis Data Streams.
	- Producers can be Applications, Client desktops or mobiles, Lower level SDK or higher level KPL library, or Kinesis agent inside the server to stream application logs into Kinesis Data streams.
	- All the **producers** produce records, made of 2 things the partition key and the data blob, the partition key will decide the shard in which this record will go.
	- Data can be send by producers at the rate of **1 MB/sec or 1000 msgs/sec per shard**. Ex for 6 shards its 6MB/s or 6000 msgs/sec
	- Consumers are SDK or at high level KCL (Kinesis Client Libraries), Lambda functions (for server less processing), Kinesis Data Firehose, or Kinesis Data Analytics.
	- The record then **consumers** receive have the same partition key + sequence no(where the record was in the shard) + data blob.
	- Throughput is **2MB/sec shared across all consumers in a shard, OR 2MB/sec per shard per consumer** if enhanced consumer mode(or enhanced fan-out mode)
  
  - **Basics**
    	- Retention 1 day to 365 days
		- Ability to reprocess or replay data
		- Once data is inserted in the Kinesis, it can’t be deleted (immutability)  
		- Data that shares the same partition goes to the same shard (ordering) 

  - **Capacity Modes**
		- 1. **Provisioned Mode**: - Chose the number of shards provisioned, scale manually or using API
			- Each shard gets 1MB/s in (or 1000 records per sec)
			- Each shard gets 2MB/sec out (classic or enhanced fan-out consumer)
			- You pay per shard provisioned per hour
		- 2. **On-demand mode**: - No need to provision or manage the capacity
			- Default capacity provisioned (4MB/s in or 4000 records per second)
			- Scales automatically based on observed throughput peak during last 30 days
			- Pay per stream per hour and data in/out per GB

   - If you don’t know your capacity event then go for **in-demand**

   - **Security** 
		- Control Access /Authorization using IAM policies
		- Encryption inflight(HTTPS), at rest (KMS), client side(on your own)
		- VPC endpoints available which allows access to Kinesis directly from HTTPS, for instance in a private subject without going through internet.
		- Monitor API calls using CloudTrail.

- **Kinesis Data Firehose** 
    - All the producers of the data streams can be producers here + Kinesis data stream + CloudWatch(logs and events) + AWS IoT
	- The producers send **Upto 1MB records** to the firehose.
	- KDF can optionally **transform data using Lambda functions**.
	- Without u writing any code KDF will read data from sources and write in batches to destinations. 
	- 3 kinds of data destinations: 
        - 1. ASW destinations - S3, Amazon Redshift (warehousing DB, to do so it first writes to S3 and from there issue COPY to write to Redshift), Amazon OpenSearch. 
         - 2. 3rd party partners - Datalog, splunk, New Relic, MongoDB etc 
         - 3. Custom Destinations, using an API with HTTP endpoint.
	- You have an option to send all the data to an S3 bucket after sending to destinations as a backup OR send the failed data to an S3 bucket.
	- **Fully managed service, server less**. 
	- Pay for the data going through Firehose.
	- **Near Real Time** - meaning since we write data to destinations in batches, there may be 60sec latency minimum for non-full batches. OR wait until at least 1MB of data.
	- Supports many data formats, conversions, transformations, compressions, and can write ur own transformations using Lambda.

**5. When to use Kinesis Data Streams Vs Kinesis Data Firehose**

| **Kinesis Data Streams**                                       | **Kinesis Data Firehose**                                                              |
|----------------------------------------------------------------|----------------------------------------------------------------------------------------|
| Streaming service to ingest at scale                           | Ingestion service to stream data into S3, RedShift, OpenSearch, partner or custom HTTP |
| Write custom code (producer/consumer                           | Fully Managed, no service to manage                                                    |
| Real time (about 200 to 70 milliseconds                        | Near Real Time                                                                         |
| Manage scaling on your own (Shard splitting and Shard Merging) | Automated scaling                                                                      |
| Pay per capacity provisioned                                   | Pay for data that goes into it.                                                        |
| Data storage for 1 to 365 days                                 | No data storage                                                                        |
| Multiple consumers can read from the same stream               |                                                                                        |
| Replay capability                                              | No replay capability                                                                   |
|                                                                |                                                                                        |



**6. Kinesis Vs SQS data ordering**

Ex: 100 trucks on road, each has a unique truck_id, sending GPS data regularly and we want to consume this in AWS to track their movements.

**7. Kinesis Data Stream**
  - For 5 shards, 20 trucks per shard
  - Trucks will have their data ordered within each shard
  - Maximum amount of consumers in parallel we can have is 5
  - Can receive upto 5MB/sec of data (high throughput)  

**8. SQS FIFO** 
  - One SQS FIFO queue
  - 100 group IDs each equal to one truck id
  - Upto 100 consumers (hooked to each group ID)
  - 300 msgs/sec (or 3000 if batching is used)  

If you want to have dynamic number of consumers based on the number of group ids, then SQS FIFO queue may be better and if you have 10000 trucks and need to send a lot of data, and have data ordering per shard then KDS are good choice.

**9. SQS vs SNS vs Kinesis**

|   SQS                                                                 |   SNS                                            |   Kinesis                                                                          |
|-----------------------------------------------------------------------|--------------------------------------------------|------------------------------------------------------------------------------------|
|   Consume pull data                                                   |   Pub-sub. Push model                            |   Standard: Pull data (2MB per shard)                                              |
|   Data is deleted after being consumed.                               |   Data is not persisted(lost if not delivered.)  |   Enhanced-fanout: push data (2MB per shard per consumer)                          |
|   Can have as many workers/consumers as we want.                      |   Upto 12,500,000 subscribers. 100,000 topics    |   Possibility to replay data.[data is persisted]                                   |
|   No need to provision throughput in advance as its managed service.  |   No need to provision throughput                |   Used for real time big data analytics and ETL.                                   |
|   Ordering guarantees only available on FIFO queues.                  |   Combine SNS FIFO + SQS FIFO.                   |   Ordering at shard level.[Shard no. specified in advance] [Scale shard yourself]  |
|   Individual msg delay capability.                                    |   Integrated with SQS for fan-out pattern        |   Data expires after X days [365 days].                                            |
|                                                                       |                                                  |   Capacity mode and on-demand mode                                                 |

The **capacity limits** of a Kinesis data stream are defined by the number of **shards** within the data stream. The limits can be exceeded by either data throughput or the number of reading data calls. Each shard allows for **1 MB/s incoming** data and **2 MB/s outgoing** data. You should increase the number of shards within your data stream to provide enough capacity.  

**Use case**: You are running an application that produces a large amount of real-time data that you want to load into S3 and Redshift. Also, these data need to be transformed before being delivered to their destination. 
**Kinesis Data Streams + Kinesis Data Firehose**  

Which is **NOT** a supported **subscriber** for **AWS SNS**?  
Kinesis Data Firehose is now supported, but not **Kinesis Data Streams**.  

**Amazon Kinesis Data Streams (KDS)** is a massively scalable and durable real-time data streaming service. It can continuously capture gigabytes of data per second from hundreds of sources such as website clickstreams, database event streams, financial transactions, social media feeds, IT logs, and location-tracking events.

**10. Amazon MQ** 
  - is not serverless and runs on a dedicated machine.
  - managed message broker service for Rabbit MQ and Active MQ
  - Doesn’t scale as much as SQS/SNS [which have sort of infinite scaling]
  - Can run in Multi-AZ with failover [for HA]
  - Has both queue and topic features [Like SQS and SNS]  
			
  - High availability is achieved by having Active and Stand-by MQ, in different AZ’s and then you define Amazon EFS as your backend storage.
  - EFS is a network file system which can be mounted onto multiple AZs.
  - So when failover happens, standby will be mounted onto the EFS, and hence we have the same data.  