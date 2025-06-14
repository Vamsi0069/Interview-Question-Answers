#### What is AWS Session Manager?

A service under AWS Systems Manager that allows secure shell-less access to EC2 instances.

* No need for SSH or key pairs
* Logs sessions to CloudWatch or S3
* Ideal for secure, auditable access


#### What is an AMI (Amazon Machine Image)?

An AMI is a template used to launch EC2 instances.
It includes the OS, application server, and application.
To create:
AWS Console → EC2 → Actions → "Create Image"


#### Difference between EBS and EFS

| Feature         | EBS (Elastic Block Store) | EFS (Elastic File System)     |
|  | - | -- |
| Type            | Block storage             | NFS-based shared file storage |
| Instance Access | Single EC2 instance       | Multiple EC2 instances        |
| Use Case        | Databases, boot volumes   | Shared storage, web content   |


####  What is AWS Backup?

A centralized backup service to automate backup of AWS resources:

* Supports RDS, EBS, DynamoDB, EFS, etc.
* Enables scheduled, compliant, and encrypted backups
* Integrates with AWS Organizations for centralized control


####  What is RDS MySQL?

A managed MySQL database hosted on Amazon RDS.

* Automated backups, patching, and failover
* Supports Multi-AZ and read replicas
* No OS-level maintenance required


####  What is AWS Lambda?

A serverless compute service that runs code in response to events.

* No provisioning or managing servers
* Pay only for execution time
* Common triggers: API Gateway, S3 uploads, DynamoDB streams


####  Explain VPC Peering (3-way: A ↔ B ↔ C)?

To enable full communication among 3 VPCs (A, B, C), you need:

* A ↔ B
* B ↔ C
* A ↔ C
  Each VPC peering is bidirectional but not transitive — you must explicitly peer each pair.


####  Difference between  Elastic IP and Public IP in AWS

| Feature     | Elastic IP (EIP)        | Public IP                    |
| -- | -- | - |
| Type        | Static                  | Dynamic (changes on restart) |
| Association | User-managed            | AWS-assigned                 |
| Persistence | Remains unless released | Released when instance stops |

####  How to Monitor EC2 with CloudWatch?

* Enable Detailed Monitoring for 1-minute metrics
* Use CloudWatch Agent to monitor:

  * Memory
  * Disk
  * Custom application logs
* Create alarms for CPU, status checks, etc.


####  What are AWS Step Functions?

A serverless orchestration service that connects AWS services using state machines.

* Automates workflows like data processing, ETL, microservices coordination
* Supports retries, branching, and parallel execution
* Visual workflow editor available

#### What is a NAT Gateway? And what is its use?
A NAT Gateway is a service in AWS that lets servers in a private subnet connect to the internet, but it blocks internet traffic from coming in.

- Why is it used?
To allow private servers (like app servers or databases) to download updates or connect to the internet (e.g., for APIs).

To keep those servers safe by not exposing them directly to the internet.

Example:
You have a private EC2 instance with no public IP.

It needs to install software from the internet.

The traffic goes through the NAT Gateway, which sits in a public subnet, and then out to the internet.

#### Difference between NAT Gateway and Internet Gateway
| Feature              | NAT Gateway                                             | Internet Gateway                                                         |
| -- | -- | - |
| Purpose          | Allows outbound internet access for private subnets | Enables both inbound and outbound internet access for public subnets |
| Used By          | Private instances (no public IP)                            | Public instances (with public IP)                                            |
| Inbound Traffic  |  Not allowed                                               |  Allowed (if security groups allow)                                         |
| Outbound Traffic |  Allowed                                                   |  Allowed                                                                    |
| Placement        | In a public subnet                                      | Attached to the VPC                                                      |
| Managed by AWS   |  Yes                                                       |  Yes                                                                        |
| Needs Public IP? |  No (for private EC2s)                                     |  Yes (for EC2 to be reachable)    
                                          |

#### What is AWS CloudTrail and how do you manage it in your project?

CloudTrail logs all API calls and activities across AWS accounts (who did what, when, from where).
We manage CloudTrail by:

Storing logs in versioned S3 buckets

Enabling multi-region trails

Using CloudWatch Logs integration to trigger alerts (e.g., for unauthorized actions)

Applying S3 bucket policies and encryption with KMS for security

#### Suppose your team member is accessing an AWS service, and you want to revoke their access?

Update or remove their IAM policy or role assignment

Use Service Control Policies (SCPs) in AWS Organizations (if in multi-account setup)

For immediate revocation: disable or delete the IAM user, or rotate their credentials

#### How do you connect from a public subnet to a private subnet?
Use bastion host (jump server) in the public subnet

Connect via SSH:
```ssh -i key.pem ec2-user@<bastion-ip> → ssh into private IP```


#### Why do we use a private subnet in a VPC?

For enhanced security. Resources (like databases) in private subnets are not accessible from the internet.

#### How many AWS accounts are used in your current project?

Usually at least 3: Dev, Staging, Prod. Sometimes separate accounts for Security, Billing, or Sandbox.

#### How can you download files from an S3 bucket within AWS while keeping public access completely blocked?

Use pre-signed URLs, IAM roles, or VPC endpoints to download securely without public access.

#### Have you worked with AWS Transit Gateway in your project?

Transit Gateway helps in centralized VPC-to-VPC and on-prem connectivity across accounts. It simplifies the network mesh.

#### How should developers write code to access AWS services (S3, SQS, SNS, RDS)?

- Use AWS SDKs
- Access via IAM roles with least privilege
- Store secrets in Secrets Manager
- Follow retry/backoff logic
- Use env variables for config
- Log via CloudWatch

#### How to handle developer authentication to AWS?

- Use IAM users/groups with MFA
- Prefer IAM roles with temporary STS credentials
- Use AWS SSO or federation
- Manage secrets via AWS Vault or CLI profiles
- Rotate creds, no hardcoding

#### How to enable communication between different VPCs?

- VPC Peering: Direct, simple, but no transitive routing
- Transit Gateway: Centralized hub, scalable
- PrivateLink: For exposing services, not full VPC access
- VPN: For secure cross-region or hybrid setups

#### What is an Internet Gateway, and where is it placed?

An Internet Gateway (IGW) is a horizontally scaled, redundant, and highly available VPC component that allows communication between instances in your VPC and the internet.
It is attached at the VPC level, not the subnet level.

#### What is VPC Peering, and how do you configure it?

VPC Peering connects two VPCs to route traffic using private IPs.
Steps to configure:
Create a VPC peering connection.
Accept the request from the target VPC.
Add route table entries in both VPCs.
Ensure security groups and NACLs allow traffic.

#### How do you give private access to an S3 bucket?


Remove public access settings.
Attach bucket policy that allows access from specific VPC or IAM roles.
Use VPC endpoint for S3 for private access without using the internet.

#### What is CloudFront?

CloudFront is AWS’s Content Delivery Network (CDN) that caches content at edge locations to reduce latency and speed up delivery.

#### What are bucket policies in AWS S3?
Answer
Bucket policies are JSON-based rules that control access to an entire S3 bucket or objects inside.

Example:
```
{
  "Effect": "Allow",
  "Principal": "*",
  "Action": ["s3:GetObject"],
  "Resource": "arn:aws:s3:::mybucket/*"
}
```
#### If you get a 403 error (Access Denied) in S3, what do you check?


Check bucket policy
Check IAM role/user permissions
Verify if object ACL is private
Ensure correct Region and signed URL, if applicable

#### We check the policy: GetObject, PutObject — why?
These are the S3 actions needed to download (GetObject) or upload (PutObject) files.
Without them, you’ll get 403 errors.

#### 81Q. What is 2/2 check in AWS EC2?
EC2 passes 2 checks:
System status check
Instance status check
Both must be "2/2 checks passed" for healthy status.

#### Why did you use S3 in your project?
Amazon S3 to store and manage static assets such as:

Images

Logs

Backups

Application data (like JSON, CSV, etc.)

The main reasons were:

Durability & Availability: S3 provides 99.999999999% durability, so my data was safe.

Scalability: I didn't have to worry about storage limits.

Cost-effective: I only paid for what I used, and it's cheaper than running EC2 for storage.

Integration: Easily integrates with other AWS services like Lambda, CloudFront, and EC2.

Access control: I could control access using IAM policies or pre-signed URLs.

#### What is a bucket policy in AWS?

JSON document attached to a bucket to define access rules for users, roles, or the public.

#### What is 403 error in S3 object permission?

It means access denied. Possible causes:
Missing s3:GetObject permission
Object is private
Bucket policy restricts access

#### What is AWS Serverless and what are its key benefits?

AWS Serverless refers to a cloud-native development model that allows you to build and run applications without managing servers. Key services include:

•	AWS Lambda – run code in response to events.

•	Amazon API Gateway – expose APIs.

•	Amazon DynamoDB – NoSQL database.

•	AWS Step Functions – orchestrate workflows

#### What is a Key Pair in AWS?
A Key Pair is used for secure SSH access to EC2 instances. It includes a public key (stored in AWS) and a private key (downloaded by the user).
You create it in EC2 Dashboard → Key Pairs → Create Key Pair, and use it when launching an EC2 instance.


#### What is AWS VPC peering?
 VPC peering is a networking connection between two VPCs that enables routing traffic between them using private IPs. Peering works across regions and accounts but does not support transitive peering.

#### What AWS resources have you worked with?
I’ve worked with a wide range of AWS resources, including:

•	EC2 (virtual machines)

•	S3 (object storage)

•	RDS (managed databases)

•	ECS/Fargate (containers)

•	Lambda (serverless compute)

•	IAM (access control)

•	CloudWatch (monitoring/logs)

•	VPC (networking)

•	Route 53 (DNS)

•	Auto Scaling Groups

•	ALB/NLB (load balancers)

•	Elastic Beanstalk, CloudFormation, and Terraform for provisioning

#### What AWS services do you use for scaling instances?

For automatic and manual scaling, I’ve used:

•	Auto Scaling Groups (ASG): Automatically add/remove EC2 instances based on CPU, memory, or custom metrics.

•	Elastic Load Balancer (ELB): Distributes traffic across instances to balance load.

•	CloudWatch Alarms: Used to trigger scaling actions.

•	ECS with Fargate or EC2: Task-based scaling based on request load or queue depth.

#### Difference between ALB and NLB

| Feature               | ALB (Application Load Balancer)                   | NLB (Network Load Balancer)          |
|  | -- | - |
| Layer             | Layer 7 (Application Layer)                           | Layer 4 (Transport Layer)                |
| Protocol          | HTTP, HTTPS, WebSocket                                | TCP, UDP, TLS                            |
| Use Case          | For web apps, APIs (content-based routing)            | For high-performance or low-latency apps |
| Routing           | Smart routing – based on URL, headers, hostname, etc. | Basic routing – based on IP and port     |
| Target Types      | EC2, IP, Lambda                                       | EC2, IP                                  |
| TLS Termination   |  Yes                                                  |  Yes                                     |
| Health Checks     | Application-level (HTTP)                              | Network-level (TCP)                      |
| Static IP Support |  No (uses DNS name)                                   |  Yes (can assign Elastic IPs)            |
| Performance       | Good for HTTP apps                                    | Best for high-throughput, real-time apps |


#### How do you connect to a private subnet?


•	Use a bastion host (jump box) in the public subnet.

•	Or use Session Manager (SSM) if agents are installed.

•	Optionally use a VPN or Direct Connect.

#### What is OAI (Origin Access Identity) in CloudFront?

OAI is used to restrict access to an S3 bucket so only CloudFront can fetch content, preventing direct access via S3 URL.

#### What is SNS and how do you create it?

SNS (Simple Notification Service) is an AWS service used to send notifications via email, SMS, HTTP, or Lambda.
To create it:

Go to AWS SNS in the console.
Click “Create topic” → Choose Standard or FIFO.
Name the topic and click Create.
To add a subscriber, choose the topic → Create subscription → select protocol (e.g., Email) and provide the endpoint.

#### How can you recover a deleted S3 object?
 How do you restore a deleted S3 object?
 If versioning was enabled, I can retrieve the deleted object using a previous version. Without versioning, the object is permanently deleted unless S3 backup (e.g., replication or lifecycle rule to Glacier) is configured.

#### How do you secure a 3-tier architecture?
 How do you secure a 3-tier app (web, app, DB)?
 I use security groups and NACLs to isolate layers:

•	Web tier: Public subnet with limited inbound (HTTP/HTTPS).

•	App tier: Private subnet, allows traffic only from web tier.

•	DB tier: Private subnet, accessible only by app tier.

Enable encryption (TLS, KMS), IAM roles, and monitoring (CloudWatch, GuardDuty).

#### How many VPCs can you create per region?

By default, you can create 5 VPCs per region per AWS account. This limit can be increased by requesting a quota increase from AWS.

#### What is the difference between a private and public subnet?

•	Public Subnet: A subnet that is associated with a route table that has a route to an Internet Gateway (IGW). Resources in this subnet can access the internet.

•	Private Subnet: A subnet that does not have a route to the Internet Gateway. Used for internal resources like databases.

#### What is a Transit Gateway?

An AWS Transit Gateway enables you to connect multiple VPCs and on-premises networks through a central hub, simplifying your network architecture and reducing the number of peering connections.

#### What is a VPC Endpoint?

A VPC Endpoint allows private connection between your VPC and AWS services (like S3, DynamoDB) without using the internet, improving security and performance.

#### How can you restore an RDS snapshot with a custom database name?

You cannot directly rename a database when restoring a snapshot. Instead:
#### 1.	Restore the snapshot.
#### 2.	Create a new DB instance.
#### 3.	Use tools like pg_dump/mysqldump, or AWS DMS to export and import data into a DB with the desired name.

#### How can you connect S3 to an EC2 instance?


•	Attach an IAM Role to EC2 with S3 access permissions (e.g., AmazonS3ReadOnlyAccess).

•	Use AWS CLI or SDK on EC2:

aws s3 ls s3://your-bucket-name

#### What is the difference between Security Group and Network ACL (NACL)?

| Feature          | Security Group                              | Network ACL (NACL)                                    |
| -- | -- |  |
| Level            | Instance-level                                  | Subnet-level                                              |
| Stateful         | Yes (automatically allows return traffic)       | No (return traffic must be explicitly allowed)            |
| Rules            | Allow rules only                                | Allow and Deny rules                                      |
| Applies to       | EC2 Instances (and other resources)             | Entire Subnets                                            |
| Default Behavior | Deny all inbound, allow all outbound by default | Allow all inbound and outbound by default(can be modified)|
| Rule Evaluation  | All rules are evaluated collectively            | Rules are evaluated in order (by rule number)             |
| Use Case         | Fine-grained control over instance traffic      | Broad control over subnet-level traffic                   |

#### What are EC2 instance types?

EC2 instances are categorized based on their hardware capabilities and use cases. Below is a corrected and properly aligned table:

| Instance Series | Type                    | Use Case                               | Examples                |
| - |  |  |  |
| t-series        | Burstable General Purpose   | Low-cost, spiky workloads                  | `t2.micro`, `t3.small`      |
| m-series        | General Purpose             | Balanced compute, memory, and networking   | `m5.large`, `m6g.medium`    |
| c-series        | Compute Optimized           | High-performance compute workloads         | `c5.large`, `c6g.xlarge`    |
| r-series        | Memory Optimized            | In-memory databases, real-time big data    | `r5.large`, `r6g.xlarge`    |
| i-series        | Storage Optimized           | High IOPS, low-latency storage workloads   | `i3.large`, `i4i.xlarge`    |
| g/p-series      | GPU / Accelerated Computing | ML training/inference, AI, video rendering | `g4dn.xlarge`, `p3.2xlarge` |


#### What is nslookup?
- nslookup is a command-line tool to query DNS for domain or IP info, older than dig. Example: nslookup google.com.

#### What is dnslookup?
- No such standard command. Usually means performing DNS lookup using tools like nslookup or dig.

#### Inode of Lambda function?
- AWS Lambda functions don’t have inodes. Inodes are filesystem metadata; Lambda runs serverless, so no direct inode.

#### Difference between  CloudWatch and CloudTrail
| Feature          | CloudWatch                  | CloudTrail                       |
||--|-|
| Purpose          | Monitoring & metrics        | API call logging & auditing      |
| Data Type        | Metrics, logs               | Management API event logs        |
| Use Case         | Performance & alerts        | Security, compliance, auditing   |
| Real-time?       | Yes                         | No                               |
| Retention        | Configurable                | 90 days default + S3 archival    |



#### Difference between IAM Role and IAM Policy?

IAM Role: A set of permissions that can be assumed by AWS services or users. It is used to grant access to AWS resources without using permanent credentials.
IAM Policy: A JSON document that defines permissions. It is attached to users, groups, or roles to control what actions they can perform.



#### What are the types of IAM policies?

AWS Managed Policies: Predefined and maintained by AWS.
Customer Managed Policies: Custom policies created and managed by you.
Inline Policies: Embedded directly into a single IAM user, group, or role.



#### What is MFA and how do you use it?

MFA (Multi-Factor Authentication) adds an extra layer of security by requiring a second form of verification (e.g., a temporary code from a mobile app).
* You enable it through the IAM console for the root account or individual IAM users.



### 4. How do you secure your AWS account?

* Enable MFA on the root and all IAM users.
* Use IAM roles and follow the principle of least privilege.
* Regularly rotate access keys.
* Enable CloudTrail for auditing.
* Use AWS Organizations and Service Control Policies (SCPs) if managing multiple accounts.



### 5. What is Lifecycle policy in S3?

* A lifecycle policy automatically transitions objects to different storage classes (e.g., to Glacier) or deletes them after a defined period.
* Useful for managing cost and data retention.



### 6. What are S3 bucket policies and ACLs?

S3 Bucket Policy: A JSON document that defines access permissions for the entire bucket or specific objects. It supports fine-grained access control.
ACL (Access Control List): A legacy feature used to grant basic access to users at the object or bucket level.



### 7. What is Glacier?

Amazon S3 Glacier is a low-cost cloud storage service for data archiving and long-term backup.
* Retrieval times can range from minutes to hours, depending on the retrieval option.



### 8. Difference between EBS and EFS?

| Feature     | EBS                        | EFS                                 |
| -- | -- | -- |
| Type        | Block storage              | File storage (NFS)                  |
| Access      | One EC2 instance at a time | Multiple EC2 instances concurrently |
| Scalability | Predefined volume size     | Scales automatically                |
| Use Case    | Databases, boot volumes    | Shared file storage                 |



### 9. What is a spot instance vs on-demand vs reserved instance?

| Type      | Description                                         | Cost        |
|  |  | -- |
| Spot      | Uses unused EC2 capacity, can be terminated anytime | Lowest      |
| On-Demand | Pay-per-use with no commitment                      | Flexible    |
| Reserved  | Commit for 1–3 years for discounted pricing         | Cost-saving |



### 10. Difference between RDS and DynamoDB?

| Feature    | RDS                               | DynamoDB                         |
| - |  | -- |
| Type       | Relational (SQL) database         | NoSQL (Key-Value/Document)       |
| Scaling    | Vertical scaling                  | Horizontal auto-scaling          |
| Use Case   | Structured data, complex queries  | Fast access to unstructured data |
| Management | Managed service, but needs tuning | Fully managed by AWS             |



### 11. What is Auto Scaling and how does it work?

* Auto Scaling automatically adjusts the number of EC2 instances in response to traffic or load.
* It uses scaling policies and CloudWatch alarms to launch or terminate instances as needed.



### 12. What is a Target Group?

* A target group is a collection of resources (such as EC2 instances or IPs) that a load balancer routes traffic to.
* Used with ALB and NLB to manage traffic and apply health checks.



### 13. How do health checks work in ALB?

* ALB sends health check requests (e.g., HTTP GET) to each target at a specific path and port.
* If a target fails health checks repeatedly, it is marked unhealthy and removed from the routing pool until it recovers.



### 14. Difference between EBS and S3?

| Feature    | EBS                   | S3                           |
| - |  | - |
| Type       | Block storage         | Object storage               |
| Access     | Mount as disk to EC2  | Access via API or console    |
| Durability | 99.999%               | 99.999999999%                |
| Use Case   | Databases, OS volumes | Backups, media, static files |


#### What is a StorageClass in Kubernetes?

A StorageClass defines how dynamic storage volumes are provisioned.
It abstracts the underlying storage type, allowing Kubernetes to automatically create volumes.
Example (AWS): `gp2`, `gp3`, `io1` provision EBS volumes with specific performance characteristics.


### Where is the Terraform state stored?

Terraform stores its state file (`terraform.tfstate`) to track infrastructure changes.

* Locally: In the working directory by default.
* Remotely: For teams and safety, store in Amazon S3 with DynamoDB for state locking and consistency.
