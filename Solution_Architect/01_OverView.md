# Overview


**Cloud Computing**  
  - on-demand-delivery of compute power,database storage, applications, and other IT resources
  - pay-as-you-go pricing
  - provisions exactly the right size and and type of computing resources you need
  - access as many resources as you need, almost instantly.
	
AWS owns and maintains the network connected hardware required for these application services, while you provision and use what you need via a web application.

**Five characteristics of cloud computing**
1. On-demand self service
2. Broad network access
3. Multi-tenancy and resource pooling
4. Rapid elasticity and scaling
5. Measured services

**Six advantages of cloud computing**
1. Trade capital expenses(CAPEX) for operational expenses(OPEX) (Reduced Total Cost of Ownership(TCO))
2. Benefits from massive economies of scale
3. Stop guessing capacity
4. Increase speed and agility
5. Stop spending money running and maintaining data centre.
6. Go global in minutes.

**Problems solved by cloud**
1. Flexibility - change resource types when needed
2. Cost effectiveness
3. Scalability
4. Elasticity
5. High availability and fault tolerance
6. Agility - rapidly develop test and launch s/w applications

**AWS Regions**

  - AWS has Regions all around the world • Names can be us-east-1, eu-west-3...
  - A region is a cluster of data centers
  - Most AWS services are region-scoped
  - **How to Choose an AWS region?**
    - **Compliance** with data governance and legal requirements: data never leaves a region without your explicit permission
    - **Proximity** to customers: reduced latency
    - **Available services** within a Region: new services and new features aren’t available in every Region
    - **Pricing**: pricing varies region to region and is transparent in the service pricing page

**AWS Availability Zones**
  - Each region has many availability zones (usually 3, min is 3, max is 6). 
  - Example:
	- ap-southeast-2a, ap-southeast-2b, ap-southeast-2c
  - Each availability zone (AZ) is one or more discrete data centers with redundant power, networking, and connectivity
  - They’re separate from each other, so that they’re isolated from disasters
  - They’re connected with high bandwidth, ultra-low latency networking

**AWS Points of Presence (Edge Locations)**
  - Amazon has 400+ Points of Presence (400+ Edge Locations & 10+ Regional Caches) in 90+ cities across 40+ countries
  - Content is delivered to end users with lower latency

**AWS Services**

  - AWS has **Global Services**:
    - Identity and Access Management (IAM)
    - Route 53 (DNS service)
    - CloudFront (Content Delivery Network)
    - WAF (Web Application Firewall)
  - Most AWS services are **Region-scoped**:
    - Amazon EC2 (Infrastructure as a Service)
    - Elastic Beanstalk (Platform as a Service)
    - Lambda (Function as a Service)
    - Rekognition (Software as a Service)

**Pricing of the Cloud** -  Compute, Storage, and Data Transfer OUT

**Classic Ports to know**
- 22 = SSH (Secure Shell) - log into a Linux instance
- 21 = FTP (File Transfer Protocol) – upload files into a file share
- 22 = SFTP (Secure File Transfer Protocol) – upload files using SSH
- 80 = HTTP – access unsecured websites
- 443 = HTTPS – access secured websites
- 3389 = RDP (Remote Desktop Protocol) – log into a Windows instance

- PostgreSQL: 5432
- MySQL: 3306
- Oracle RDS: 1521
- MSSQL Server: 1433
- MariaDB: 3306 (same as MySQL)
- Aurora: 5432 (if PostgreSQL compatible) or 3306 (if MySQL compatible)

**Policy types**
  - 1 - **Identity-based policies** – Attach managed and inline policies to IAM identities (users, groups to which users belong, or roles). Identity-based policies grant permissions to an identity.

  - 2 - **Resource-based policies** – Attach inline policies to resources. The most common examples of resource-based policies are S3 bucket policies and IAM role trust policies. Grants permissions to the principal that is specified in the policy. Principals can be in the same account as the resource or in other accounts. The IAM service supports only one type of resource-based policy called a **role trust policy**, which is attached to an IAM role.

  - 3 - **Permissions boundaries** – Use a managed policy as the permissions boundary for an IAM entity (user or role). It does not grant permissions. Permissions boundaries do not define the maximum permissions that a resource-based policy can grant to an entity.

  - 4 - **Organizations SCPs** – Use an AWS Organizations service control policy (SCP) to define the maximum permissions for account members of an organization or organizational unit (OU). SCPs limit permissions that identity-based policies or resource-based policies grant to entities (users or roles) within the account, but do not grant permissions.

  - 5 - **Access control lists (ACLs)** – Use ACLs to control which principals in other accounts can access the resource to which the ACL is attached. ACLs are similar to resource-based policies, and only policy that does not use the JSON structure. ACLs are cross-account permissions policies that grant permissions to the specified principal. ACLs cannot grant permissions to entities within the same account.

  - 6 - **Session policies** – Pass advanced session policies when you use the AWS CLI or AWS API to assume a role or a federated user. Session policies limit the permissions that the role or user's identity-based policies grant to the session. Session policies limit permissions for a created session, but do not grant permissions.


**AWS services that can be used for buffering or throttling to handle sudden traffic spikes**  
- **Throttling** is the process of limiting the number of requests an authorized program can submit to a given operation in a given amount of time.
- **Amazon API Gateway**
  - Amazon API Gateway throttles requests to your API using the token bucket algorithm, where a token counts for a request.
  - API Gateway sets a limit on a steady-state rate and a burst of request submissions against all APIs in your account
  - In the token bucket algorithm, the burst is the maximum bucket size.
- **Amazon SQS**
  - Offers buffer capabilities to smooth out temporary volume spikes without losing messages or increasing latency.
- **Amazon Kinesis** 
  - Amazon Kinesis is a fully managed, scalable service that can ingest, buffer, and process streaming data in real-time.

- **Services which does not help with buffering/Throttling**
  - **SNS** cannot buffer messages and is generally used with SQS to provide the buffering facility.
  - **Lambda** When requests come in faster than your Lambda function can scale, or when your function is at maximum concurrency, additional requests fail as the Lambda throttles those requests with error code 429 status code.

**Blue/green deployment**
- <span style="color:blue">Blue</span>/<span style="color:green">green</span> deployment is a technique for releasing applications by shifting traffic between two identical environments running different versions of the application: "Blue" is the currently running version and "green" the new version.
- This type of deployment allows you to test features in the green environment without impacting the currently running version of your application.
- When you’re satisfied that the green version is working properly, you can gradually reroute the traffic from the old blue environment to the new green environment.
- Blue/green deployments can mitigate common risks associated with deploying software, such as downtime and rollback capability.

  - **AWS Global Accelerator** 
    - uses **endpoint weights** to determine the proportion of traffic that is directed to endpoints in an endpoint group, and **traffic dials** to control the percentage of traffic that is directed to an endpoint group (an AWS region where your application is deployed).
    - you can shift traffic gradually or all at once between the blue and the green environment and vice-versa without being subject to **DNS caching** on client devices and internet resolvers.
    - Traffic dials and endpoint weights changes are effective within seconds.
  - **Route53** 
    - Weighted routing lets you associate multiple resources with a single domain name (example.com) or subdomain name (acme.example.com) and choose how much traffic is routed to each resource.
    - This can be useful for a variety of purposes, including load balancing and testing new versions of the software
    - While relying on the DNS service is a great option for blue/green deployments, **it may not fit use-cases that require a fast and controlled transition of the traffic**.
    - Some client devices and internet resolvers **cache DNS answers** for long periods; this DNS feature improves the efficiency of the DNS service as it reduces the DNS traffic across the Internet, and serves as a resiliency technique by preventing authoritative name-server overloads. 
    - The **downside** of this in blue/green deployments is that you don’t know how long it will take before all of your users receive updated IP addresses when you update a record, change your routing preference or when there is an application failure.
  - **Elastic Load Balancing (ELB)**
    - Can distribute traffic across healthy instances
    - Can also use the Application Load Balancers weighted target groups feature for blue/green deployments as it does not rely on the DNS service.
    - In addition you don’t need to create new ALBs for the green environment.
    - **This option cannot be used for a multi-Region global solution**

**CodeDeploy**
  - CodeDeploy, a deployment is the process, and the components involved in the process, of installing content on one or more instances.
  - This content can consist of code, web and configuration files, executables, packages, scripts, and so on.
  - AWS CodeDeploy deploys content that is stored in a source repository, according to the configuration rules you specify. 
  - Blue/Green deployment is one of the deployment types that CodeDeploy supports
  - CodeDeploy is not meant to distribute traffic across instances

**AWS Resource Access Manager (AWS RAM)**

- is a service that enables you to easily and securely share AWS resources with any AWS account or within your AWS Organization.