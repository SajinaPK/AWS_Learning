# AWS_Learning
AWS Learning Notes

https://www.credly.com/badges/0a6f1643-88a7-4f46-a8ac-7cc5cccf6430/public_url


1. **Kinesis Data Stream** cannot scale the numbers of consumers as we can only have as many consumers as shards in Kinesis.
2. With Amazon Kinesis Data Streams, you can scale up to a sufficient number of shards. You'll need to provision enough shards ahead of time and requires manual administration of shards
3. Kinesis Data Firehose a fully managed service that automatically scales to match the throughput of your data and requires no ongoing administration
4. Kinesis Agent is a stand-alone Java software application that offers an easy way to collect and send data to Amazon Kinesis Data Streams or Amazon Kinesis Firehose. 
5. Kinesis Agent cannot write to Amazon Kinesis Firehose for which the delivery stream source is already set as Amazon Kinesis Data Streams. 
6. In **SQS with FIFO**, to allow for multiple consumers to read data for each application, and to scale the number of consumers, we should use the "Group ID" attribute
7. **s3:ListBucket** is applied to buckets, so the ARN is in the form "Resource":"arn:aws:s3:::mybucket", without a trailing / s3:GetObject is applied to objects within the bucket, so the ARN is in the form "Resource":"arn:aws:s3:::mybucket/*", with a trailing /* to indicate all objects within the bucket mybucket
8. Highly parallelized applications and workloads, such as big data analysis, media processing, and genomic analysis benefit from **Max I/O performance mode**
9. You cannot directly integrate **Cognito User Pools** with CloudFront distribution as you have to create a separate AWS Lambda@Edge function to accomplish the authentication via Cognito User Pools. 
10. **ALB** can be used to securely authenticate users for accessing your applications. This enables you to offload the work of authenticating users to your load balancer so that your applications can focus on their business logic. You can use Cognito User Pools to authenticate users via ALB.
11. You cannot convert an existing single-Region key to a multi-Region key.
12. A secondary **elastic network interface** can be added to an EC2 instance. While primary network interfaces cannot be detached from an instance, secondary network interfaces can be detached and attached to a different EC2 instance.
13. IAM permission boundary can only be applied to roles or users, not IAM groups.
14. CloudFormation takes time to provision the resources and hence is not the right solution when LEAST amount of downtime is mandated
15. By default, Amazon EC2 Auto Scaling doesn't use the results of ELB health checks to determine an instance's health status
16. When a custom health check determines that an instance is unhealthy, the check manually triggers SetInstanceHealth and then sets the instance's state to Unhealthy. Amazon EC2 Auto Scaling then terminates the unhealthy instance.
17. **Resource Access Manager (RAM)** is a service that enables you to easily and securely share AWS resources with any AWS account or within your AWS Organization. RAM eliminates the need to create duplicate resources in multiple accounts, reducing the operational overhead of managing those resources in every single account you own.
18. You can only change the tenancy of an instance from dedicated to host, or from host to dedicated after you've launched it
19. **Service control policy (SCP)** does not affect any service-linked role.
20. If either Launch Configuration **Tenancy** or VPC Tenancy is set to dedicated, then the instance tenancy is also dedicated.
21. If your instance has a public IPv4 address, it retains the public IPv4 address after **recovery**.
22. **Alias records** let you route traffic to selected AWS resources, such as Amazon CloudFront distributions and Amazon S3 buckets. They also let you route traffic from one record in a hosted zone to another record.
23. 'Alias record' cannot be used to map one domain name to another.
24. If you have objects that are smaller than 1GB or if the data set is less than 1GB in size, you should consider using Amazon **CloudFront's PUT/POST** commands for optimal performance.
25. **Elasticache** is used as a caching layer. It's not a fully managed NoSQL database.
26. You can't convert an existing standard queue into a FIFO queue. To make the move, you must either create a new **FIFO queue** for your application or delete your existing standard queue and recreate it as a FIFO queue.
27. **GuardDuty** identifies any unusual or unauthorized activity, like cryptocurrency mining or infrastructure deployments in a region that has never been used.
28. Amazon **Kinesis Data Firehose** is the easiest way to capture, transform, and load streaming data into Redshift for near real-time analytics.
29. Amazon **RDS** applies operating system updates by performing maintenance on the standby, then promoting the standby to primary and finally performing maintenance on the old primary, which becomes the new standby
30. Amazon **EFS** has a higher throughput than Amazon EBS.
31. You can't detach an instance store volume from one instance and attach it to a different instance
32. When you stop, hibernate, or terminate an instance, every block of storage in the instance store is reset.
33. **Firewall Manager**, you can centrally configure AWS WAF rules, AWS Shield Advanced protection, Amazon Virtual Private Cloud (VPC) security groups, AWS Network Firewalls, and Amazon Route 53 Resolver DNS Firewall rules across accounts and resources in your organization.
34. **AWS DMS** lets you expand the existing application to stream data from Amazon S3 into Amazon Kinesis Data Streams for real-time analytics without writing and maintaining new code
35. There is no such thing as a cross-region Multi-AZ deployment. RDS Multi-AZ deployments provide enhanced availability for database instances within a single AWS Region. 
36. **Amazon Quicksight** is not an SQL query based analysis tool like Amazon Athena.
37. **AWS DataSync** cannot be used to facilitate ongoing updates to the migrated files from the on-premises applications.
38. There are no standby instances in **Aurora**. Aurora performs an automatic failover to a read replica when a problem is detected.
39. **S3 Standard** has no minimum storage duration charge and no retrieval fee. S3 Standard IA and S3 Standard IA One zone has minimum storage duration charge is 30 days.
