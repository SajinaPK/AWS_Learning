# CloudWatch Vs CloudTrail Vs Config

- **CloudWatch**
    - Performance monitoring (metrics, CPU, network, etc ..) & dashboard
    - Events & Alerting
    - Log aggregation and Analysis
    
- **CloudTrail**
    - Record API calls made within your Account by everyone
    - Can define trails for specific resources
    - Global service

- **Config**
    - Record configuration changes
    - Evaluate resources against compliance rules
    - Get timeline of changes and compliance

# Example for an Elastic Load Balancer
    
  - **CloudWatch**:
    - Monitoring Incoming connections metris
    - Visualize error codes as a % over time
    - Make a dashboard to get an idea of your load balancer performance (for a global application we can have a global dashboard)
  - **Config**
    - Track security group rules for the Load Balancer (track noone does anything fishy or changes anything)
    - Track configuration changes for the Load Balancer (can also have a a rule to say an SSL certificate should be assigned to the load balancer)
    - Ensure an SSL certificate is always assigned to the load balancer (compliance) (never allow non-encrypted traffic into load balancer)
  - **CloudTrail**
    - Track who made changes to the Load Balancer with API calls (ex who changed security group rules, who changed SSL certificates, or removed it)