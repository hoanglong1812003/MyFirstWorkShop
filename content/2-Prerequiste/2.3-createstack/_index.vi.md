---
title : "Tạo Stack"
date :  2025-06-11
weight : 3 
chapter : false
pre : " <b> 2.3 </b> "
---

### Tạo Stack

Trong bước này chúng ta sẽ tiến hành tạo Stack để theo dõi và ngăn chặn các cuộc tấn công thông qua ***Findings*** trên GuardDuty.

1. Đăng nhập vào AWS Console.
2. Truy cập vào [Giao diện quản trị dịch vụ CloudFormation](https://ap-southeast-1.console.aws.amazon.com.cloudformation/home?region=ap-southeast-1).
3. Tiến hành tạo Stack mới bằng việc ấn nút **Create stack**.

![CloudFormation](/images/2.prerequisite/2.3/1.png)

4. Ở trang Specify template, click vào Upload a template file.

Tải file template tại [đây](https://github.com/AWS-First-Cloud-Journey/GuardDuty-Hands-On/archive/refs/heads/main.zip).

![CloudFormation](/images/2.prerequisite/2.3/2.png)

5. Ở trang Paraments, chúng ta sẽ nhập một số thông tin bắt buộc sau:
+ **Email Address**: Địa chỉ email của bạn. Click **Next**.

![CloudFormation](/images/2.prerequisite/2.3/3.png)

6. Ở trang Specify Stack Details, ấn **Next**.
7. Ở trang Configure stack options, ấn **Next**.
8. Ở trang Capabilities, tiến hành chấp nhận (Acknowledge) cho phép Template tạo các IAM role, cuối cùng chọn nút **Create Stack**.

![CloudFormation](/images/2.prerequisite/2.3/4.png)

9. Kiểm tra lại các thông tin, ấn **Submit**.

![CloudFormation](/images/2.prerequisite/2.3/5.png)

{{% notice note %}}
Quá trình trên sẽ diễn ra trong vòng 5-10 phút cho tới khi chúng ta thấy được trạng thái của Stack là CREATE_COMPLETE. Sau đó, chúng ta sẽ nhận được một thông báo qua Email với chủ đề tương tự AWS Notification - Subscription Confirmation.
{{% /notice %}}

![CloudFormation](/images/2.prerequisite/2.3/6.png)

### Các tài nguyên được chuẩn bị
1. Dịch vụ EC2:
- 2 instances với tên gọi là Compromised Instance.
- 1 instance với tên gọi là Malicious Instance.
2. Dịch vụ IAM:
- 1 IAM Role dành cho EC2 instance với quyền hạn truy cập đến SSM Parameter Store và DynamoDB.
3. Dịch vụ SNS:
-  1 SNS Topic gửi thông báo qua E-mail.
4. Dịch vụ EventBridge:
- 3 EventBridge Events Rules cho việc kích hoạt các thông báo và quá trình khắc phục.
5. Dịch vụ Lambda:
- 2 Lambda Functions tiến hành khắc phục các lỗ hổng.
6. Dịch vụ SSM:
- 1 SSM Parameter Store dùng để chứa mật khẩu cho môi trường TESTING.