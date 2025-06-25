---
title : "Create Stack"
date :  2025-06-11
weight : 3 
chapter : false
pre : " <b> 2.3 </b> "
---

### Create Stack

In this step we will create a Stack to monitor and prevent attacks through **Findings** on GuardDuty

1. Sign in to AWS Console
2. Access to [CloudFormation Service Administration Interface](https://ap-southeast-1.console.aws.amazon.com.cloudformation/home?region=ap-southeast-1)
3. Proceed to create a new Stack by pressing the **Create stack**
![CloudFormation](/images/2.prerequisite/2.3/1.png)

4. On the Specify template page, click Upload a template file

Download template file at [here](https://github.com/AWS-First-Cloud-Journey/GuardDuty-Hands-On/archive/refs/heads/main.zip)

![CloudFormation](/images/2.prerequisite/2.3/2.png)

5. On the Parameters page, we will enter some required information:
+ **Email Address**: Your email address. Click **Next**
![CloudFormation](/images/2.prerequisite/2.3/3.png)

6. On the Specify Stack Details page, click **Next**.
7. On the Configure stack options page, click **Next**.
8. On the Capabilities page, accept (Acknowledge) to allow the Template to create IAM roles, and finally select the **Create Stack** button.
![CloudFormation](/images/2.prerequisite/2.3/4.png)
9. Check the information again, press **Submit**
![CloudFormation](/images/2.prerequisite/2.3/5.png)
{{% notice note %}}
The above process will take place within 5-10 minutes until we see the status of the Stack as CREATE_COMPLETE. After that, we will receive an Email notification with the same subject as AWS Notification - Subscription Confirmation.
{{% /notice %}}

### Resources prepared
1. EC2 Services:
- 2 instances called Compromised Instance.
- 1 instance called Malicious Instance.
2. IAM Services:
- 1 IAM Role for EC2 instance with access to SSM Parameter Store and DynamoDB.
3. SNS Services:
- 1 SNS Topic sends notifications via E-mail.
4. EventBridge Services:
- 3 EventBridge Events Rules for triggering notifications and remediation.
5. Lambda Service:
- 2 Lambda Functions proceed to fix the vulnerabilities.
6. SSM Services:
- 1 SSM Parameter Store is used to store passwords for the TESTING environment.