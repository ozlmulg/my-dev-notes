# AWS Introduction

## What is AWS and Why Should You Specialize?

Amazon Web Services (AWS) is the world’s most comprehensive and widely adopted cloud platform, offering over 200 fully
featured services.  
Becoming proficient in AWS allows developers, DevOps engineers, and cloud architects to build scalable, reliable, and
secure systems.

---

## Prerequisites and Tools

To get started with AWS, you will need:

- Basic knowledge of **Linux and Windows systems**.
- Understanding of **networking fundamentals**.
- Familiarity with **programming or scripting languages**.
- An **AWS Free Tier account** for hands-on practice.

Recommended tools:

- AWS CLI
- AWS SDKs
- Infrastructure as Code (Terraform, AWS CDK, CloudFormation)

---

## Virtualization

Virtualization allows multiple operating systems to run on a single physical server.  
This concept underpins cloud computing by enabling efficient resource utilization.

---

## IaaS, PaaS, SaaS

- **Infrastructure as a Service (IaaS):** Provides virtualized computing resources (e.g., EC2).
- **Platform as a Service (PaaS):** Provides environments for application deployment (e.g., Elastic Beanstalk).
- **Software as a Service (SaaS):** Fully managed applications accessed over the internet (e.g., Gmail, Salesforce).

---

## Software Development Lifecycle (SDLC)

AWS supports every stage of the SDLC with services for building, testing, deploying, and monitoring applications.

---

## Software Deployment Process

Cloud platforms like AWS enable **continuous delivery and integration**, reducing deployment times from weeks to
minutes.

---

## DevOps

DevOps is a culture and set of practices that bridge development and operations.  
AWS provides many DevOps services such as:

- AWS CodePipeline
- AWS CodeBuild
- AWS CodeDeploy
- AWS CloudWatch

---

## Microservices

Microservices architecture breaks applications into independent services that can be deployed and scaled separately.  
AWS services supporting microservices include ECS, EKS, and API Gateway.

---

## APIs

An API (Application Programming Interface) allows communication between software components.  
In AWS, APIs are the primary method of interacting with cloud resources.

---

## Containers

Containers package code, runtime, and dependencies into isolated environments.  
AWS services for containers include ECS, EKS, and Fargate.

---

## AWS History

- 2002: AWS launched as a set of simple web services.
- 2006: AWS introduced EC2 and S3, marking the beginning of modern cloud computing.
- Today: AWS operates globally, serving millions of customers including startups, enterprises, and governments.

---

## AWS Global Infrastructure

One of AWS’s biggest strengths is its **global presence**.  
AWS organizes its infrastructure into multiple layers:

### Regions

- A **Region** is a physical geographical area (e.g., `us-east-1` in Virginia, `eu-west-1` in Ireland, `ap-southeast-1`
  in Singapore).
- Each Region consists of multiple **Availability Zones (AZs)**.
- Customers choose Regions based on:
    - **Proximity to end-users** (lower latency).
    - **Regulatory compliance** (data residency laws).
    - **Service availability** (not all services are in every Region).

**Examples:**

- **North Virginia (us-east-1)** – The largest AWS region with most new services launched first.
- **Frankfurt (eu-central-1)** – Chosen for European workloads with strict data protection.

### Availability Zones (AZs)

- An **Availability Zone** is one or more isolated data centers within a Region.
- Each AZ has independent **power, cooling, and networking**.
- AZs are connected by **low-latency, high-bandwidth fiber**.
- Deploying across multiple AZs enables **high availability and fault tolerance**.

**Example:**

- In `us-east-1` (Virginia), there are **6 AZs**.
- A web app could run across **2 AZs**, ensuring uptime if one AZ fails.

### Data Centers

- **Data Centers** are the physical facilities inside AZs.
- Each contains thousands of servers, storage, and networking equipment.
- AWS does not reveal exact locations for security reasons.

### Edge Locations

- **Edge Locations** are used by **CloudFront CDN** and **Route 53 DNS**.
- They cache content closer to users to reduce latency.
- AWS has **400+ Edge Locations** globally.

**Example:**

- A user in Istanbul loading a website hosted in Frankfurt may receive static content from the nearest Edge Location in
  Turkey.

**Summary:**

- **Region → AZ → Data Center** form the backbone for running applications.
- **Edge Locations** accelerate content delivery and DNS.
- Best practice: Deploy workloads across **multiple AZs** within a Region.

---

## Creating an AWS Account

To start using AWS, you need an AWS account.

**Steps:**

1. Go to [https://aws.amazon.com](https://aws.amazon.com) and click **Create an AWS Account**.
2. Provide:
    - **Email address**
    - **Password**
    - **AWS account name**
3. Enter **billing information** (credit/debit card is required).
4. Provide **phone number verification** (SMS/voice).
5. Choose a **Support Plan** (Basic is free).
6. After verification, log in to the **AWS Management Console**.

💡 **Tip:** The **Free Tier** offers 12 months of free usage for selected services (e.g., EC2 `t2.micro`, S3, RDS).

---

## AWS Management Console

The **AWS Management Console** is a web-based interface for accessing and managing AWS resources.

**URL:** [https://console.aws.amazon.com](https://console.aws.amazon.com)

**Key Features:**

- **Service Search** – Quickly find AWS services.
- **Resource Groups & Tags** – Organize resources by project/environment.
- **Dashboards** – Manage EC2, S3, Lambda, etc.
- **Billing Dashboard** – Monitor costs and set budgets.
- **IAM Integration** – Securely manage users, groups, and roles.

**Example:**

To launch a virtual machine (EC2):

1. Open **EC2 Dashboard**.
2. Click **Launch Instance**.
3. Choose AMI, instance type, configure settings.
4. Launch and connect via SSH.
