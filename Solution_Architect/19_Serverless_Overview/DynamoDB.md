# Amazon Dynamo DB
<p>

- Fully managed, highly available with replication across multiple AZs  
  NoSQL - not a relational DB - with transaction support  
  Scales to massive workloads, distributed DB  
  Millions of requests per second, trillions of row, 100s of TB of storage  
  Fast and consistent in performance (single digit milliseconds)  
  Integrated with IAM for security, authorization and administration  
  Low cost and auto scaling capability  
  No maintenance or patching, always available  
  Standard and Infrequent Access (IA) Table class  

- **DynamoDB Basics**
    - Dynamo DB is made of **Tables**. [No need to create a DB, the DB is available as a service]  
    - Each table has a **primary key** (must be decided at creation time)(optionally sort key which is a part of the primary key)  
	- Each table can have an infinite number of **items (=rows)**
	- Each item has **attributes** (can be added over time - can be null)
	- In RDS/Aurora you can add columns later but it’s complicated, can be difficult to evolve schemas. In Dynamo DB it’s easy to add column or attributes later.
	- Max size of an item is **400KB**
	- **Data types** supported are: Scalar Types (String, Number, Binary, Boolean, Null), Document Types (List, Map), Set Types (String set, Number set, Binary set)
	- **Therefore in DynamoDB you can rapidly evolve schemas.**

- **DynamoDB Capacity**
	- Dynamo - Read/Write Capacity Modes
	- Control how you manage your tables capacity (read/write throughput)
	- **Provisioned Mode**(default) 
        - you specify the number of reads/writes per second.
		- You need to Plan capacity **beforehand**
		- Pay for **provisioned** Read Capacity Units (**RCU**) & Write Capacity Units (**WCU**)
		- Possibility to add **auto-scaling** mode for RCU & WCU
	- **On-Demand Mode** 
        - Read/writes automatically scale up/down with your workloads
		- **No capacity** planning needed.
		- Pay for what you use, **more expensive**
		- Great for **unpredictable** workloads, steep sudden spikes.

- **DynamoDB Advance Features**
	- DynamoDB Accelerator (DAX)
        - Fully managed, highly available, seamless in-memory cache for Dynamo DB.
		- Help solve read congestion by caching data. 
		- Microseconds latency for cached data.
		- Doesn’t require application logic modification (compatible with existing DynamoDB APIs)
		- The cache has a TTL of 5 minute.


</p>