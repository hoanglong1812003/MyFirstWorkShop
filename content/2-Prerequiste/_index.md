---
title : "Preparation steps"
date :  2025-06-11
weight : 2 
chapter : false
pre : " <b> 2. </b> "
---

### Overview
In the next steps, we will proceed to enable GuardDuty, Security Hub and create Stack.
### Situation
*The exercise will describe the following situations.*
| **Order** | **Name**                          | **Specification**                                                                                                   | **Solutions**                                                                                                     |
|------------|----------------------------------|--------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| 1          | **Compromised EC2 Instance**    |	Detect and recover attacked EC2 instances.                                                               | Combining **Amazon GuardDuty** , **Amazon EventBridge Event Rules** , and **AWS Lambda**                      |
| 2          | **Compromised IAM credentials**| Identify an individual actively making API calls to a system on AWS.                                             | Fix this threat immediately (*manually*)                                           |
| 3          | **IAM role exfiltration**       | Through a leaked credential, an individual is trying to break in and call an API from an external server.     | Remediation with **AWS Lambda**                                                                           |

### Content
  - [Enable GuardDuty](2.1-enableguardduty/)
  - [Enable Security Hub](2.2-enablesecurityhub/)
  - [Create Stack](2.3-createstack/)


  
