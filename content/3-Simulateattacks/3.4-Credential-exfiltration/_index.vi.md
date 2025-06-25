---
title : "Credential Exfiltration"
date :  2025-06-11
weight : 2 
chapter : false
pre : " <b> 3.4. </b> "
---
### Kiến trúc tổng quan

**Truy xuất IAM security credential tạm thời với Systems Manager**

![CredentialExfiltration](/images/3.attack/3.4/CredentialExfiltration.png)

1. Máy chủ từ xa truy cập đến EC2 compromised instance và đánh cắp IAM role credential thông qua dữ liệu Metadata.
2. Máy chủ này thiết lập AWS CLI Profile để tiến hành gọi API đến tài khoản AWS.
3. **Security Hub** phát hiện các bất thường
4. **GuardDuty** sinh ra những Findings liên quan và đồng thời gửi tới **GuardDuty console** và **EventBridge Events**.
5. **EventBridge Event Rule** kích hoạt **SNS Topic** và **Lambda Function**.
6. **SNS Topic** tiến hành gửi thông báo E-mail với chi tiết Finding.
7. **Lambda Function** tiến hành gán một chính sách mới nhằm thu hồi mọi Sessions đang hoạt động.

#### Quá trình điều tra
1. Truy cập [Security Hub](https://ap-southeast-1.console.aws.amazon.com/securityhub/home?region=ap-southeast-1#/), chúng ta thấy Findings ở mức **High** được phát hiện bởi GuardDuty. 
![CredentialExfiltration](/images/3.attack/3.4/hub.png)
2. Truy cập [GuardDuty Console](https://ap-southeast-1.console.aws.amazon.com/guardduty/home?region=ap-southeast-1#/)
3. Chúng ta sẽ thấy được Findings với định dạng như sau **UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration**.
![CredentialExfiltration](/images/3.attack/3.4/8.png)
4. Nếu không có bất kỳ Finding nào, tiến hành nhấn nút Refresh và đợi.
5. Từ Finding **UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration**, chúng ta có thể dễ dàng truy xuất một số thông tin sau:
- High Severity
- Thông báo rằng có người cố ý sử dụng IAM role credential ở ngoài EC2 instance
> Mỗi GuardDuty Finding sẽ được gán một mức độ nghiêm trọng cụ thể - Low/Medium/High. Các mức độ này được định nghĩa bởi AWS, chúng được dùng để phân loại và xác định

**Kiểm tra EventBridge Event Rule**

1. Truy cập vào [EventBridge Event Rule](https://ap-southeast-1.console.aws.amazon.com/events/home?region=ap-southeast-1#/) ở ap-southeast-1.
2. Ở thanh điều hướng bên tay trái, dưới **Events**, chọn Rules. Bạn sẽ thấy có 3 quy tắc đã được thiết lập (bởi CloudFormation Template), bắt đầu với tiền tố có dạng sau **GuardDuty-Event..**
![CredentialExfiltration](/images/3.attack/3.4/10.png)
3. Tiến hành chọn quy tắc có tên là **GuardDuty-Event-IAMUser-InstanceCredentialExfiltration**.
![CredentialExfiltration](/images/3.attack/3.4/11.png)
4. Ở mục Event Pattern, chúng ta dễ dàng thấy được nguồn dữ liệu mà Event này sẽ ghi nhận và tiến hành kích hoạt các Target khi có bất kỳ sự kiện nào.

> Bạn có thể tạo EventBridge Event Rule nhằm ghi nhận sự kiện của một loại Finding cụ thể hay bất kỳ loại Finding nào.

**Kiểm tra quá trình Remediation với Lambda Function**

Việt đã thiết lập quá trình Remediation nhằm phản ứng tự động với mối nguy hại này thông qua Lambda function. Chúng ta có thể kiểm tra đoạn mã được lập trình để hiểu thêm về quá trình này.

1. Truy cập vào [Lambda Console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/begin) ở ap-southeast-1.
2. Ở thanh điều hướng bên tay trái, chọn Functions và tìm kiếm **GuardDuty-Example-Remediation-InstanceCredentialExfiltration**.
![CredentialExfiltration](/images/3.attack/3.4/9.png)
> Về cơ bản, Lambda function này sẽ truy xuất thông tin về IAM Role từ Finding và tiến hành thêm IAM Policy.

**Kiểm chứng quá trình Remediation**

Để tiến hành kiểm chứng liệu Finding InstanceCredentialExfiltration đã được giải quyết triệt để, chúng ta sẽ lần lượt tiến hành các bước sau.

**Kiểm chứng thông qua AWS CLI**
Tiến hành thực thi câu lệnh sau:

        aws dynamodb list-tables --profile iamhacker

Chúng ta sẽ nhận được kết quả trả về sẽ là **AccessDeniedException** cho câu lệnh thực thi.
