# Overview

- When we start deploying multiple applications, they will inevitably need to communicate with one another
- There are two patterns of application communication
![Alt text](images/Communications.png)

- Synchronous between applications can be problematic if there are sudden spikes of traffic
- What if you need to suddenly encode 1000 videos but usually it’s 10?
- In that case, it’s better to decouple your applications
  - using SQS: queue model
  - using SNS: pub/sub model
  - using Kinesis: real-time streaming model
- These services can scale independently from our application!

# SNS 
  
![Alt text](images/PubSub.png)
- Simple Notification service
- The “event producer” only sends message to one SNS topic
- As many “event receivers” (subscriptions) as we want to listen to the SNS topic notifications
- Each subscriber to the topic will get all the messages (note: new feature to filter messages)
- Up to 12,500,000 subscriptions per topic
- 100,000 topics limit
![Alt text](images/SNS1.png)
- Many AWS services can send data directly to SNS for notifications
![Alt text](images/SNS2.png)
- can publish messages to emails, sms, mobile notifications, HTTP endpoints OR SQS, Lambda, S3, Kinesis Data Firehose.
- can receive msgs from CloudWatch Alarms, ASGs, CloudFormation(state changes), AWS buckets, S3, DMS (new replica), Lambda, DynamoDB, RDS events.
- **Standard SNS** - Best effort msg ordering, At least once message delivery, Highest throughput in publishes/sec, subscription protocols: SQS, Lambda, HTTP, SMS, email, mobile application endpoints
- **FIFO** - strictly preserved message ordering, exactly once message delivery, Highest throughput upto 300 publishes/sec. Subscription protocol : SQS

- **SNS – How to publish**
  - Topic Publish (using the SDK)
    - Create a topic
    - Create a subscription (or many)
    - Publish to the topic
  - Direct Publish (for mobile apps SDK)
    - Create a platform application
    - Create a platform endpoint
    - Publish to the platform endpoint
    - Works with Google GCM, Apple APNS, Amazon ADM...

- **SNS - Security** 
  - **Encryption**:
    - In-flight encryption using HTTPS API
    - At-rest encryption using KMS keys
    - Client-side encryption if the client wants to perform encryption/decryption itself
  - **Access Controls**: IAM policies to regulate access to the SNS API
  - **SNS Access Policies** (similar to S3 bucket policies)
    - Useful for cross-account access to SNS topics
    - Useful for allowing other services ( S3...) to write to an SNS topic

**SNS + SQS : Fan-out pattern** 

  ![Alt text](images/FanOut.png)  
  - Push once in SNS, receive in all SQS that are subscribers
  - Fully decoupled, no data loss
  - SQS allows for: data persistence, delayed processing and retries of work
  - Ability to add more SQS subscribers over time
  - Make sure SQS queue **access policy** allows for SNS to write.
  - Cross-Region Delivery: works with SQS queues in other regions

- **Use case 1: S3 Events to multiple queues**: 
  - For the same combination of **event type** (e.g object create) and prefix (eg images/), you can only have one S3 Event rule.
  - If you want to send the same S3 event to many SQS queues, use fan out
  - S3 object created -> Amazon S3 (event) -> SNS Topic -> Fan-out (multiple SQS + Lambda functions + ..)
  ![Alt text](images/Fanout1.png)

- **Use case 2: SNS to Amazon S3 through Kinesis Data Firehose**: 
  - SNS can send to Kinesis Data Firehose and then to S3 or any supported KDF destination
  ![Alt text](images/Fanout2.png)

- **SNS – FIFOTopic**
  - FIFO = First In First Out (ordering of messages in the topic)
  ![Alt text](images/SQSFifo.png)
  - Similar features as SQS FIFO:
    - **Ordering** by Message Group ID (all messages in the same group are ordered)
    - **Deduplication** using a Deduplication ID or Content Based Deduplication
  - **Can have SQS Standard and FIFO queues as subscribers**
  - Limited throughput (same throughput as SQS FIFO)

- **SNS FIFO + SQS FIFO: Fan Out**
	- In case you need fan out + ordering + deduplication
  ![Alt text](images/Fanout3.png)

- **SNS – Message Filtering**
	- JSON policy used to filter messages sent to SNS topics subscription. For ex if a new transaction messages has a field called state then we can create a filter policy for state as placed (for placed orders), another policy for cancelled, declined etc which all go to different SQS queue and may have another SQS queue to have all the messages unfiltered.
  - If a subscription doesn’t have a filter policy, it receives every message
  ![Alt text](images/MessageFiltering.png)

# Amazon MQ

- Traditional applications running from on-premises may use open protocols such as: MQTT, AMQP, STOMP, Openwire, WSS
- **When migrating to the cloud**, instead of re-engineering the application to use SQS and SNS, we can use Amazon MQ
- **Is not serverless and runs on a dedicated machine.**
- **Managed message broker service for Rabbit MQ and Active MQ**
- SQS, SNS are “cloud-native” services: proprietary protocols from AWS
- Doesn’t scale as much as SQS/SNS [which have sort of infinite scaling]
- Can run in Multi-AZ with failover [for HA]
- Has both queue and topic features [Like SQS and SNS]  

- **Amazon MQ – High Availability**
![Alt text](images/MQ_HA.png)
- High availability is achieved by having Active and Stand-by MQ, in different AZ’s and then you define Amazon EFS as your backend storage.
- EFS is a network file system which can be mounted onto multiple AZs.
- So when failover happens, standby will be mounted onto the EFS, and hence we have the same data.  