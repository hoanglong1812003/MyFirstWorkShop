---
title : "Compromised EC2 Instance"
date :  2025-06-11
weight : 1 
chapter : false
pre : " <b> 3.1. </b> "
---

### Situation 1
One Monday morning, Long arrived at the office early, prepared a cup of coffee, sat down and opened his laptop, started his daily tasks such as sending E-mails, writing plans. Suddenly, Long started receiving a series of E-mails with content related to the latest discovery of threats, which Long had never seen before, but Long immediately started to learn and investigate. The good news is that his colleague, Viet, had set up automatic responses for those Findings so they were resolved immediately.

*So what are the automatic responses that Viet has set up?*

The first E-mail you Long received with the news that your EC2 instance may have been compromised is as follows:

![Compromised EC2 Instance](/images/3.attack/3.1/1.png)

The second E-mail you received shortly after with the situation being resolved is as follows:

![Compromised EC2 Instance](/images/3.attack/3.1/2.png)

### Overview Architecture

![Compromised EC2 Instance](/images/3.attack/3.1/CompromisedEC2-1.png) 

1. A **compromised EC2** instance sends pings to the EIP address of a malicious EC2 instance. That EIP address has been added to the **Custom Threat List**.
2. **GuardDuty** discovered an unusual security vulnerability and sent it to **Security Hub** for synthesis.
3. **GuardDuty** monitors **VPC Flow Logs** (including CloudTrail and DNS Logs) and analyzes the situation based on Machine Learning, Custom Threat Lists, and more.
4. **GuardDuty** generates a Finding and displays it on the GuardDuty Console and sends this event to **EventBridge Events**.
5. Based on this event, **EventBridge Event Rule** reacts and triggers the corresponding **SNS Topic** and **Lambda Function** simultaneously.
6. **SNS Topic** will send you an E-mail with Finding details.
8. The **Lambda Function** will isolate the EC2 compromised instance.

> When Viet set up the E-mail notification for this event, he only added certain information about the Finding and configured the Lambda Function to automatically isolate the EC2 compromised instance. Even though the Finding has been resolved, you still decide to dig deeper into Viet's current settings and configuration.

### Investigation process
While you can see these Findings from the GuardDuty Console, most customers want to aggregate them from AWS Regions and AWS Accounts to a centralized security information management ( **SIEM** ) for analysis and remediation. The most common approach is to configure GuardDuty under an Admin/Member model and use a combination of EventBridge Event Rules and Lambda Functions to push these Findings to **a SIEM** or a **Centralized Logging Framework** . There are also a number of solutions from AWS partners that make it easier for customers to consolidate and push data.

1. Access to [Security Hub](https://ap-southeast-1.console.aws.amazon.com/securityhub/home?region=ap-southeast-1#/) at ap-southeast-1.

2. We will see security alerts in GuardDuty with Finding: **UnauthorizedAccess:EC2/MaliciousIPCaller.Custom**.

![Compromised EC2 Instance](/images/3.attack/3.1/hub.png)

3. Proceed to access GuardDuty Console at ap-southeast-1.

4. We will see a Finding with the following format **UnauthorizedAccess:EC2/MaliciousIPCaller.Custom**.

![Compromised EC2 Instance](/images/3.attack/3.1/4.png)

5. If there is no Finding, please press refresh and wait.

{{% notice note %}}
Users can access Findings in the GuardDuty console for 90 days.
{{% /notice %}}

6. In your environment, this type of Finding indicates that an EC2 instance is communicating with an IP address (that has been added to the Threat Lists).
7. Select Lists in the navigation bar (left side) to see the Threat List that Viet added earlier **Example-Threat-List**.

![Compromised EC2 Instance](/images/3.attack/3.1/5.png)

> GuardDuty uses Threat Intelligence systems provided by AWS Security and third parties such as ProofPoint and CrowdStike. You can extend GuardDuty’s visibility by configuring your own Trusted IP Lists and Threat Lists. If you have set up GuardDuty in an Admin/Member structure, you can manage these lists from the GuardDuty Admin account and have Members inherit them. By default, Members do not have the ability to edit these lists.

{{% notice note %}}
In this simulation scenario, the EC2 compromised instance only accesses the EIP of another EC2 instance in the same VPC to internalize the simulation and data processing that only occurs in your environment. CloudFormation Templates automatically create Threat Lists and assign this EIP address to them.
{{% /notice %}}

**Check EventBridge Event Rule**

Viet uses EventBridge Event Rules to send you notifications about Findings along with the steps of the Remediations process. Let's take a closer look to understand what Viet has set up and how this process works?

1. Access to [EventBridge](https://ap-southeast-1.console.aws.amazon.com/events/home?region=ap-southeast-1#/) Console at ap-southeast-1.

![Compromised EC2 Instance](/images/3.attack/3.1/6.png)

2. In the left navigation bar, under **Events** , select **Rules**. You'll see three rules already set up (by CloudFormation Template), starting with a prefix like **GuardDuty-Event**.

3. Proceed to select the rule named **GuardDuty-Event-EC2-MaliciousIPCaller**.

![Compromised EC2 Instance](/images/3.attack/3.1/7.png)

4. You will easily notice that there are 2 targets in the **Targets** area.
- **Lambda Function**
- **SNS Topic**: Proceed to send E-mail notifications to you based on the data provided by **EventBridge Event Rule**. Instead of using the entire JSON data, by using **Input Transformer** , Viet has customized the notification content.

![Compromised EC2 Instance](/images/3.attack/3.1/8.png)

**Testing the Remediation process based on Lambda Function**

The Lambda Function is the key that holds the logic to execute the Remediations steps for the Findings. Viet has set up a Lambda Function to remove and replace the Security Group of the EC2 compromised instance with a Security Group **that does not contain any Ingress/Egress rules**. This will help **isolate the EC2 compromised instance from the current network**.

*To perform the Remediation process check:*
1. From the GuardDuty-Event-EC2-MaliciousIPCaller rule , in the Targets area , in the Type section is Lambda Function, we search for the corresponding Resource Name.
2. In the Lambda Function console, search for the Resource Name from the previous step.

3. We can consider some items
- Configuration
    + In the Designer bar, we can easily see the relationship with EventBridge Event Rule.
    + In the Function code section, the coding logic will be executed here.
- Permissions.
- Monitoring.

![Compromised EC2 Instance](/images/3.attack/3.1/9.png)

![Compromised EC2 Instance](/images/3.attack/3.1/10.png)

**Confirming the Remediation Process Was Successful**

To ensure the success of the Remediation process, we need to check whether the EC2 instance has been isolated or not. At this point, you have received an E-mail with some important information.
1. Access the EC2 console at ap-southeast-1 at [here](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Overview:).

![Compromised EC2 Instance](/images/3.attack/3.1/11.png)

2. Select **Instances**, cwe will see 3 EC2 instances with prefix starting with the following format *GuardDuty-Example*.
3. Based on **instance ID**  from GuardDuty Finding or E-mail notification, we select the corresponding EC2 instance: *GuardDuty-Example: Compromised Instance: Scenario 1*.

![Compromised EC2 Instance](/images/3.attack/3.1/12.png)

4. Once the Remediation process is complete, we will check the **Security Group** of this EC2 compromised instance, which will have a similar name format after **ForensicSecurityGroup**.
5. **ForensicSecurityGroup** will not have any Ingress/Egress rules containing IP addresses in **Example-Threat-List**.

![Compromised EC2 Instance](/images/3.attack/3.1/13.png)



