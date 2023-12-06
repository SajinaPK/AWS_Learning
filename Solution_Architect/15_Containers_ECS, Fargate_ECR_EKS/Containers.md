
# Containers on AWS: ECS, Fargate, ECR & EKS
  
**Docker** is a s/w development platform to develop app  
Apps are packaged in containers that can run on any OS  
Apps run the same, regardless of where they run: any machine, No compatibility issues, predictable behaviour, Less Work, Easier to maintain and deploy, Works with any language, OS and technology  

**Use case**: micro service architecture, lift and shift apps from on-prem to AWS cloud.

**Where are docker images stored?** 
- Repositories
	- **Docker Hub** - public repository, Find base images of many technologies or OS like Ubuntu, mySQL
	- **Amazon ECR** - private repository, public repository (Amazon ECR public gallery)

![Alt text](images/Docker1.png)  

**Docker container mgmt on AWS** 
- Amazon Elastic Container Service (Amazon ECS) = Amazon’s own container platform
- Amazon Elastic Kubernetes Service (Amazon EKS)= Amazon’s managed Kubernetes (open source)
- AWS Fargate = Amazon’s own Serverless container platform = Works with ECS and with EKS
- Amazon ECR = Store container images


# ECR 

- ECR = Elastic Container Registry
- Store and manage Docker images on AWS
- Private and Public repository (Amazon ECR Public Gallery https://gallery.ecr.aws)
- Fully integrated with ECS and backed by S3.
- Access is controlled through IAM.
- Supports image vulnerability scanning, versioning, image tags, image lifecycle. 
![Alt text](images/ECR.png)

# ECS 

- Elastic Container Service
- Launch container on AWS = Launch **ECS tasks** on ECS clusters
- **EC2 Launch Type**
    - made of EC2 instances
	- You must provision and maintain infra (the EC2 instances)
	- Means your EC2 cluster is composed of multiple EC2 instances, each instance runs an ECS agent, each agent registers its EC2 instance to ECS service and the specified EC2 cluster.
	- Once this is in place, when you start ECS tasks, then AWS will start to stop containers.
	- When we have new docker container, it’s going to be places accordingly, on each EC2 instance over time.
	- You can start/stop the ECS task.
	- Docker containers are placed on Amazon EC2 instances that we provision in advance.
- Each EC2 Instance must run the ECS Agent to register in the ECS Cluster
- AWS takes care of starting / stopping containers
![Alt text](images/ECSCluster.png)

- **Fargate Launch Type**  
	- Launch Docker containers on AWS
	- **You do not provision the infrastructure. Serverless.** (no EC2 instances to manage)
	- You just create task definitions
	- When tasks are defined, AWS runs the tasks based on the CPU/RAM you need.
	- To scale, just increase the number of tasks. Simple - no more EC2 instances
![Alt text](images/FargateLaunch.png)

- Third option is to use External instances using **ECS Anywhere**.

- **IAM roles for ECS**
    - **1 EC2 instance profile [for EC2 launch type only]**  
        - used by ECS agents.
		- ECS agents will use this profile to make API calls to ECS (for ex to restore the instance), API calls to CloudWatch Logs (to send container logs), API calls to ECR (to pull images) and reference sensitive data in Secrets Manger or SSM Parameter store.

    - **2 ECS Task Role [for both launch types]**
        - Allows each task to have specific role. 
		- Use different roles for different ECS services you run. Ex EC2 task role can allow you to run API calls against S3, another EC2 task role may allow to run API against DynamoDB.
		- define the task role in the **task definition** of the **ECS service**.
	![Alt text](images/ECS_IAMRoles.png)

- **Load Balancer Integrations** 
    - If all the ECS tasks are running in a ECS cluster and we want to expose these tasks as HTTP(or HTTPS) endpoints, then we can run an application load balancer in front of it, and all users will be going to this ALB and in the back end to the ECS tasks directly.
	- Network Load Balancer - recommended only for high throughput/ high performance use cases, or to pair with AWS private link.
	- Classic Load Balancer - supported not recommended (no advance features, no Fargate)
	![Alt text](images/ECS_LB.png)

- **Data volumes (EFS**)  
    - Data persistence through data volumes.
	- Mount EFS file system onto ECS Tasks
	- Works for both EC2 and Fargate
	- Allows us to mount file system directly onto our ECS tasks because when tasks running in any AZ linked to this EFS, it will share data.
	- **Fargate + EFS = Serverless** 
	- **Use case**:  Persistent Multi AZ shared storage for your containers.
		- note: S3 cannot be mounted as a file system on your tasks.
	![Alt text](images/ECS_EFS.png)

First your create a cluster, then inside you can define an ASG with 1-5 capacity, in this case at launch itself we can see one container in the cluster. We can then define task definitions, and then launch this task definition as **service** (for long running computing work for ex web application) or as a **task** (standalone task that runs and terminates for ex a batch job). 

- **ECS Service Auto Scaling** 
    - Automatically increase/decrease number of ECS tasks
	- AWS ECS **auto scaling** uses AWS **application auto scaling**
		- ECS service average CPU utilization
		- ECS service average memory utilization- scale on RAM
		- ALB request count per target - metric coming from ALB
	- Based on the above 3 metric different kinds of auto scaling can be set up like
		- **Target tracking** (scale based on target value of one of the 3 metric for a specific CloudWatch metric)
		- **Step Scaling** (scale based on a specific CloudWatch Alarm)
		- **Scheduled Scaling** (scale based on a specified date/time thanks to predictable changes)
	- ECS Service Auto Scaling (task level) **not equal to** EC2 Auto scaling (EC2 instance level)
	- Fargate Auto Scaling is much easier to setup (because **Serverless**)
	- For EC2 Launch type the **ECS service** scaling can be done by adding more EC2 instances which can be done in 2 ways. 
		- **1 ASG scaling**
			- scale based on CPU utilization
			- Add EC2 instances over time
        - **2 ECS Cluster** 
			- Used to automatically provision and scale the infrastructure for your ECSTasks
			- Capacity Provider paired with an Auto Scaling Group
			- Add EC2 Instances when you’re missing capacity (CPU, RAM...)

- **ECS Scaling – Service CPU Usage Example**
![Alt text](images/ECS_Scaling.png)
ECS auto scaling ex can be based on Cloudwatch metric which monitors the CPU usage at the ECS service level then if the value of CPU usage goes up then it will trigger a CloudWatch alarm which will trigger a scaling activity in your ECS Auto scaling for your service. Optionally if ASG and cluster capacity provider is used then more EC2 instances will be added or the ASG will be scaled.

## Solution architectures:

Case 1: **ECS Tasks invoked by Event Bridge**
  - We have a VPC inside that we have our AWS cluster backed by Fargate.
  - We have S3 buckets and users upload objects /images into this
  - The S3 buckets can be integrated with Amazon Event bridge to send all the events to it.
  - The event bridge can have a rule to run ECS tasks on the go.
  - When ECS tasks are going to be creates then they will have task roles associated with them.
  - The task can be to get the objects, process it, and then send the results to Amazon Dynamo DB (the task role will allow this)
  - This is a server less architecture to process images/ objects from S3 using docker containers.

![Alt text](images/ECS%20tasks%20invoked%20by%20Event%20Bridge.png)

Case 2: **ECS tasks invoked by Event Bridge Schedule**
  - In the Event bridge we schedule a rule to be triggered every 1 hour.
  - this rule will run ECS tasks for us in fargate. Every 1 hour a new task will be created.
  - For ex the task can run some batch processing against some file in S3 (task role with access to S3 so the docker container will also have access to it)

  ![Alt text](images/ECS%20tasks%20invoked%20by%20Event%20Bridge%20Schedule.png)

Case 3: **SQS queue example**
  - A service on ECS with 2 ECS tasks and messages are being sent to the SQS queue and the service is pulling messages from the SQS queue and processing them.
  - Can enable ECS service auto scaling on top of the service.
  - So if more msgs in the SQS queue then more tasks will be created on the ECS service. 

  ![Alt text](images/ECS%20-%20SQS%20Queue%20Example.png)

Case 4: **Intercept stopped tasks using Event Bridge**
  - If you want to react to tasks being exited. So this can be triggered as an event in the Event bridge.
  - From there we could alert an SNS topic, and send emails to our administrators.
  - EventBridge hence can be used to understand the lifecycle of the containers in the ECS cluster. 

  ![Alt text](images/ECS%20-%20Intercept%20Stopped%20Tasks%20using%20EventBridge.png)


# EKS

- Amazon EKS = Amazon Elastic Kubernetes Service
- It is a way to launch **managed Kubernetes clusters on AWS**
- Kubernetes is an **open-source system** for automatic deployment, scaling and management of containerized (usually Docker) application
- It’s an alternative to ECS, similar goal but different API
- EKS supports **EC2** if you want to deploy worker nodes or **Fargate** to deploy serverless containers
- Use case: if your company is already using Kubernetes on-premises or in another cloud, and wants to migrate to AWS using Kubernetes
- **<u>Kubernetes is cloud-agnostic</u>** (can be used in any cloud – Azure, GCP...)
- For multiple regions, deploy one EKS cluster per region
- Collect logs and metrics using **CloudWatch Container Insights**

![Alt text](images/EKS.png)

- **EKS – Node Types**
	- **1 Managed Node Groups** - 
		- Creates and manages nodes (EC2 instances) for you
		- nodes are part of ASG managed by EKS
		- Supports On-demand or spot instances
	- **2 Self Managed Nodes**
		- Nodes created by you and registered to EKS and managed by ASG
		- supports On demand or spot instances
		- you can use pre-built AMI - Amazon EKS Optimized AMI.
	- **3 AWS Fargate** - No maintenance required, no nodes managed

- **EKS - Data Volume**
	- Need to specify **StorageClass** manifest on your EKS cluster
	- Leverages a **Container Storage Interface** (CSI) compliant driver
	- Support for
		- Amazon EBS
		- Amazon EFS (works with fargate)
		- Amazon FSx for Lustre
		- Amazon FSx for NetApp ONTAP.

# AWS App Runner
    
- Fully managed service that makes it easy to deploy web applications and APIs at scale.
- No infrastructure expertise required 
- Start with your source code repository or container image registry -> configure settings like CPU, RAM, Auto scaling, health check etc, -> Create and Deploy -> and then Access using URL.
- Automatically build and deploy the web app
- Automatic scaling, highly available, load balancer, encryption
- VPC access support
- Behind the scenes, deploy ASG, scaling triggers, fargate containers, load balancer, give a domain etc etc
- Connect to DB, cache, MQ services etc
- **Use case**: Quickly deploy web apps, API, micro services, and do rapid production deployment using best practices.
![Alt text](images/AppRunner.png)

**ECS Task Role** is the IAM Role used by the ECS task itself. Use when your container wants to call other AWS services like S3, SQS, etc.