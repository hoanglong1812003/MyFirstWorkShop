---
title : "Compromised IAM credentials"
date :  2025-06-11
weight : 2 
chapter : false
pre : " <b> 3.2. </b> "
---
### Tình huống 2: Compromised IAM credentials
Bạn Long đã hoàn thành tình huống giả lập tấn công đầu tiên và quay trở lại với tách cà phê của mình. Tuy nhiên, bạn Long lại tiếp tục nhận thêm những thông báo mới về những Findings liên quan tới các dịch vụ AWS IAM. Nội dung thông báo đầu tiên chỉ ra rằng, bằng cách sử dụng **IAM credentials**, một số **API calls** đã được thực hiện từ địa chỉ IP đã được thêm vào **Threat List** (ở bài trước).

{{% notice note %}}
Không có bất kỳ IAM credentials cá nhân nào đã từng bị phơi bày hay lộ ra dưới bất kỳ hình thức nào.
{{% /notice %}}
### Kiến trúc tổng quan

![Compromised IAM credentials](/images/3.attack/3.2/CompromisedIAMcredentials-1.png)

1. **EC2-malicious** instance này tiến hành thực hiện các **API calls**, EIP của instance này đã được thêm vào **Threat List**. Nội dung các API calls đã được lưu lại trong nhật ký của CloudTrail.
2. **Security Hub** phát hiện các hành động đáng ngờ được phát hiện bởi GuardDuty
3. **GuardDuty** quan sát nhật ký của **CloudTrail Logs** cùng với **VPC Flow Logs** và **DNS Logs**, qua đó đánh giá tình hình dựa trên một số cơ sở nhất định.
3. GuardDuty tạo ra những Findings tương ứng và đồng thời gửi chi tiết đến **GuardDuty Console** và **EventBridge Events**.
4. **EventBridge Event Rule** tiến hành kích hoạt **SNS Topic**.
5. **SNS Topic** tiến hành gửi thông báo E-mail cùng với thông tin liên quan.

### Quá trình điều tra
Để tiến hành xem xét các Findings:
1. Truy cập Security Hub, chúng ta phát hiện ra lỗ hổng bảo mật được phát hiện bởi GuardDuty

![Compromised IAM credentials](/images/3.attack/3.2/hub.png)

2. Truy cập vào GuardDuty Console ở **ap-southeast-1** tại [đây](https://ap-southeast-1.console.aws.amazon.com/guardduty/home?region=ap-southeast-1#/).
3. Chúng ta sẽ thấy được các Findings với định dạng như sau.
- Recon:IAMUser.
- UnauthorizedAccess:IAMUser.

![Compromised IAM credentials](/images/3.attack/3.2/1.png)

4. Nếu không có bất kỳ Finding nào, tiến hành nhấn nút Refresh và đợi.
5. Từ Finding **Recon:IAMUser/MaliciousIPCaller.Custom**, chúng ta có thể dễ dàng truy xuất một số thông tin sau:
- Chuyện gì đã xảy ra?
- Tài nguyên AWS nào bị ảnh hưởng?
- Sự kiện này xảy ra khi nào?
6. Ở dưới phần **Resource Affected**, bạn sẽ kiếm được User Name mà liên quan đến Finding này. 

![Compromised IAM credentials](/images/3.attack/3.2/2.png)

Finding này chỉ ra rằng **IAM credential** của **User Name** trên đã có thể bị phơi bày bởi những **API calls** xuất phát từ địa chỉ IP mà trước đó đã được thêm vào **Threat List**.

> Vậy những hành động nào đã được IAM User này thực hiện?

Ở mục **Action**, chúng ta thấy được hành động **DescribeParameters** đã được thực hiện.

![Compromised IAM credentials](/images/3.attack/3.2/3.png)

> Làm thế nào để chúng ta có thể thấy được hết các hành động còn lại, được thực hiện bởi IAM User này, trong vòng 1 tiếng trước hay 1 ngày truớc?

GuardDuty có khả năng phân tích một lượng lớn dữ liệu nhằm xác định chính xác mối nguy hiểm có mặt trong môi trường của bạn. Tuy nhiên, trong quá trình điều tra và các bước thực hiện Remediation, chúng ta cũng cần kết hợp nhiều nguồn dữ liệu khác nhau để có một cái nhìn tương quan nhất có thể.

Trong trường hợp này, nhà phân tích có thể sử dụng các thông tin chi tiết có thể tìm thấy trong nhật ký hành vi người dùng thông qua **CloudTrail**.

![Compromised IAM credentials](/images/3.attack/3.2/4.png)

{{% notice note %}}
Những Findings về IAM được sinh ra bởi EC2 malicious instance đã thực hiện những API calls và EIP của instance này nằm trong Custom Threat List.
{{% /notice %}}

**Kiểm tra EventBridge Event Rule**
1. Truy cập vào **EventBridge Event Rule** Console ở ap-southeast-1.
2. Ở thanh điều hướng bên tay trái, dưới **Events**, chọn **Rules**. Bạn sẽ thấy có 3 quy tắc đã được thiết lập (bởi CloudFormation Template), bắt đầu với tiền tố có dạng sau *GuardDuty-Event..*
3. Tiến hành chọn quy tắc có tên là **GuardDuty-Event-IAMUser-MaliciousIPCaller**
4. Bạn sẽ dễ dàng nhận thấy chỉ có 1 mục tiêu tại vùng **Targets** là **SNS Topic**.

![Compromised IAM credentials](/images/3.attack/3.2/5.png)

Hoá ra, Việt chưa từng thiết lập Lambda Function để thực hiện quá trình Remediation bởi đội ngũ Security đã đưa ra quyết định rằng họ sẽ tiến hành một cách thủ công đối với Finding này.

> Sự kết hợp giữa GuardDuty, Security Hub và EventBridge Events mang lại sự uyển chuyển giúp cho chúng ta dễ dàng tạo ra một quy trình Remediation tự động hoá. Lambda function hay giải pháp đến từ phía đối tác của AWS là những lựa chọn hàng đầu.

Đối với một số Finding nhất định, chúng ta có thể sẽ chỉ cấu hình thông báo và giải quyết vấn đề một cách thủ công, thay vì tự động hoá. Bởi khi thiết kế quá trình tự động hoá, chúng ta sẽ phải vô cùng lưu ý và đánh giá kết quả mà quá trình Remediation mang lại, bao gồm cả ưu điểm và nhược điểm.

> Ngoài ra, chúng ta có thể thiết lập Target đối một số tài nguyên AWS khác như SSM Run Commands hay Step Function State Machine.

**Giải quyết tình hình**
Bởi Việt chưa từng thiết lập quá trình Remediation đối với Finding này nên bạn Long cần phải thực hiện một cách thủ công. Trong khi đội ngũ Security đang tiến hành phân tích những hành vi của IAM user này để xác định rõ hơn phạm vi lỗ hổng, bạn Long cần phải thực hiện một số bước để vô hiệu hoá Access Key nhằm ngăn chặn lập tức những hành động tiếp theo.

1. Truy cập vào IAM Console tại [đây](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/home)

![Compromised IAM credentials](/images/3.attack/3.2/6.png)

2. Ở thanh điều hướng bên tay trái, chọn Users.
3. Dựa trên GuardDuty Finding và thông báo E-mail, chúng ta dễ dàng chọn ra được IAM user - **GuardDuty-Example-Compromised-Simulated**.

![Compromised IAM credentials](/images/3.attack/3.2/7.png)

4. Ở user **GuardDuty-Example-Compromised-Simulated**, chúng ta chọn thanh **Security Credentials**.
5. Ở mục Access Keys, dựa trên thông tin Access Key ID từ Finding, chúng ta tiến hành chọn **Action** -> **Deactivate**.

![Compromised IAM credentials](/images/3.attack/3.2/8.png)

![Compromised IAM credentials](/images/3.attack/3.2/9.png)


