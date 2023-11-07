# AWS Lambda

 **AWS Lambda - Why?**
  - Amazon EC2 - Virtual servers in Cloud  
    - Limited by RAM and CPU  
	- Continuously running [we can optimize by starting and stopping efficiently]  
	- Scaling means intervention to add/remove servers [for ex with ASG]  
  - AWS Lambda - Virtual functions, no servers to manage  
    - Limited by time - **short executions** (upto 15 mins)  
	- Run on-demand [when not using its not running and billed for duration when its running]  
	- Scaling is automated.   

  - Benefits   
    - Easy pricing - Pay per request and compute time  
	- Free tier of 1,000,000 AWS Lambda requests and 400,000 GBs of compute time  
	- Integrated with the whole AWS suite of services  
	- Integrated with many programming languages [Node.js, Python, Java, C#, GoLang, Ruby, Custom runtime API(ex Rust with community support)]  
	- Support for Lambda container Image - Container image that run on Lambda, must implement the Lambda runtime API. ECS/Fargate is preferred for running arbitrary Docker images.  
	- Easy monitoring through AWS CloudWatch  
	- Easy to get more resources per functions (upto 10 GB of RAM)  
	- Increasing RAM will also improve CPU and n/w.  
  	
  - Integrations with 
    - API Gateway (create REST APIs that invokes Lambda functions)  
    - Kinesis (for data transformation on the fly)  
	- DynamoDB (create triggers when something happens in the DB)  
	- S3 (ex lambda function will be triggered when a file is created)  
	- CloudFront (Lambda Edge)  
	- CloudWatch Events EventBridge (ex if something happens to our infra and we want to react, ex we have a cut pipeline, state changes, and we want to do automation based on it)  
	- SNS (react to notifications )  
	- SQS (process messages)  
	- Cognito (react when a user logs into a DB  


##Examples

Case 1: **Serverless Thumbnail Creation**

A reactive architecture to the events of a new image being created in S3.

![Alt text](<Example Serverless Thumbnail creation.png>)