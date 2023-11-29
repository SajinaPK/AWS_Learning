# EC2

- EC2 = Elastic Compute Cloud = Infrastructure as a Service
- It mainly consists in the capability of : 
    - Renting virtual machines (**EC2**)
    - Storing data on virtual drives (**EBS**)
    - Distributing load across machines (**ELB**)
    - Scaling the services using an auto-scaling group (**ASG**)

- **EC2 sizing & configuration options**

    - Operating System (**OS**): Linux, Windows or Mac OS
    - How much compute power & cores (**CPU**)
    - How much random-access memory (**RAM**)
    - How much storage space:
        - Network-attached (**EBS & EFS**)
        - hardware (**EC2 Instance Store**)
    - Network card: speed of the card, Public IP address
    - Firewall rules: **security group**
    - Bootstrap script (configure at first launch): **EC2 User Data**

- **EC2 User Data**
    - It is possible to bootstrap our instances using an EC2 User data script.
    - **bootstrapping** means launching commands when a machine starts
    - That script is **only run once** at the instance **first start**
    - EC2 user data is used to automate boot tasks such as:
        - Installing updates
        - Installing software
        - Downloading common files from the internet
        - Anything you can think of
    - The EC2 User Data Script runs with the **root user**

# EC2 Instance 

- AWS has the following naming convention: **m5.2xlarge**
    - m: instance class
    - 5: generation (AWS improves them over time)
    - 2xlarge: size within the instance class

- **EC2 Instance Types – General Purpose**
    - Great for a diversity of workloads such as web servers or code repositories
    - Balance between:
        - Compute
        - Memory
        - Networking
    - Ex: t2.micro

- **EC2 Instance Types – Compute Optimized**
    - Great for compute-intensive tasks that require high performance processors:
        - Batch processing workloads
        - Media transcoding
        - High performance web servers
        - High performance computing (HPC)
        - Scientific modeling & machine learning
        - Dedicated gaming servers

- **EC2 InstanceTypes – Memory Optimized**
    - Fast performance for workloads that process large data sets in memory
    - Use cases:
        - High performance, relational/non-relational databases
        - Distributed web scale cache stores
        - In-memory databases optimized for BI (business intelligence)
        - Applications performing real-time processing of big unstructured data

- **EC2 InstanceTypes – Storage Optimized**
    - Great for storage-intensive tasks that require high, sequential read and write access to large data sets on local storage
    - Use cases:
        - High frequency online transaction processing (OLTP) systems
        - Relational & NoSQL databases
        - Cache for in-memory databases (for example, Redis)
        - Data warehousing applications
        - Distributed file systems

- **EC2 Instance Types - example**

![Alt text](images/InstanceExample.png)

- **Classic Ports to know**
    - 22 = SSH (Secure Shell) - log into a Linux instance
    - 21 = FTP (File Transfer Protocol) – upload files into a file share
    - 22 = SFTP (Secure File Transfer Protocol) – upload files using SSH
    - 80 = HTTP – access unsecured websites
    - 443 = HTTPS – access secured websites
    - 3389 = RDP (Remote Desktop Protocol) – log into a Windows instance

- **EC2 Instance Connect**
    - Connect to your EC2 instance within your browser
    - No need to use your key file that was downloaded
    - The “magic” is that a temporary key is uploaded onto EC2 by AWS
    - **Works only out-of-the-box with Amazon Linux 2**
    - Need to make sure the port 22 is still opened!

- **EC2 Instances Purchasing Options**

    - **1. On Demand** - short workload, predictable pricing, pay by second
        - Pay for what you use:
            - Linux or Windows - billing per second, after the first minute
            - All other operating systems - billing per hour
        - Has the highest cost but no upfront payment
        - No long-term commitment
        - Recommended for **short-term** and **un-interrupted workloads**, where you can't predict how the application will behave

    - **2. Reserved Instances** - (1 & 3 years)- Reserved Instances (long workloads), Convertible Reserved Instances (long workloads with flexible instances)
        - Up to **72%** discount compared to On-demand
        - You reserve a specific instance attributes (**Instance Type, Region,Tenancy, OS**)
        - **Reservation Period** – **1 year** (+discount) or **3 years** (+++discount)
        - **Payment Options – No Upfront (+), Par tial Upfront (++), All Upfront (+++)**
        - **Reserved Instance’s Scope – Regional or Zonal** (reserve capacity in an AZ)
        - Recommended for steady-state usage applications (think database)
        - You can buy and sell in the Reserved Instance Marketplace
        - **Convertible Reserved Instance**
            - Can change the EC2 instance type, instance family, OS, scope and tenancy
            - Up to 66% discount

    - **3. Savings Plans** - (1 & 3 years) –commitment to an amount of usage, long workload
        - Get a discount based on long-term usage (up to 72% - same as RIs)
        - Commit to a certain type of usage ($10/hour for 1 or 3 years)
        - Usage beyond EC2 Savings Plans is billed at the On-Demand price
        - Locked to a specific instance family & AWS region (e.g., M5 in us-east-1)
        - Flexible across:
            - Instance Size (e.g., m5.xlarge, m5.2xlarge)
            - OS (e.g., Linux, Windows)
            - Tenancy (Host, Dedicated, Default)

    - **4. Spot Instances** – short workloads, cheap, can lose instances (less reliable)
        - Can get a **discount of up to 90%** compared to On-demand
        - Instances that you can “lose” at any point of time if your max price is less than the current spot price
        - The **MOST cost-efficient** instances in AWS
        - **Useful for workloads that are resilient to failure**
            - Batch jobs
            - Data analysis
            - Image processing
            - Any distributed workloads
            - Workloads with a flexible start and end time
        - **Not suitable for critical jobs or databases**

    - **5. Dedicated Hosts** – book an entire physical server, control instance placement
        - A physical server with EC2 instance capacity fully dedicated to your use
        - Allows you address **compliance requirements** and **use your existing server- bound software licenses** (per-socket, per-core, per—VM software licenses)
        - Purchasing Options:
            - **On-demand** – pay per second for active Dedicated Host
            - **Reserved** - 1 or 3 years (No Upfront, Partial Upfront,All Upfront)
        - The most expensive option
        - Useful for software that have complicated licensing model (BYOL – Bring Your Own License)
        - Or for companies that have strong regulatory or compliance needs

    - **6. Dedicated Instances** – no other customers will share your hardware
        - Instances run on hardware that’s dedicated to you
        - May share hardware with other instances in same account
        - No control over instance placement (can move hardware after Stop / Start)

    - **7. Capacity Reservations** – reserve capacity in a specific AZ for any duration
        - Reserve **On-Demand** instances capacity in a specific AZ for any duration
        - You always have access to EC2 capacity when you need it
        - **No time commitment** (create/cancel anytime), **no billing discounts**
        - Combine with Regional Reserved Instances and Savings Plans to benefit from billing discounts
        - You’re charged at On-Demand rate whether you run instances or not
        - Suitable for short-term, uninterrupted workloads that needs to be in a specific AZ
    ![Alt text](images/DedicatedHostVsInstance.png)

    - **Choosing the Instance Type**
        - **On demand**: coming and staying in resort whenever we like, we pay the full price
        - **Reserved**: like planning ahead and if we plan to stay for a long time, we may get a good discount.
        - **Savings Plans**: pay a certain amount per hour for certain period and stay in any room type (e.g., King, Suite, Sea View, ...)
        - **Spot instances**: the hotel allows people to bid for the empty rooms and the highest bidder keeps the rooms.You can get kicked out at any time
        - **Dedicated Hosts**: We book an entire building of the resort
        - **Capacity Reservations**: you book a room for a period with full price even you don’t stay in it

# Security Groups

- Security Groups are the fundamental of network security in AWS
- They control how traffic is allowed into or out of our EC2 Instances.
- Security groups only contain **allow** rules
- Security groups rules can reference by IP or by security group
- Security groups are acting as a “firewall” on EC2 instances
- They regulate:
    - Access to Ports
    - Authorised IP ranges – IPv4 and IPv6
    - Control of inbound network (from other to the instance)
    - Control of outbound network (from the instance to other)
![Alt text](images/SecurityGroup.png)

- **Security Groups**
![Alt text](images/SecurityGroup2.png)

- **Reference other security groups**
![Alt text](images/SecurityGroup3.png)

- **Good to Know**
    - Can be attached to multiple instances
    - Locked down to a **region / VPC** combination
    - Does live “outside” the EC2 – if traffic is blocked the EC2 instance won’t see it
    - It’s good to maintain one separate security group for SSH access
    - If your application is not accessible (**time out**), then it’s a security group issue
    - If your application gives a “**connection refused**“ error, then it’s an application error or it’s not launched
    - All inbound traffic is **blocked** by default
    - All outbound traffic is **authorised** by default