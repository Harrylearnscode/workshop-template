# Báo cáo Tóm tắt: "DevOps trên AWS" --- Phiên bản Diễn đạt lại (Tiếng Việt)

## Mục tiêu Sự kiện

-   Hiểu rõ văn hóa DevOps cùng các chỉ số hiệu suất quan trọng như DORA
    và MTTR.
-   Học cách tự động hóa quy trình build, test và deploy bằng bộ công cụ
    CI/CD của AWS.
-   Nắm vững phương pháp Infrastructure as Code (IaC) với CloudFormation
    và AWS CDK.
-   Tìm hiểu chiến lược container hóa qua ECR, ECS, EKS và App Runner.
-   Xây dựng khả năng quan sát (observability) toàn diện bằng CloudWatch
    và X-Ray.

## Diễn giả

-   **Các chuyên gia DevOps từ AWS**
-   **Những Kiến trúc sư Giải pháp cấp cao**

------------------------------------------------------------------------

## Các nội dung chính

### Văn hóa & Tư duy DevOps

-   **Ôn tập:** Tiếp nối các khái niệm AI/ML từ những buổi trước.
-   **Tầm quan trọng của số liệu:** Tập trung vào Deployment Frequency,
    Lead Time for Changes, MTTR và Change Failure Rate.
-   **Chuyển đổi văn hóa:** Thúc đẩy tinh thần trách nhiệm chung, phá bỏ
    mô hình làm việc theo silo.

### Pipeline CI/CD trên AWS

-   **Quản lý mã nguồn:** Sử dụng **AWS CodeCommit** cùng các chiến lược
    Git như GitFlow hoặc trunk-based.
-   **Build & Test:** Tự động hóa build và kiểm thử bằng **AWS
    CodeBuild**.
-   **Triển khai:** Áp dụng các mô hình triển khai an toàn thông qua
    **AWS CodeDeploy**:
    -   **Blue/Green:** Giảm thời gian downtime và rủi ro.
    -   **Canary:** Xả lưu lượng dần cho một nhóm người dùng nhỏ.
    -   **Rolling:** Cập nhật lần lượt từng nhóm instance.
-   **Tổng hợp pipeline:** Kết nối toàn bộ quy trình bằng **AWS
    CodePipeline**.

### Infrastructure as Code

-   **CloudFormation:** Định nghĩa hạ tầng bằng template, quản lý drift
    giữa các stack.
-   **AWS CDK:** Dùng các ngôn ngữ lập trình để mô hình hóa tài nguyên
    AWS bằng các construct cấp cao.
-   **So sánh IaC:** Lựa chọn giữa CloudFormation (khai báo) và CDK
    (mệnh lệnh) dựa trên năng lực đội ngũ.

### Dịch vụ Container & Observability

-   **Quản lý vòng đời container:** Lưu trữ image trong **Amazon ECR**
    với lifecycle policy.
-   **Dịch vụ chạy container:** So sánh **ECS** (đơn giản, native AWS),
    **EKS** (chuẩn Kubernetes), và **App Runner** (triển khai PaaS đơn
    giản).
-   **Giám sát:** Sử dụng **CloudWatch** cho metric/alarm và **X-Ray**
    để tracing và tìm bottleneck hiệu năng.

------------------------------------------------------------------------

## Các điểm rút ra

### Chiến lược DevOps

-   **Tự động hóa trước tiên:** Giảm lỗi thủ công nhờ tự động hóa từ hạ
    tầng đến triển khai.
-   **Đo lường liên tục:** Sử dụng DORA để đánh giá tốc độ và độ ổn
    định.
-   **Shift Left:** Đưa kiểm thử & bảo mật vào sớm trong pipeline.

### Kiến trúc Kỹ thuật

-   **Immutable Infrastructure:** Thay thế thay vì cập nhật trực tiếp
    trên server.
-   **Container hóa:** Tạo môi trường nhất quán giữa Dev -- Test --
    Prod.
-   **Observability sâu:** Không chỉ monitoring uptime mà cần tracing
    toàn hệ thống.

### Ứng dụng vào công việc

-   **Triển khai CI/CD:** Thiết lập CodePipeline cho ứng dụng Spring
    Boot hoặc React.
-   **Áp dụng IaC:** Khai báo tài nguyên AWS (CSDL, S3, Cognito...) bằng
    **AWS CDK**.
-   **Container hóa:** Dockerize microservices và đẩy image lên ECR.
-   **Nâng cao giám sát:** Thêm X-Ray để phân tích độ trễ API và truy
    vấn DB.

------------------------------------------------------------------------

## Trải nghiệm tại sự kiện

Workshop **"DevOps on AWS"** mang lại góc nhìn thực tế về tự động hóa
quy trình triển khai phần mềm, giúp thu hẹp khoảng cách giữa lập trình
và vận hành sản phẩm.

### Học hỏi từ chuyên gia

-   Hiểu rõ hệ sinh thái DevOps của AWS (CodeCommit, CodeBuild,
    CodeDeploy, CodePipeline).
-   Biết cách dùng DORA để giải thích giá trị DevOps cho lãnh đạo doanh
    nghiệp.

### Trải nghiệm thực hành

-   **CI/CD Demo:** Minh họa cách code push tự động kích hoạt build &
    deploy.
-   **IaC bằng CDK:** Trực quan hơn khi viết hạ tầng bằng
    TypeScript/Java thay vì JSON/YAML.
-   **Chiến lược triển khai:** Blue/Green cho thấy cách update không
    downtime.

### Thảo luận & kết nối

-   Thảo luận về việc chọn ECS hay EKS --- nhận ra ECS/App Runner phù
    hợp cho triển khai nhanh với ít overhead.
-   Chia sẻ kinh nghiệm xử lý database migration trong pipeline.

### Bài học quan trọng

-   Drift detection trong CloudFormation rất quan trọng để giữ ổn định
    hạ tầng.
-   Observability là bắt buộc cho microservices --- X-Ray giúp tracing
    hệ thống phân tán.
-   Một nền tảng DevOps vững mạnh giảm MTTR đáng kể và hỗ trợ đổi mới
    nhanh.

------------------------------------------------------------------------

### Hình ảnh sự kiện

![Photo](/images/aws2.jpg)
