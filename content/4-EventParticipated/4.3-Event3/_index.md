---
title: "Event 2"
date: "2025-09-09"
weight: 1
chapter: false
pre: " <b> 4.2. </b> "
---

# Summary Report: "DevOps on AWS" --- Paraphrased Version

## Event Objectives

-   Gain a solid understanding of DevOps culture along with essential
    performance indicators such as DORA metrics and MTTR.
-   Learn how to automate build, test, and deployment processes using
    the AWS CI/CD toolset.
-   Explore Infrastructure as Code (IaC) practices using CloudFormation
    and AWS CDK.
-   Examine containerization approaches with ECR, ECS, EKS, and App
    Runner.
-   Build end-to-end observability with CloudWatch and X-Ray.

## Speakers

-   **AWS DevOps Experts**
-   **Senior Solutions Architects**

## Key Highlights

### DevOps Culture & Mindset

-   **Revisited Concepts:** Built on AI/ML foundations from earlier
    sessions.
-   **Importance of Metrics:** Emphasis on Deployment Frequency, Lead
    Time for Changes, MTTR, and Change Failure Rate.
-   **Cultural Transformation:** Encouraging shared ownership and
    dismantling silos.

### AWS CI/CD Pipeline

-   **Source Control:** Leveraged **AWS CodeCommit** with GitFlow or
    trunk-based workflows.
-   **Build & Test:** Automated compilation and testing via **AWS
    CodeBuild**.
-   **Deployments:** Implemented deployment patterns using **AWS
    CodeDeploy**:
    -   **Blue/Green:** Minimizes downtime and deployment risks.
    -   **Canary:** Gradual traffic shifting to validate behavior on a
        small user group.
    -   **Rolling:** Sequential updates across instances.
-   **Pipeline Automation:** Coordinated end-to-end automation through
    **AWS CodePipeline**.

### Infrastructure as Code

-   **CloudFormation:** Defined infrastructure with templates and
    managed drift across stacks.
-   **AWS CDK:** Used programming languages to create infrastructure
    using high-level constructs.
-   **Choosing IaC Tools:** Evaluated declarative (CloudFormation)
    vs. imperative (CDK) styles based on team capabilities.

### Container Services & Observability

-   **Container Lifecycle:** Managed images in **Amazon ECR** using
    lifecycle rules.
-   **Service Options:** Compared **ECS** for simplicity, **EKS** for
    Kubernetes compatibility, and **App Runner** for streamlined
    deployments.
-   **Monitoring:** Applied **CloudWatch** metrics/alarms and **X-Ray**
    tracing to detect performance issues.

## Key Takeaways

### DevOps Strategy

-   **Automate Everything:** Reduce human error by automating deployment
    and infrastructure processes.
-   **Measure Continuously:** Track progress using DORA metrics to
    evaluate delivery efficiency.
-   **Shift Left:** Integrate testing and security early in the
    pipeline.

### Technical Architecture

-   **Immutable Infrastructure:** Replace servers instead of patching
    them.
-   **Containerized Environments:** Promote consistent behavior across
    Dev, Test, and Prod.
-   **Deep Observability:** Move past basic uptime checks by adopting
    distributed tracing.

### Practical Applications

-   **Build CI/CD Pipelines:** Create CodePipeline flows for Spring Boot
    or React workloads.
-   **Adopt IaC:** Provision AWS services (databases, S3, Cognito, etc.)
    using **AWS CDK**.
-   **Containerize Projects:** Push Docker images to ECR to standardize
    deployments.
-   **Enhance Monitoring:** Add X-Ray tracing to analyze latency and
    backend performance.

## Event Experience

The **"DevOps on AWS"** workshop delivered a hands-on perspective on
modern software delivery, bridging the gap between code development and
production reliability.

### Learning from Experts

-   Clarified the AWS DevOps ecosystem---CodeCommit, CodeBuild,
    CodeDeploy, CodePipeline---and how they interconnect.
-   Understood how DORA metrics support conversations with business
    leaders about DevOps investment.

### Hands-On Insights

-   **CI/CD Demo:** Showed how code pushes automatically trigger builds
    and deployments.
-   **IaC with CDK:** Highlighted the benefits of writing infrastructure
    in TypeScript/Java over JSON/YAML.
-   **Deployment Models:** Blue/Green demos illustrated how to release
    updates without service interruptions.

### Networking & Peer Discussions

-   Discussed trade-offs between ECS and EKS, realizing that ECS or App
    Runner often provide quicker, simpler paths to production.
-   Shared solutions for managing database schema changes within CI/CD.

### Key Lessons

-   CloudFormation drift detection is crucial for infrastructure
    reliability.
-   Observability is mandatory for microservices---X-Ray is
    indispensable for tracing distributed systems.
-   Strong DevOps practices drastically reduce MTTR and support fast,
    safe innovation.

### Event Photo

![Photo](/images/aws2.jpg)
