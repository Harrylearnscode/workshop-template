---
title: "Bản đề xuất"
date: "2025-09-09"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

Tại phần này, bạn cần tóm tắt các nội dung trong workshop mà bạn **dự tính** sẽ làm.

# IoT Weather Platform for Lab Research  
## Giải pháp AWS Serverless hợp nhất cho giám sát thời tiết thời gian thực  

### 1. Tóm tắt điều hành  
IoT Weather Platform được thiết kế dành cho nhóm *ITea Lab* tại TP. Hồ Chí Minh nhằm nâng cao khả năng thu thập và phân tích dữ liệu thời tiết. Nền tảng hỗ trợ tối đa 5 trạm thời tiết, có khả năng mở rộng lên 10–15 trạm, sử dụng thiết bị biên Raspberry Pi kết hợp cảm biến ESP32 để truyền dữ liệu qua MQTT. Nền tảng tận dụng các dịch vụ AWS Serverless để cung cấp giám sát thời gian thực, phân tích dự đoán và tiết kiệm chi phí, với quyền truy cập giới hạn cho 5 thành viên phòng lab thông qua Amazon Cognito.  

### 2. Tuyên bố vấn đề  
*Vấn đề hiện tại*  
Các trạm thời tiết hiện tại yêu cầu thu thập dữ liệu thủ công, khó quản lý khi có nhiều trạm. Không có hệ thống tập trung cho dữ liệu hoặc phân tích thời gian thực, và các nền tảng bên thứ ba thường tốn kém và quá phức tạp.  

*Giải pháp*  
Nền tảng sử dụng AWS IoT Core để tiếp nhận dữ liệu MQTT, AWS Lambda và API Gateway để xử lý, Amazon S3 để lưu trữ (bao gồm data lake), và AWS Glue Crawlers cùng các tác vụ ETL để trích xuất, chuyển đổi, tải dữ liệu từ S3 data lake sang một S3 bucket khác để phân tích. AWS Amplify với Next.js cung cấp giao diện web, và Amazon Cognito đảm bảo quyền truy cập an toàn. Tương tự như Thingsboard và CoreIoT, người dùng có thể đăng ký thiết bị mới và quản lý kết nối, nhưng nền tảng này hoạt động ở quy mô nhỏ hơn và phục vụ mục đích sử dụng nội bộ. Các tính năng chính bao gồm bảng điều khiển thời gian thực, phân tích xu hướng và chi phí vận hành thấp.  

*Lợi ích và hoàn vốn đầu tư (ROI)*  
Giải pháp tạo nền tảng cơ bản để các thành viên phòng lab phát triển một nền tảng IoT lớn hơn, đồng thời cung cấp nguồn dữ liệu cho những người nghiên cứu AI phục vụ huấn luyện mô hình hoặc phân tích. Nền tảng giảm bớt báo cáo thủ công cho từng trạm thông qua hệ thống tập trung, đơn giản hóa quản lý và bảo trì, đồng thời cải thiện độ tin cậy dữ liệu. Chi phí hàng tháng ước tính 0,66 USD (theo AWS Pricing Calculator), tổng cộng 7,92 USD cho 12 tháng. Tất cả thiết bị IoT đã được trang bị từ hệ thống trạm thời tiết hiện tại, không phát sinh chi phí phát triển thêm. Thời gian hoàn vốn 6–12 tháng nhờ tiết kiệm đáng kể thời gian thao tác thủ công.  

### 3. Kiến trúc giải pháp  

Trang web được host trên EC2. Dữ liệu được lưu trữ bằng EC2 instance. Hình ảnh được lưu trên S3. Code sẽ dược đẩy lên github nhằm quản lý và tự động đẩy code lên s3 để CodeDeploy sẽ thực hiện deploy lên server. Cloudfront được sử dụng nhằm cải thiện tốc tải. Cognito dùng để quản lý danh tính người dùng. CloudTrail được dùng để giám sát và lữu trữ lịch sử hoạt động. CloudWatch dùng để giám sát và quản lý hiệu suất, tình trạng hoạt động của các tài nguyên và ứng dụng trên AWS. IAM dùng để cấp quyền cho các service. SecretManager được dùng nhằm quản lý các thông tin nhạy cảm.

![AWS Architecture](../../static/images/2-Proposal/AWS_Architecture.jpeg)

*Dịch vụ AWS sử dụng*  
- *WAF*: Bảo vệ ứng dụng web khỏi các tấn công mạng
- *AWS CloudFront*: Xử lý dữ liệu và kích hoạt Glue jobs (2 hàm).  
- *AWS EC2*: Deploy sản phẩm, NAT instance, Database.
- *AWS VPC *: Giao tiếp với ứng dụng web.  
- *AWS S3*: Lưu trữ code, file log, hình ảnh.
- *CodeDeploy*: Deploy code lên EC2.
- *GitLab*: chứa source code và push code lên s3.
- *Amazon Cognito*: Quản lý quyền truy cập cho người dùng trang web.  
- *IAM*: Tạo user và role.
- *Secret Manager*: Chứa các thông tin quan trọng.
- *CloudTrail*: Giám sát và lữu trữ lịch sử hoạt động
- *CloudWatch*: Giám sát và quản lý hiệu suất, tình trạng hoạt động của các tài nguyên và ứng dụng trên AWS

### 4. Triển khai kỹ thuật  
*Các giai đoạn triển khai*  
Dự án gồm 2 phần — thiết lập trạm thời tiết biên và xây dựng nền tảng thời tiết — mỗi phần trải qua 4 giai đoạn:  
1. *Nghiên cứu và vẽ kiến trúc*: Nghiên cứu Raspberry Pi với cảm biến ESP32 và thiết kế kiến trúc AWS Serverless (1 tháng trước kỳ thực tập).  
2. *Tính toán chi phí và kiểm tra tính khả thi*: Sử dụng AWS Pricing Calculator để ước tính và điều chỉnh (Tháng 1).  
3. *Điều chỉnh kiến trúc để tối ưu chi phí/giải pháp*: Tinh chỉnh (ví dụ tối ưu Lambda với Next.js) để đảm bảo hiệu quả (Tháng 2).  
4. *Phát triển, kiểm thử, triển khai*: Lập trình Raspberry Pi, AWS services với CDK/SDK và ứng dụng Next.js, sau đó kiểm thử và đưa vào vận hành (Tháng 2–3).  

*Yêu cầu kỹ thuật*  
- *Trạm thời tiết biên*: Cảm biến (nhiệt độ, độ ẩm, lượng mưa, tốc độ gió), vi điều khiển ESP32, Raspberry Pi làm thiết bị biên. Raspberry Pi chạy Raspbian, sử dụng Docker để lọc dữ liệu và gửi 1 MB/ngày/trạm qua MQTT qua Wi-Fi.  
- *Nền tảng thời tiết*: Kiến thức thực tế về AWS Amplify (lưu trữ Next.js), Lambda (giảm thiểu do Next.js xử lý), AWS Glue (ETL), S3 (2 bucket), IoT Core (gateway và rules), và Cognito (5 người dùng). Sử dụng AWS CDK/SDK để lập trình (ví dụ IoT Core rules tới S3). Next.js giúp giảm tải Lambda cho ứng dụng web fullstack.

### 5. Lộ trình & Mốc triển khai  
- *Trước thực tập (Tháng 0)*: 1 tháng lên kế hoạch và đánh giá trạm cũ.  
- *Thực tập (Tháng 1–3)*:  
    - Tháng 1: Học AWS và nâng cấp phần cứng.  
    - Tháng 2: Thiết kế và điều chỉnh kiến trúc.  
    - Tháng 3: Triển khai, kiểm thử, đưa vào sử dụng.  
- *Sau triển khai*: Nghiên cứu thêm trong vòng 1 năm.  

### 6. Ước tính ngân sách  
Có thể xem chi phí trên [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=621f38b12a1ef026842ba2ddfe46ff936ed4ab01)  
Hoặc tải [tệp ước tính ngân sách](../attachments/budget_estimation.pdf).  

*Chi phí hạ tầng*  
- AWS Lambda: 0,00 USD/tháng (1.000 request, 512 MB lưu trữ).  
- S3 Standard: 0,15 USD/tháng (6 GB, 2.100 request, 1 GB quét).  
- Truyền dữ liệu: 0,02 USD/tháng (1 GB vào, 1 GB ra).  
- AWS Amplify: 0,35 USD/tháng (256 MB, request 500 ms).  
- Amazon API Gateway: 0,01 USD/tháng (2.000 request).  
- AWS Glue ETL Jobs: 0,02 USD/tháng (2 DPU).  
- AWS Glue Crawlers: 0,07 USD/tháng (1 crawler).  
- MQTT (IoT Core): 0,08 USD/tháng (5 thiết bị, 45.000 tin nhắn).  

*Tổng*: 0,7 USD/tháng, 8,40 USD/12 tháng  
- *Phần cứng*: 265 USD một lần (Raspberry Pi 5 và cảm biến).  

### 7. Đánh giá rủi ro  
*Ma trận rủi ro*  
- Mất mạng: Ảnh hưởng trung bình, xác suất trung bình.  
- Hỏng cảm biến: Ảnh hưởng cao, xác suất thấp.  
- Vượt ngân sách: Ảnh hưởng trung bình, xác suất thấp.  

*Chiến lược giảm thiểu*  
- Mạng: Lưu trữ cục bộ trên Raspberry Pi với Docker.  
- Cảm biến: Kiểm tra định kỳ, dự phòng linh kiện.  
- Chi phí: Cảnh báo ngân sách AWS, tối ưu dịch vụ.  

*Kế hoạch dự phòng*  
- Quay lại thu thập thủ công nếu AWS gặp sự cố.  
- Sử dụng CloudFormation để khôi phục cấu hình liên quan đến chi phí.  

### 8. Kết quả kỳ vọng  
*Cải tiến kỹ thuật*: Dữ liệu và phân tích thời gian thực thay thế quy trình thủ công. Có thể mở rộng tới 10–15 trạm.  
*Giá trị dài hạn*: Nền tảng dữ liệu 1 năm cho nghiên cứu AI, có thể tái sử dụng cho các dự án tương lai.