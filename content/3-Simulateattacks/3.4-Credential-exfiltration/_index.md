---
title : "Credential Exfiltration"
date :  2025-06-11
weight : 2 
chapter : false
pre : " <b> 3.4. </b> "
---
### Overview Architecture

**Retrieve temporary IAM security credentials with Systems Manager**

![CredentialExfiltration](/images/3.attack/3.4/CredentialExfiltration-1.png)

1. Remote server accesses compromised EC2 instance and steals IAM role credentials via Metadata.
2. This server sets up an AWS CLI Profile to make API calls to an AWS account.
3. **Security Hub** detects anomalies
4. **GuardDuty** generates relevant Findings and simultaneously sends them to **GuardDuty console** and **EventBridge Events**.
5. **EventBridge Event Rule** triggers **SNS Topic** and **Lambda Function**.
6. **SNS Topic** proceeds to send E-mail notification with Finding details.
7. **Lambda Function** then assigns a new policy to revoke all active Sessions.

#### Investigation process
1. Access [Security Hub](https://ap-southeast-1.console.aws.amazon.com/securityhub/home?region=ap-southeast-1#/), we see **High** Findings detected by GuardDuty
![CredentialExfiltration](/images/3.attack/3.4/hub.png)
2. Access [GuardDuty Console](https://ap-southeast-1.console.aws.amazon.com/guardduty/home?region=ap-southeast-1#/)
3. We will see Findings with the following format **UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration**.
![CredentialExfiltration](/images/3.attack/3.4/8.png)
4. If there is no Finding, press the Refresh button and wait.
5. From Finding **UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration**, c we can easily retrieve some of the following information:
- High Severity
- Notice that someone is trying to use IAM role credential outside EC2 instance
> Each GuardDuty Finding will be assigned a specific severity level - Low/Medium/High. These levels are defined by AWS and are used to classify and identify.

**Check EventBridge Event Rule**

1. Check EventBridge Event Rule [EventBridge Event Rule](https://ap-southeast-1.console.aws.amazon.com/events/home?region=ap-southeast-1#/) at ap-southeast-1.
2. In the left navigation bar, under **Events** , select **Rules**. You'll see three rules already set up (by CloudFormation Template), starting with a prefix like **GuardDuty-Event..**
![CredentialExfiltration](/images/3.attack/3.4/10.png)
3. Proceed to select the rule named **GuardDuty-Event-IAMUser-InstanceCredentialExfiltration**.
![CredentialExfiltration](/images/3.attack/3.4/11.png)
4. In the Event Pattern section, we can easily see the data source that this Event will record and activate the Targets when any event occurs.

> You can create an EventBridge Event Rule to record events of a specific Finding type or any Finding type.

**Test the Remediation process with Lambda Function**

Viet has set up a Remediation process to respond to this threat automatically through a Lambda function. We can examine the code to understand more about this process.

1. Access [Lambda Console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/begin) to ap-southeast-1.
2.In the left navigation bar, select Functions and search for **GuardDuty-Example-Remediation-InstanceCredentialExfiltration**.
![CredentialExfiltration](/images/3.attack/3.4/9.png)
> Basically, this Lambda function will retrieve information about IAM Role from Finding and proceed to add IAM Policy.

**Verify the Remediation process**

To verify whether Finding InstanceCredentialExfiltration has been completely resolved, we will proceed with the following steps.

**Verify via AWS CLI**
Execute the following command:

        aws dynamodb list-tables --profile iamhacker

We will get the return result will be **AccessDeniedException** for the executed statement.
