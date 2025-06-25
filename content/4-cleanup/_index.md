+++
title = "Clean up resources"
date = 2025
weight = 4
chapter = false
pre = "<b>4.</b>"
+++

We will follow these steps to delete the resources we created in this lab.

### Delete S3 Bucket
1. Access [S3 Bucket](https://ap-southeast-1.console.aws.amazon.com/s3/get-started?region=ap-southeast-1)
2. Empty the newly created S3 Buckets
3. Delete S3 Buckets.

### Delte IAM Role
1. Access [IAM Role](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/roles)
2. Search **GuardDuty-Example-EC2-Compromised**
3. Delete

### Disable Amazon GuardDuty
1. Access [Amazon GuardDuty](https://ap-southeast-1.console.aws.amazon.com/guardduty/home?region=ap-southeast-1#/)
2. Click on **Setting**
3. Select **Disable GuardDuty**
4. Click **Save**

### Disable Security Hub
1. Access [Security Hub](https://ap-southeast-1.console.aws.amazon.com/securityhub/home?region=ap-southeast-1#/landing)
2. On the left sidebar, select **Setting**.
3. In Settings select General.
4. Scroll down, select Disable AWS Security Hub CSPM.

### Delete Stack
1. Access [CloudFormation](https://ap-southeast-1.console.aws.amazon.com/cloudformation/home?region=ap-southeast-1#/stacks?filteringText=&filteringStatus=active&viewNested=true)
2. Select the newly created Stack
3. Delete
> If deletion fails, please review S3 Bucket deletion and IAM Role deletion.