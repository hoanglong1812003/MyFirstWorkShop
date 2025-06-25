---
title : "Các bước chuẩn bị"
date :  2025-06-11
weight : 2 
chapter : false
pre : " <b> 2. </b> "
---

### Tổng quan
Trong các bước tiếp theo, chúng ta sẽ tiến hành kích hoạt GuardDuty, Security Hub và tạo Stack.
### Tình huống
*Bài thực hành sẽ mô tả các tình huống sau*
| **Thứ tự** | **Tên**                          | **Đặc tả**                                                                                                   | **Giải pháp**                                                                                                     |
|------------|----------------------------------|--------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| 1          | [Compromised EC2 instance](3.1-public-instance/)    | Phát hiện và hồi phục EC2 instance bị tấn công                                                               | Sự kết hợp giữa **Amazon GuardDuty**, **Amazon EventBridge Event Rules** và **AWS Lambda**                      |
| 2          | [Compromised IAM credentials](3.2-private-instance/) | Xác định một cá nhân đang chủ động gọi API đến hệ thống trên AWS                                             | Khắc phục mối nguy hại này ngay lập tức một cách thủ công (*manually*)                                           |
| 3          | [IAM role exfiltration](#)       | Thông qua một credential bị rò rỉ, một cá nhân đang cố gắng xâm nhập và gọi API từ một máy chủ bên ngoài     | Tiến hành khắc phục với **AWS Lambda**                                                                           |

### Nội dung
  - [Kích hoạt GuardDuty](2.1-enableguardduty/)
  - [Kích hoạt Security Hub](2.2-enablesecurityhub/)
  - [Tạo Stack](2.3-createstack/)

  
