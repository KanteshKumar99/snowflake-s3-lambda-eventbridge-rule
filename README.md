# 📈 Automated Forex Data Pipeline (AWS + Snowflake)

<div align="center">

**Developed by [Kantesh Kumar](https://www.linkedin.com/in/kantesh-kumar-863778331)**

[![Python 3.13](https://img.shields.io/badge/python-3.13-blue.svg?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/downloads/release/python-3130/)
[![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/)
[![Snowflake](https://img.shields.io/badge/snowflake-%2329B5E8.svg?style=for-the-badge&logo=snowflake&logoColor=white)](https://www.snowflake.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

*A fully serverless, event-driven pipeline for real-time Forex exchange rate ingestion and analytics.*

</div>

---

## 📌 Table of Contents

- [Overview](#-overview)
- [Architecture](#-architecture)
- [Tech Stack](#-tech-stack)
- [Repository Structure](#-repository-structure)
- [Technical Workflow](#-technical-workflow)
- [Deployment Guide](#-deployment-guide)
- [Configuration](#-configuration)
- [Connect](#-lets-connect)

---

## 🚀 Overview

This project implements a **production-grade Data Engineering pipeline** that automates the full lifecycle of Forex exchange rate data — from real-time extraction to analytical storage. Built on a serverless foundation, the pipeline is designed for reliability, scalability, and low operational overhead.

Key capabilities:
- ⏱️ **Scheduled ingestion** via AWS EventBridge
- 🔒 **Secure credential management** via AWS Secrets Manager
- 🗄️ **Raw data archival** in Amazon S3 for auditability and replayability
- 🔄 **Idempotent loading** into Snowflake using `MERGE`-based deduplication

---

## 🏗️ Architecture

The diagram below illustrates the end-to-end event-driven flow of the pipeline.

![Architecture Diagram](<img width="1884" height="694" alt="image" src="https://github.com/user-attachments/assets/1f2f1e4e-de59-4937-8729-20a01258cb03" />)

> **Flow:** EventBridge → Lambda (Extract) → S3 (Raw Landing Zone) → Snowflake (Staging → Analytics Table)

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **Orchestration** | AWS EventBridge (Scheduled Rules) |
| **Compute** | AWS Lambda (Python 3.13 Runtime) |
| **Raw Storage** | Amazon S3 |
| **Secret Management** | AWS Secrets Manager |
| **Data Warehouse** | Snowflake |
| **Transformation** | SQL — Stored Procedures & MERGE Statements |
| **Language** | Python 3.13 |

---

## 📁 Repository Structure

```
forex-data-pipeline/
│
├── architecture/
│   └── Project pic.jpg          # End-to-end architecture diagram
│
├── code/
│   ├── lambda_function.py       # Core ETL logic: API extraction & Snowflake ingestion
│   └── snowflake_setup.sql      # DDL: database, staging tables & stored procedures
│
├── documentation/
│   ├── environment-variables.txt  # Lambda environment variable configuration guide
│   ├── roles.txt                  # Required IAM policies for execution
│   └── secret-manager.txt         # AWS Secrets Manager setup guide
│
├── convention.txt                 # Project coding and naming standards
└── README.md
```

---

## ⚙️ Technical Workflow

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│  AWS EventBridge│────▶│   AWS Lambda     │────▶│   Forex API     │
│  (Scheduled)    │     │  (Python 3.13)   │     │  (JSON Source)  │
└─────────────────┘     └──────────────────┘     └────────┬────────┘
                                                           │
                                          ┌────────────────▼────────────────┐
                                          │         Amazon S3               │
                                          │     (Raw JSON Landing Zone)     │
                                          └────────────────┬────────────────┘
                                                           │
                                          ┌────────────────▼────────────────┐
                                          │           Snowflake             │
                                          │  Staging → MERGE → Analytics   │
                                          └─────────────────────────────────┘
```

1. **Trigger** — AWS EventBridge fires the pipeline on a defined schedule (e.g., hourly/daily).
2. **Extraction** — AWS Lambda (Python 3.13) calls the live Forex API and retrieves JSON exchange rate data.
3. **Storage** — Raw JSON is persisted in Amazon S3 as the landing zone, enabling historical replay and auditability.
4. **Security** — All credentials (API keys, Snowflake connection strings) are fetched at runtime from AWS Secrets Manager — never hardcoded.
5. **Loading & Deduplication** — Data is pushed into a Snowflake staging table. A Stored Procedure executes a `MERGE` statement to upsert records into the final analytics table, preventing duplicates.

---

## 🚢 Deployment Guide

### Prerequisites

- AWS account with permissions for Lambda, EventBridge, S3, and Secrets Manager
- Snowflake account with a dedicated database/schema
- Python 3.13 compatible Snowflake Connector Lambda Layer

### Step 1 — Snowflake Setup

Run the setup script to initialize the database, staging table, analytics table, and stored procedure:

```sql
-- Run in your Snowflake worksheet
-- File: code/snowflake_setup.sql
```

### Step 2 — AWS Secrets Manager

Configure your secrets as described in `documentation/secret-manager.txt`. The Lambda function expects the following secret keys:

```
SNOWFLAKE_ACCOUNT
SNOWFLAKE_USER
SNOWFLAKE_PASSWORD
FOREX_API_KEY
```

### Step 3 — S3 Bucket

Create an S3 bucket for the raw landing zone. Note the bucket name for use in environment variables.

### Step 4 — IAM Role

Attach the IAM policies described in `documentation/roles.txt` to your Lambda execution role.

### Step 5 — Lambda Deployment

1. Package `code/lambda_function.py` with the Snowflake Connector Layer.
2. Deploy to AWS Lambda with the **Python 3.13** runtime.
3. Set the environment variables per `documentation/environment-variables.txt`.
4. Configure the timeout and memory to accommodate Snowflake connection latency (recommended: ≥ 30s timeout).

### Step 6 — EventBridge Rule

Create a scheduled rule in AWS EventBridge pointing to your Lambda function. Example cron for hourly ingestion:

```
cron(0 * * * ? *)
```

---

## ⚙️ Configuration

All environment variables required by the Lambda function are documented in `documentation/environment-variables.txt`. Key variables include:

| Variable | Description |
|---|---|
| `S3_BUCKET_NAME` | Target S3 bucket for raw JSON |
| `SECRET_NAME` | AWS Secrets Manager secret identifier |
| `SNOWFLAKE_DATABASE` | Target Snowflake database |
| `SNOWFLAKE_SCHEMA` | Target Snowflake schema |
| `SNOWFLAKE_WAREHOUSE` | Snowflake virtual warehouse name |

---

## 🤝 Let's Connect!

I'm an aspiring **Cloud Data Engineer** passionate about building scalable data solutions on the AWS ecosystem. Feel free to reach out!

<div align="center">

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Kantesh%20Kumar-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/kantesh-kumar-863778331)
[![GitHub](https://img.shields.io/badge/GitHub-Kantesh--Kumar-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/Kantesh-Kumar)

</div>

---

<div align="center">
  <sub>Built with ☁️ on AWS & ❄️ Snowflake</sub>
</div>
# snowflake-s3-lambda-eventbridge-rule
