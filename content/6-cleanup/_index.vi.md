+++
title = "Dọn dẹp tài nguyên  "
date = 2025
weight = 4
chapter = false
pre = "<b>4. </b>"
+++

Chúng ta sẽ tiến hành các bước sau để xóa các tài nguyên chúng ta đã tạo trong bài thực hành này.

### Xóa S3 Bucket
1. Truy cập [S3 Bucket](https://ap-southeast-1.console.aws.amazon.com/s3/get-started?region=ap-southeast-1)
2. Empty các S3 Bucket vừa tạo
3. Delete các S3 Bucket.

### Xóa IAM Role
1. Truy cập [IAM Role](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/roles)
2. Tìm kiếm **GuardDuty-Example-EC2-Compromised**
3. Delete

### Vô hiệu hóa Amazon GuardDuty
1. Truy cập [Amazon GuardDuty](https://ap-southeast-1.console.aws.amazon.com/guardduty/home?region=ap-southeast-1#/)
2. Nhấn vào mục **Setting**
3. Chọn **Disable GuardDuty**
4. Ấn **Save**

### Vô hiệu hóa Security Hub
1. Truy cập [Security Hub](https://ap-southeast-1.console.aws.amazon.com/securityhub/home?region=ap-southeast-1#/landing)
2. Ở thanh bên trái, chọn **Setting**
3. Ở Setting chọn General
4. Cuộn xuống, chọn Disable AWS Security Hub CSPM

### Xóa Stack
1. Truy cập [CloudFormation](https://ap-southeast-1.console.aws.amazon.com/cloudformation/home?region=ap-southeast-1#/stacks?filteringText=&filteringStatus=active&viewNested=true)
2. Chọn Stack vừa tạo
3. Delete
> Nếu xóa không được, vui lòng xem lại xóa S3 Bucket và xóa IAM Role