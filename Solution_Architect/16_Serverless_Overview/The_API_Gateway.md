# API Gateway Overview

- **Building a server less API**  
    - Lambda functions can do CRUD on the tables in DynamoDB  
    - How to enable clients to use these Lambda functions- multiple ways to do this  
        1. Client can directly invoke the Lambda functions which means they need **IAM permissions** to do this.  
        2. Use **Application Load balancer** between client and Lambda functions and expose the functions as HTTP endpoint.  
        3. Or Use **API Gateway** between client and Lambda functions. Serverless offering which allows us to create REST APIs that are going to be public and accessible for our clients. Clients talk to the API gateway which will then proxy the request to Lambda functions.  
  
- **API Gateway**   
	- Lambda + API Gateway : No infrastructure to manage  
	- Support for the WebSocket API [real time streaming]  
	- Handle API versioning   
	- Handle different environments (dev, test, prod)  
	- Handle security ( Authentication and Authorization)  
	- Create API keys, handle request throttling (in case some clients send too many requests)  
	- Use common standards like Swagger / Open API 3.0 to import quickly defined APIs  
	- Transform and validate requests and response  
	- Generate SDK and API specifications   
	- Cache API responses  
  
## API Gateway Integrations  
- **Lambda**   
	- Invoke Lambda function 	[Full server less application]  
	- Easy way to expose REST API backed by Lambda  
- **HTTP**  
	- Expose HTTP endpoints in the backend  
	- Ex: Internal HTTP API on premise, Application Load Balancer ..  
	- Why? Add rate limiting, caching, user authentication, API keys etc..  
- **AWS Service**  
	- Expose any AWS API through API Gateway?  
	- Ex, start an AWS Step Function workflow, post a message to SQS  
	- Why? Add authentication, deploy publicly, rate control  
- **Others**
	- Mock (Generate a response based on API mappings and transformations)
	- VPC Link (Integrate with a resource not available over public internet)

    ![Alt text](<images/API Gateway - AWS Service Integration.png>)
  
- **API Gateway - Endpoint types**  
	- There are 3 ways to deploy API Gateways also called endpoint types.  
	- **Edge-optimized** (default): For global clients (API gateway accessible from anywhere in the world)  
		- Requests are routed through the CloudFront Edge locations (improves latency and efficiency)  
		- API Gateway still lives in the region where created (but accessible from every Edge location)  
	- **Regional**:
		- Expect all users to be within the same region  
		- Could manually combine with CloudFront (will give the same result as edge-opimized but more control over location and caching strategies and the distribution setting itself)  
	- **Private**:
		- Can only be accessed from your VPC using an interface VPC endpoint (ENI)  
		- Use a resource policy to define access  

- **API Gateway - Security**  
	- **User Authentication** through
		- IAM Roles (useful for internal applications, ex apps running on EC2 instances, which wants to access the API in the API Gateway)  
		- Cognito (identity for external users - ex mobile users/web apps)  
		- Custom Authorizer (your own login, using Lambda function)    
	- **Custom Domain Name HTTPS** security through integration with AWS Certificate Manager (ACM)  
		- If using Edge-Optimized endpoint, then the certificate must be in **us-east-1**  
		- If using Resional endpoint, the cetificate must be in the same region as the API Gateway  
		- Must setup **CNAME** or A-alias record in Route 53.   