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