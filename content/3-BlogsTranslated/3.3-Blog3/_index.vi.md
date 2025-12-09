---
title: "Blog 3"
date: "2025-09-09"
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---

# Các Giao Thức Mở Cho Tính Tương Tác Giữa Các Agent – Phần 1: Giao Tiếp Giữa Các Agent Trên MCP

**Tác giả:** Nick Aldridge, Marc Brooker, Swami Sivasubramanian
**Ngày đăng:** 19/05/2025
**Chuyên mục:** Artificial Intelligence, Generative AI, Open Source, Thought Leadership

---

Tại AWS, các open standards đã ăn sâu vào DNA của chúng tôi, định hướng mọi việc chúng tôi làm. Đó là lý do tại sao chúng tôi quyết định xây dựng Amazon Elastic Cloud Compute (EC2) như một protocol-agnostic cloud computing service và Amazon SageMaker như framework-agnostic deep learning service. Cam kết về openness của chúng tôi tiếp tục được duy trì khi chúng ta bước vào kỷ nguyên AI Agent, mở rộng sang cả giao tiếp giữa các agent.

Nhiều giao thức đã xuất hiện để kích hoạt khả năng này, bao gồm **Model Context Protocol (MCP)** được Anthropic công bố dưới dạng mã nguồn mở vào năm 2024 và **Agent2Agent protocol (A2A)** được Google giới thiệu trong năm 2025. AWS tin rằng cả MCP và A2A đều có tiềm năng lớn. Để thể hiện cam kết đối với open standards, AWS đang tham gia vào ban chỉ đạo của MCP.

Với việc hỗ trợ nhiều giao thức, AWS cho phép các nhà phát triển xây dựng ứng dụng agentic mà không bị phụ thuộc vào một chuẩn duy nhất.

---

## Vai trò của các giao thức mở trong đổi mới AI

Các giao thức và chuẩn mở từ lâu đã thúc đẩy sự đổi mới: REST, GraphQL, TensorFlow, PyTorch…

Giờ đây, với sự phát triển của các AI agent, nhu cầu về **chuẩn mở cho tương tác giữa agent với công cụ, dữ liệu và agent khác** trở nên cấp thiết. MCP và A2A ra đời nhằm đáp ứng nhu cầu đó.

AWS hỗ trợ xu hướng này thông qua:

* Các dịch vụ mới
* Đóng góp mã nguồn mở
* Best practices
* Ra mắt **Strands Agents** – SDK mã nguồn mở để xây dựng AI agent chỉ với vài dòng mã, tương thích nhiều mô hình qua Amazon Bedrock, Anthropic API, Llama API, Ollama…

Strands hoạt động với MCP và sắp hỗ trợ A2A và OpenTelemetry (OTEL).

---

# Sự Phát Triển của MCP

MCP được cộng đồng đón nhận như chuẩn kết nối generative AI agents với hệ thống bên ngoài. Ban đầu chỉ tập trung vào tool integration, nhưng kiến trúc MCP vốn đã đủ linh hoạt để mở rộng sang inter-agent communication.

AWS đang hợp tác với các framework agent hàng đầu như **LangGraph, CrewAI, LlamaIndex** và các công ty như Autodesk, Confluent, Dynatrace, Elastic, IBM, Workday, Writer… để thúc đẩy tương lai của MCP.

---

# Nền Tảng Kỹ Thuật Của MCP

## 1. Streamable HTTP

Hỗ trợ nhiều interaction patterns:

* Stateless request/response
* Stateful session với persistent ID
* Real-time data streaming qua Server-Sent Events (SSE)
* Progress events, cancelation, buffering

Rất phù hợp cho long-running agents cần chia sẻ cập nhật liên tục.

## 2. Capability Discovery

Các agent cần biết nhau có thể làm gì. MCP cho phép:

* Khai báo tool/skill và tham số
* Thông báo capability mới hoặc thay đổi
* Tạo hệ sinh thái agent động, thích nghi

## 3. Bảo mật MCP

Xác thực và phân quyền dựa trên OAuth 2.0/2.1.

## 4. Context Sharing

MCP hỗ trợ chia sẻ:

* Tệp
* Trạng thái ứng dụng
* Agent memory

Qua resource capability, agent có thể khám phá và truy xuất context của nhau. Các agent cũng có thể **chia sẻ prompt** hoặc **chia sẻ LLM** thông qua *sampling*.

Quy trình sampling:

1. Server gửi sampling request
2. Client xem xét/sửa đổi
3. Client gọi LLM
4. Client xem xét kết quả
5. Client trả về server

---

# Triển Khai Giao Tiếp Agent-to-Agent Trên MCP

Ví dụ: HR Agent cần hỏi thông tin nhân viên → gọi Employee Info Agent qua MCP.

Sơ đồ tương tác gồm:

* **Agent**
* **Tool / Agent Skill**
* **MCP Server**
* **MCP Client**

## Ví dụ Java với Spring AI

### Định nghĩa Employee Info Agent

```java
@Bean
ChatClient chatClient(
    List<McpSyncClient> mcpSyncClients, 
    ChatClient.Builder builder
) {
    return builder
            .defaultToolCallbacks(
                new SyncMcpToolCallbackProvider(mcpSyncClients)
            )
            .build();
}

String employeeInfoAgent(String query) {
    return chatClient
            .prompt()
            .system("abbreviate first names with first letter and a period")
            .user(query)
            .call()
            .content();
}
```

Expose dưới dạng MCP tool:

```java
@Tool(description = "answers questions related to our employees")
String employeeQueries(
    @ToolParam(description = "the query about the employees", required = true) String query) {
    return employeeInfoAgent(query);
}
```

### Tích hợp vào HR Agent (REST API)

```java
@RestController
class ConversationalController {
    private final ChatClient chatClient;

    ConversationalController(ChatClient chatClient) {
        this.chatClient = chatClient;
    }

    @PostMapping("/inquire")
    String inquire(@RequestBody Prompt prompt) {
        return chatClient
                .prompt()
                .user(prompt.question())
                .call()
                .content();
    }
}
```

Kết quả: HR Agent và Employee Info Agent có thể giao tiếp qua MCP như microservices.

---

# Hướng Đi Tương Lai: Nâng Cấp MCP

AWS và cộng đồng MCP đang thảo luận về các cải tiến:

* **Human-in-the-loop interactions** (elicitation)
* **Streaming partial results**
* **Cải thiện capability discovery**
* **Asynchronous communication** với shared state và client-driven polling

AWS cam kết mở rộng MCP để hỗ trợ inter-agent collaboration mượt mà hơn.

---

# MCP Đang Tạo Ra Sự Hứng Thú

Nhiều công ty lớn ca ngợi MCP:

* **Confluent**: mở ra tương tác real-time
* **CrewAI**: MCP sẽ định hình tương lai agent interoperability
* **Dynatrace**: hỗ trợ tự động hóa thông minh
* **Elastic**, **IBM**, **LangChain**, **LlamaIndex**, **Writer**, **Broadcom Tanzu**… đều xác nhận MCP là lớp tương tác agent tương lai

---

# Bắt Đầu Với MCP

* Xem **tài liệu MCP chính thức**
* Tham gia GitHub discussions
* Làm theo **AWS MCP getting-started guide**
* Xem ví dụ mã inter-agent từ blog

---

# Về Tác Giả

## **Nick Aldridge**

Principal Engineer tại AWS, từng tham gia Amazon Lex, Amazon Bedrock, và lãnh đạo Bedrock Knowledge Bases.

## **Marc Brooker**

VP & Distinguished Engineer tại AWS, từng làm EC2, EBS, Lambda, Aurora DSQL.

## **Swami Sivasubramanian**

VP phụ trách AI Agentic tại AWS, lãnh đạo SageMaker, DynamoDB, Bedrock, Amazon Q; từng phục vụ trong National AI Advisory Committee.
