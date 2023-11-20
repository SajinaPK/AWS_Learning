# IAM Advanced Policies

- **IAM Conditions**
    - Apply to policies within IAM. (like policies for users, resource policies, S3 buckets, endpoint policies ...)
    - **aws:SourceIp** restrict the client IP **from** which the API calls are being made.
    ![Alt text](images/SourceIP_Condition.png)
    (This has Deny star on everything, unless the client makes API calls from within the specified IP address ranges.)  
    (For ex this can be used to restrict usage of AWS only to your company n/w)  
    - **aws:RequestedRegion** restricts the region the API calls are made **to**. This is also **global** because it has prefix AWS.
    ![Alt text](images/ReqRegion_Condition.png)
    (We deny anything on EC2, RDS, and DynamoDB if we are in region eu-central-1 or eu-west-1)
    - **ec2:ResourceTag** restricts based on tags. Here prefix is EC2 so it applies only to EC2 instances.
    ![Alt text](images/ResourceTag_Conditions.png)
    (Here we allow start and stop instances, for any instance if the ResourceTag/Project equals DataAnalytics.)  
    (Another tag here is aws:PrincipalTag, this applies to the user tag and not on instances.)  
    (So here user should also be a part of department Data to perform the said actions)  
    - **aws:MultiFactorAuthPresent** to force MFA
    ![Alt text](images/MFA_Condition.png)
    (User can do anything on EC2, but only stop and terminate instances if MFA is present. So this is **deny on the false.**)

- **IAM for S3**
    ![Alt text](images/IAM_S3.png)
    (Above is a bucket policy with 2 statements)  
    (First is the ListBucket which is a **bucket level permission**, so we need to specify the arn of the bucket itself arn:aws:s3:::test)  
    (Second applies to objects within a bucket **object level permissions**, so the arn will have /* to represent all the objects within your buckets. arn:aws:s3:::test/*)  

- **Resource Policies & aws:PrincipalOrgID**
    - aws:PrincipalOrgID can be used in any resource policies to restrict access to accounts that are member of an AWS Organization.
    ![Alt text](images/PrincipalOrg_Condition.png)
    (Note the PrincipalOrgID in the policy)

# IAM Roles Vs Resource Based Policies

- **Cross Account**:
    - attaching a resource based policy to a resource example S3 bucket policy.
    - OR using a role as a proxy

    ![Alt text](images/IAM_Vs_ResourcePolicy.png)

    - **When you assume a role (user, application or service), you give up your original permissions and take permissions assigned to the role.**
    - When using resource based policy, the principal doesnt have to give up his permissions.
    - Ex: User in account A needs to scan a DynamoDB table in Account A and dump it in an S3 bucket in Account B. In this case resource based policy is good because we can do both scan the table and write to S3.
    - Resource based policy is supported by: S3, SNS, SQS, Lambda etc.

- **EventBridge - Security**
    - When an EventBridge rule runs, the rule needs permissions on the target. There are 2 kinds of target:
        - Resource-based policy: Lambda, SNS, SQS, CloudWatch Logs, API Gateway
        - IAM role: Kinesis stream, Systems Manager Run command, ECS task ..

    ![Alt text](images/EventBridge_Security.png)  

IAM condition key you can use only to allow API calls to a specified AWS region? - **aws:RequestedRegion**
