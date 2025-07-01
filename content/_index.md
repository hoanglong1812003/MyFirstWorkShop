---
title : "Detect threats with Amazon Security Hub & Amazon GuardDuty"
date :  2025-06-11
weight : 1 
chapter : false
---
# Detect threats with Amazon Security Hub & Amazon GuardDuty

### Overview

In this lab, you will learn about Amazon GuardDuty combined with AWS Security Hub . Amazon GuardDuty is a service that detects and remediates threats to the system, while AWS Security Hub will provide us with a comprehensive view of priority security alerts and compliance status on AWS accounts. We will analyze, evaluate, and how to alert and fix security issues based on the "Findings" of GuardDuty and Security Hub.

 ### About GuardDuty
 **Data Sources**
 Once enabled in an AWS Region, GuardDuty analyzes all data coming from:
 1. VPC Flow Logs
 2. CloudTrail Logs
 3. DNS Logs
    - DNS logs come from DNS resolvers (owned by AWS) for VPCs and are not directly accessible to users.
    - If the DNS resolver is configured independently by yourself or by a third party, GuardDuty will not be able to retrieve, process and identify threats from this data source.

    ![GuardDuty](/images/1.png) 
    
GuardDuty can access any data sources (mentioned above) even if they are not previously enabled.

{{% notice note %}}
You should enable CloudTrail Logs and VPC Flow Logs at the same time to get the most comprehensive and detailed view when analyzing data.
{{% /notice %}}

GuardDuty is a Regional service, so to be able to monitor data in an AWS Region, you must activate it in that AWS Region.

{{% notice note %}}
You can enable it through the AWS Console or using the APIs. Most users will enable it across all AWS Regions and AWS Accounts simultaneously through the APIs.
{{% /notice %}}

Regardless of how many AWS resources you have (e.g. VPCs or IAM users), GuardDuty will not impact any of them because the processing will be performed internally within the GuardDuty service.

{{% notice note %}}
GuardDuty is a fully managed service provided by AWS.
{{% /notice %}}

GuardDuty's pricing will be based on

- Number of CloudTrail events analyzed.
- Volume of VPC floư logs (in GB)
- DNS log size (in GB)

{{% notice note %}}
Each AWS account will have a 30-day trial period in each AWS Region, which will help GuardDuty easily predict the costs incurred.
{{% /notice %}}

### Findings
GuardDuty will proactively observe and monitor for anomalies originating from:
- 3 data sources (mentioned above)/
- EC2 Instances.
- AWS IAM Resources. 

You can easily access the details of the Findings discovered by GuardDuty in the Findings tab. Each Finding is broken down into information in a format that allows us to easily understand and address security risks.

{{% notice note %}}
Learn more about the full list of GuardDuty Findings series at [here](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_finding-format.html)
{{% /notice %}}

### About AWS Security Hub
AWS Security Hub gives you a comprehensive view of high-priority security alerts and compliance status across your AWS accounts.

You have a lot of powerful security tools at your disposal, from firewalls and endpoint protection to vulnerability scanning and security compliance. But sometimes, this leaves your team flipping back and forth between these tools to deal with hundreds or thousands of security alerts each day. With Security Hub, you now have a single place to aggregate, sort, and prioritize security alerts or findings from multiple AWS services (such as Amazon GuardDuty, Amazon Inspector, and Amazon Macie) or AWS partner solutions.

The risks found are visually summarized on a built-in dashboard with interactive charts and tables. You can also continuously monitor your system using automated compliance checks against AWS best practices and market standards that your business follows.

![SecurityHub](/images/2.jpeg)

### Expense
Typically, it will cost less than $1 per month if your account is only used for testing, training, and not for performing simulated attacks.

**Security Check**
- First 100,000 => $0.0010/check
- 100,001 – 500,000 => $0.0008/check
- 500,001+ => $0.0005/check

**Find attack events**
- First 10,000 times => free
- 10,001+ => $0.00003/time

### Comparing Amazon GuardDuty and AWS Security Hub

| Criteria                  | Amazon GuardDuty                                                                 | AWS Security Hub                                                              |
|---------------------------|----------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| **Main purpose**        | PAutomated threat detection based on behavior and machine learning.               | Aggregate, standardize, and evaluate security alerts from multiple sources.          |
| **Data processing**         | VPC Flow Logs, CloudTrail Events, DNS logs. logs                                       | Alerts from GuardDuty, Inspector, IAM Access Analyzer,…                    |
| **How it works**        | Log analysis, using AI and threat intel to detect anomalies.               | Collect and standardize alerts according to ASFF standards.                               |
| **Event response**      | Send alert to CloudWatch, trigger Lambda                                    | Allows creation of Custom Actions, integration with Lambda, EventBridge                |
| **Warning standard**        | Private internal format.                                                             | AWS Security Finding Format (ASFF).                                           |
| **Advantage**               |  No agent installation required, real-time, automatic detection.                      |  Comprehensive synthesis, integrating multiple sources, supporting Best Practice.     |
| **Disadvantages**            |  No aggregate reporting, may generate false alarms.                         |  Depending on other warning sources, information overload may occur.            |

> It is possible to **combine both** : use GuardDuty to detect threats, then send findings to Security Hub for aggregation, assessment, and response.



### Content

 1. [Introduce](1-introduce/)
 2. [Preparation steps](2-Prerequiste/)
 3. [Simulate attacks](3-Simulateattacks/)
 4. [Clean up resources](4-cleanup/)

