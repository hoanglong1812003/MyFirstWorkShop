---
title : "Phát hiện các mối đe dọa với Amazon GuardDuty"
date :  2025-06-11
weight : 1 
chapter : false
---
# Phát hiện các mối đe dọa với Amazon GuardDuty

### Tổng quan

 Trong bài lab này, các bạn sẽ được tìm hiểu về **Amazon GuardDuty** kết hợp với **AWS Security Hub**. Amazon GuardDuty là một dịch vụ có chức năng phát hiện các mối đe dọa đến hệ thống và khắc phục chúng, trong khi AWS Security Hub sẽ cung cấp cho chúng ta cái nhìn toàn diện về các cảnh báo bảo mật ưu tiên và trạng thái tuân thủ trên các tài khoản AWS. Chúng ta sẽ cùng nhau phân tích, đánh giá và làm thế nào để báo động cũng như khắc phục các vấn đề bảo mật dựa trên các "Findings" của GuardDuty và Security Hub.

 ### Về GuardDuty
 **Nguồn dữ liệu**
 Kể từ khi được kích hoạt trên một AWS Region, GuardDuty sẽ tiến hành phân tích mọi dữ liệu đến từ:
 1. VPC Flow Logs
 2. CloudTrail Logs
 3. DNS Logs
    - Nhật ký lưu trữ của DNS xuất phát từ các DNS resolvers (sở hữu bởi AWS) dành cho các VPC và không thể truy xuất trực tiếp từ phía người dùng.
    - Nếu như DNS resolver được cấu hình độc lập bởi chính bạn hoặc từ phía bên thứ 3, GuardDuty sẽ không thể truy xuất, xử lý và xác định các mối nguy hại từ nguồn dữ liệu này.
    
GuardDuty có thể truy xuất đến mọi nguồn dữ liệu (được đề cập ở trên) cho dù chúng chưa được kích hoạt từ trước.

{{% notice note %}}
Bạn nên kích hoạt đồng thời CloudTrail Logs và VPC Flow Logs để có cái nhìn tổng quan và chi tiết nhất khi tiến hành phân tích dữ liệu
{{% /notice %}}

GuardDuty là một dịch vụ mang tính chất Regional, thế nên để có thể theo dõi dữ liệu ở một AWS Region thì bạn phải kích hoạt ở AWS Region đó.

{{% notice note %}}
Bạn có thể kích hoạt thông qua AWS Console hoặc sử dụng APIs. Đa số người dùng sẽ kích hoạt ở mọi AWS Regions và AWS Accounts một cách đồng thời thông qua APIs.
{{% /notice %}}

Dù bạn có số lượng các tài nguyên AWS ít hay nhiều (ví dụ như VPCs hay IAM users), GuardDuty sẽ không gây bất kỳ ảnh hưởng nào đến bất kỳ tài nguyên nào bởi các quy trình xử lý sẽ chỉ được thực hiện nội bộ bên trong dịch vụ GuardDuty.

{{% notice note %}}
GuardDuty là một dịch vụ được quản lý hoàn toàn bởi AWS.
{{% /notice %}}

Cách tính giá của GuardDuty sẽ dựa trên

- Số lượng CloudTrail events được phân tích
- Khối lượng của VPC floư logs (theo GB)
- Khối lượng của DNS log (theo GB)

{{% notice note %}}
Mỗi tài khoản AWS sẽ có 30 ngày thử nghiệm ở mỗi AWS Region, điều này sẽ giúp GuardDuty dễ dàng dự đoán chi phí phát sinh.
{{% /notice %}}


### Findings
GuardDuty sẽ chủ động quan sát và theo dõi các dấu hiệu bất thường xuất phát từ:
- 3 nguồn dữ liệu (được đề cập ở trên)
- Các EC2 Instances
- Các tài nguyên AWS IAM
Bạn sẽ dễ dàng truy xuất chi tiết các Findings được phát hiện bởi GuardDuty ở thanh Findings. Mỗi Finding sẽ được chia nhỏ thành nhiều thông tin theo định dạng mà cho phép chúng ta dễ dàng đọc hiểu và xử lý các nguy cơ về bảo mật.

{{% notice note %}}
Tìm hiểu chi tiết hơn về danh sách của toàn bộ các loạt GuardDuty Findings tại [đây](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_finding-format.html)
{{% /notice %}}

### Về AWS Security Hub
AWS Security Hub cung cấp cho bạn cái nhìn toàn diện về các cảnh báo bảo mật ưu tiên cao và trạng thái tuân thủ trên các tài khoản AWS

Sẽ có rất nhiều các công cụ bảo mật mạnh mẽ để bạn sử dụng, từ tường lửa hay ứng dụng bảo vệ endpoint đến các ứng dụng quét lỗ hổng và sự tuân thủ bảo mật. Nhưng đôi khi, điều này khiến đội ngũ của bạn phải chuyển đổi qua lại giữa các công cụ này để đối phó với hàng trăm, hàng nghìn cảnh báo bảo mật mỗi ngày. Với Security Hub, giờ đây bạn có một nơi tổng hợp duy nhất, sắp xếp và phân độ ưu tiên các cảnh báo hoặc phát hiện bảo mật từ nhiều dịch vụ AWS (như Amazon GuardDuty, Amazon Inspector và Amazon Macie) hay từ các giải pháp cung cấp bởi đối tác của AWS.

Các rủi ro được tìm thấy sẽ được tóm tắt trực quan trên trang tổng quan tích hợp với các biểu đồ và bảng mà bạn có thể tương tác. Bạn cũng có thể giám sát hệ thống của bạn liên tục bằng cách sử dụng chức năng kiểm tra tự động sự tuân thủ so với các thực nghiệm tối ưu từ AWS và các tiêu chuẩn trên thị trường mà doanh nghiệp của bạn đang tuân theo.

### Chi phí
Thông thường, chi phí sẽ ít hơn $1 mỗi tháng nếu tài khoản của bạn chỉ sử dụng cho mục đích thử nghiệm, luyện tập và không thực hiện những cuộc tấn công giả lập.
**Kiểm tra bảo mật**
- 100,000 lần đầu tiên => $0.0010/check
- 100,001 – 500,000 => $0.0008/check
- 500,001+ => $0.0005/check
**Tìm các sự kiện tấn công**
- 10,000 lần đầu tiên => miễn phí
- 10,001+ => $0.00003/lần

### So sánh Amazon GuardDuty và AWS Security Hub

| Tiêu chí                  | Amazon GuardDuty                                                                 | AWS Security Hub                                                              |
|---------------------------|----------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| **Mục đích chính**        | Phát hiện mối đe dọa tự động dựa trên hành vi và machine learning               | Tổng hợp, chuẩn hóa và đánh giá các cảnh báo bảo mật từ nhiều nguồn          |
| **Dữ liệu xử lý**         | VPC Flow Logs, CloudTrail Events, DNS logs                                       | Cảnh báo từ GuardDuty, Inspector, IAM Access Analyzer,...                    |
| **Cách hoạt động**        | Phân tích log, sử dụng AI và threat intel để phát hiện bất thường               | Thu thập và chuẩn hóa cảnh báo theo chuẩn ASFF                               |
| **Phản ứng sự kiện**      | Gửi cảnh báo đến CloudWatch, kích hoạt Lambda                                    | Cho phép tạo Custom Actions, tích hợp với Lambda, EventBridge                |
| **Chuẩn cảnh báo**        | Định dạng nội bộ riêng                                                            | AWS Security Finding Format (ASFF)                                           |
| **Ưu điểm**               |  Không cần cài agent, phát hiện real-time, tự động                      |  Tổng hợp toàn diện, tích hợp nhiều nguồn, hỗ trợ Best Practice     |
| **Nhược điểm**            |  Không có báo cáo tổng hợp, có thể tạo cảnh báo giả                         |  Phụ thuộc vào nguồn cảnh báo khác, có thể quá tải thông tin            |

> Có thể **kết hợp cả hai**: sử dụng GuardDuty để phát hiện mối đe dọa, sau đó gửi findings về Security Hub để tổng hợp, đánh giá và phản ứng.



### Nội dung

 1. [Giới thiệu](1-introduce/)
 2. [GuardDuty & Security Hub](2-Prerequiste/)
 3. [Compromised EC2 Instance](3-Accessibilitytoinstance/)
 4. [Compromised IAM credentials](4-s3log/)
 5. [Credential Exfiltration](5-Portfwd/)
 6. [Dọn dẹp tài nguyên](6-cleanup/)
