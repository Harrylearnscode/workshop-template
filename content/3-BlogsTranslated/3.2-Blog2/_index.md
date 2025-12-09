---
title: "Blog 2"
date: "2025-09-09"
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---


# Gìn giữ các tiêu chuẩn đạo đức trong thời trang với multimodal toxicity detection của Amazon Bedrock Guardrails

**Tác giả:** Jean Jacques Mikem và Jordan Jones
**Ngày đăng:** 11 JUL 2025
**Chuyên mục:** Amazon Bedrock Guardrails, Amazon Simple Storage Service (S3), AWS Lambda, Intermediate (200), Technical How-to

---

Ngành thời trang toàn cầu được ước tính sẽ đạt **1,84 nghìn tỉ đô la trong năm 2025**, chiếm khoảng **1,63% GDP thế giới** (Statista, 2025). Với lượng lớn vốn được tạo ra, ngành thời trang có nguy cơ trở thành mục tiêu của các nội dung độc hại và sử dụng sai mục đích.

Trong ngành thời trang, chúng tôi ủng hộ việc sử dụng **multimodal toxicity detection** của **Amazon Bedrock Guardrails** để ngăn chặn nội dung gây hại. Nếu bạn là doanh nghiệp lớn hoặc nhãn hàng đang phát triển, phương pháp này có thể giúp bạn phát hiện các nội dung có nguy cơ độc hại trước khi chúng gây ảnh hưởng đến danh tiếng và tiêu chuẩn đạo đức của thương hiệu.

---

## Tổng quan giải pháp

Để tích hợp multimodal toxicity detection guardrails vào quy trình **image generating workflow** với Amazon Bedrock, bạn có thể sử dụng các dịch vụ AWS sau:

* **Amazon S3** để lưu trữ hình ảnh
* **Amazon S3 Event Notifications** để kích hoạt xử lý khi có hình ảnh mới
* **AWS Lambda** để xử lý hình ảnh
* **Amazon Bedrock Guardrails** để phân tích nội dung

Sơ đồ giải pháp minh họa pipeline từ upload ảnh đến xử lý kiểm duyệt tự động.

---

## Điều kiện tiên quyết

Bạn cần:

* Một AWS account
* IAM execution role
* IAM policy đầy đủ quyền truy cập CloudWatch Logs, S3, và Bedrock Guardrails

**IAM Policy tiêu chuẩn:**

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {"Sid": "CloudWatchLogsAccess","Effect": "Allow","Action": "logs:CreateLogGroup","Resource": "arn:aws:logs:<REGION>:<ACCOUNT-ID>:*"},
        {"Sid": "CloudWatchLogsStreamAccess","Effect": "Allow","Action": ["logs:CreateLogStream","logs:PutLogEvents"],"Resource": ["arn:aws:logs:<REGION>:<ACCOUNT-ID>:log-group:/aws/lambda/<FUNCTION-NAME>:*"]},
        {"Sid": "S3ReadAccess","Effect": "Allow","Action": "s3:GetObject","Resource": "arn:aws:s3:::<BUCKET-NAME>/*"},
        {"Sid": "BedrockGuardrailsAccess","Effect": "Allow","Action": "bedrock:ApplyGuardrail","Resource": "arn:aws:bedrock:<REGION>:<ACCOUNT-ID>:guardrail/<GUARDRAIL-ID>"}
    ]
}
```

---

## Tạo multimodal guardrail trong Amazon Bedrock

1. Truy cập **Amazon Bedrock Console** → Guardrails
2. Chọn **Create guardrail**
3. Nhập tên và mô tả

### Cấu hình content filters

* Chọn **Image** trong *Filter for prompts*
* Bật các danh mục:

  * Hate
  * Insults
  * Sexual
  * Violence

Misconduct và Prompt threat chỉ áp dụng cho text.

---

## Tạo S3 bucket

1. Vào **S3 Console → Create Bucket**
2. Nhập tên và khu vực
3. Giữ nguyên thiết lập mặc định

Bucket là nơi tải ảnh để kích hoạt pipeline.

---

## Tạo Lambda function

1. Vào **Lambda Console → Create function**
2. Chọn runtime Python
3. Gán IAM role với quyền phù hợp

### Python code mẫu

```python
import boto3
import json
import os
import traceback

s3_client = boto3.client('s3')
bedrock_runtime_client = boto3.client('bedrock-runtime')

GUARDRAIL_ID = '<YOUR_GUARDRAIL_ID>'
GUARDRAIL_VERSION = '<SPECIFIC_VERSION>'

SUPPORTED_FORMATS = {'jpg': 'jpeg', 'jpeg': 'jpeg', 'png': 'png'}

def lambda_handler(event, context):
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = event['Records'][0]['s3']['object']['key']

    try:
        file_ext = os.path.splitext(key)[1].lower().lstrip('.')
        image_format = SUPPORTED_FORMATS.get(file_ext)
        if not image_format:
            return {'statusCode': 400, 'body': 'Unsupported image format'}
    except Exception:
        return {'statusCode': 500, 'body': 'Error determining file format'}

    try:
        response = s3_client.get_object(Bucket=bucket, Key=key)
        image_bytes = response['Body'].read()

        if len(image_bytes) > 4 * 1024 * 1024:
            return {'statusCode': 400, 'body': 'Image size exceeds 4MB limit'}

        content_to_assess = [
            {"image": {"format": image_format, "source": {"bytes": image_bytes}}}
        ]

        guardrail_response = bedrock_runtime_client.apply_guardrail(
            guardrailIdentifier=GUARDRAIL_ID,
            guardrailVersion=GUARDRAIL_VERSION,
            source='INPUT',
            content=content_to_assess
        )

        action = guardrail_response.get('action')
        return {'statusCode': 200, 'body': f'Processed {key}. Guardrail: {action}'}

    except Exception:
        traceback.print_exc()
        return {'statusCode': 500, 'body': 'Internal server error'}
```

Đặt timeout function khoảng **30 giây**.

---

## Tạo S3 Trigger cho Lambda

* Chọn S3 làm nguồn
* Trỏ đến bucket đã tạo
* Bật *Object Created* events

---

## Kiểm tra pipeline

Tải lên ảnh dưới 4 MB vào bucket → Kiểm tra CloudWatch Logs để xem kết quả đánh giá:

* NONE → nội dung an toàn
* BLOCKED → nội dung vi phạm

Ví dụ response (rút gọn):

```json
{
  "action": "GUARDRAIL_INTERVENED",
  "outputs": [{"text": "Sorry, the model cannot answer this question."}],
  "assessments": [{"contentPolicy": {"filters": [{"type": "HATE", "action": "BLOCKED"}]}}]
}
```

---

## Clean Up

1. Xóa event notification trong S3
2. Xóa bucket
3. Xóa Lambda function
4. Xóa IAM role
5. Xóa Bedrock guardrail nếu không cần

---

## Kết luận

Việc triển khai **Amazon Bedrock Guardrails multimodal toxicity detection** giúp ngành thời trang:

* Tự động xử lý hình ảnh tải lên
* Kiểm duyệt nội dung bằng AI tiên tiến
* Giảm rủi ro và xây dựng niềm tin khách hàng
* Duy trì tính toàn vẹn thương hiệu
* Vận hành serverless, dễ mở rộng, ít bảo trì

Tham khảo thêm:

* Video *Amazon Bedrock Guardrails: Make Your AI Safe and Ethical*
* Bài viết *Amazon Bedrock Guardrails image content filters ...*

---

## Về tác giả

**Jordan Jones** – Solutions Architect tại AWS, chuyên về cloud architecture và cybersecurity. Ngoài công việc, anh thích xem giải NBA, giải Sudoku và du lịch.

**Jean Jacques Mikem** – Solutions Architect tại AWS, chuyên thiết kế các giải pháp có khả năng mở rộng, bảo mật cao và kết nối giữa nhu cầu kinh doanh và kỹ thuật.
