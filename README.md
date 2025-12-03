📌 Overview

This repository documents the cloud infrastructure I architected and implemented for a Cloud-Native Business Management System, a scalable SaaS platform supporting modules such as finance, learning, scheduling, messaging, medical features, and enterprise entities management.

I worked as part of the Infrastructure & Architecture Team, collaborating with the lead architect to design, optimize, and maintain a modern, secure, and cost-efficient AWS environment.

🏗️ Architecture Evolution
1. Initial State – Monolithic EC2 Architecture

React frontend + Node.js backend running together in EC2 t3.medium

Single deployment unit

Limited scalability

Manual deployments

Infrastructure coupling

2. Target State – Cloud-Native Microservices

Migrated to a fully decoupled and containerized architecture using AWS managed services for scalability and reliability.

🗺️ High-Level AWS Architecture Diagram (ASCII)
                    ┌─────────────────────────────┐
                    │        Route 53              │
                    └──────────────┬──────────────┘
                                   │
                    ┌──────────────▼──────────────┐
                    │         CloudFront           │
                    └───────┬──────────┬──────────┘
                            │          │
                            │          │ Static Assets (S3)
                            │
        ┌───────────────────▼─────────────────────┐
        │      Application Load Balancer (ALB)    │
        └───────────────┬───────────────┬────────┘
                        │               │
                        │               │
     ┌──────────────────▼───┐     ┌─────▼─────────────────┐
     │   ECS Fargate Task   │     │   ECS Fargate Task     │
     │   (Backend API)      │ ... │  (Other Services)      │
     └──────────────┬──────┘     └───────────┬────────────┘
                    │                        │
                    │                        │
            ┌───────▼────────┐        ┌──────▼────────┐
            │   RDS MySQL    │        │   Kafka (MSK)* │
            └────────────────┘        └───────────────┘
                      *Only when interacting with backend microservices


🌐 Frontend Hosting

Migrated static React frontend to S3 + CloudFront

Enabled global low-latency delivery through edge locations

Configured automatic cache invalidation via CodePipeline

Added custom domain + SSL using Route 53 + ACM

🔧 Application Layer – ECS Fargate

Backend deployed as ECS Fargate tasks

Task definition:

0.5 vCPU

1GB memory

Behind an Application Load Balancer

Auto Scaling:

CPU target: 70%

Memory-based scaling

Graceful shutdown and health checks

Blue/Green deployments with CodeDeploy

📨 Serverless Modules
Email Validation Service (Lambda)

Runtime: Node.js 18.x, 512MB

Event-driven architecture triggered by Cognito Post-Confirmation

Implemented:

Strategy Pattern for email providers

Observer Pattern for logging/metrics

SQS DLQ for delivery failures

Cost: ~$2.5/month for ~50K emails

🗄️ Data Layer

RDS MySQL (db.t3.micro → dev, db.t3.small → prod)

Multi-AZ for production

Daily automated snapshots (7-day retention)

Read replica for reporting workloads

Secrets Manager for rotating DB credentials

🔐 Authentication & Authorization

Cognito User Pool with custom attributes

MFA (optional for admins)

Federated authentication (Google, Facebook)

JWT access tokens (1-hour), refresh token rotation

Cognito Identity Pool for AWS-scoped permissions

🚀 CI/CD Pipeline (CodePipeline)

Workflow:

Source Stage

GitHub webhook on push to main

Build Stage (CodeBuild)

Lint + tests

Docker image build

Push to ECR

Deploy Stage (CodeDeploy)

ECS Blue/Green rollout

Automated traffic shifting

Health validation

DBDeploy (Liquibase)

Automatic DB migrations before deployment

Notifications (SNS)

Pipeline failures

Deployment outcomes

🛡️ Security Architecture

IAM roles following least privilege

Strict Security Groups:

ALB: 443 from public

ECS tasks: only from ALB

RDS: only from ECS

VPC layout:

2 public subnets (ALB)

2 private subnets (ECS)

2 isolated subnets (RDS)

WAF with AWS managed rule sets

GuardDuty enabled

S3 bucket encryption enforcement

Secrets Manager for all credentials

📊 Monitoring & Observability

CloudWatch Dashboards:

ALB latency

ECS CPU/Memory

Lambda errors

RDS connections

CloudWatch Alarms + SNS

AWS X-Ray for tracing

CloudTrail for auditing

💰 Cost Optimization

S3 Lifecycle policies

RDS reserved instances (1 year → 35% savings)

Lambda reserved concurrency (40% reduction)

CloudWatch Logs retention adjustments

Total cost (dev + prod): $450–600/month

📈 Key Results

Deployment time: 45 min → 8 min

Achieved 99.95% uptime

Automatic scaling during peak traffic

40% cost reduction vs. initial monolithic setup

👨‍💻 My Role & Responsibilities

Designed and implemented AWS cloud architecture

Built CI/CD pipelines and deployment automation

Migrated frontend & backend to cloud-native services

Developed serverless functions and event-driven workflows

Maintained documentation in Confluence

Participated in production on-call rotation

Collaborated with lead architect on design decisions
