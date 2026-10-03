# 🚀 GCP CloudOps Platform

A production-oriented cloud platform built on **Google Cloud Platform (GCP)** to demonstrate real-world Cloud Engineering, DevOps, Infrastructure as Code, CI/CD, security, networking, observability, and reliability practices.

The project is intentionally designed as a continuously evolving platform. It starts with a serverless architecture using **Cloud Run** and progressively evolves toward **GKE, advanced DevOps automation, observability, and AI-powered incident management**.

---

## 🎯 Project Objective

The goal of this project is not to build a complex business application.

The primary goal is to demonstrate the ability to:

* Design production-oriented GCP architectures
* Deploy containerized applications
* Implement secure cloud networking
* Apply least-privilege IAM
* Use managed GCP services effectively
* Manage infrastructure using Terraform
* Build CI/CD pipelines with GitHub Actions
* Implement monitoring, logging, and alerting
* Troubleshoot real-world cloud failures
* Compare Cloud Run and GKE architectures
* Gradually introduce AI/agentic capabilities into a DevOps platform

The application layer will remain intentionally simple so that the focus stays on the **cloud platform and engineering practices**.

---

# 🏗️ Architecture

## Current Target Architecture

```text
                         ┌───────────────────┐
                         │       USER        │
                         │ Browser / Client  │
                         └─────────┬─────────┘
                                   │
                                  HTTPS
                                   │
                                   ▼
                         ┌───────────────────┐
                         │    Cloud DNS      │
                         └─────────┬─────────┘
                                   │
                                   ▼
                    ┌──────────────────────────┐
                    │ Global External HTTPS LB │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                         ┌────────────────┐
                         │   Cloud Run    │
                         │                │
                         │  Revision v1   │
                         │  Revision v2   │
                         │                │
                         │ Traffic Split  │
                         │ Autoscaling    │
                         └───────┬────────┘
                                 │
                ┌────────────────┼────────────────┐
                │                │                │
                ▼                ▼                ▼
         ┌────────────┐   ┌─────────────┐  ┌──────────────┐
         │    VPC     │   │   Secret    │  │    Cloud     │
         │            │   │   Manager   │  │   Storage    │
         │ Direct VPC │   │             │  │   Private    │
         │   Egress   │   │ DB Secrets  │  │   Bucket     │
         └─────┬──────┘   └─────────────┘  └──────────────┘
               │
               │ Private IP
               ▼
        ┌─────────────────┐
        │    Cloud SQL    │
        │   PostgreSQL    │
        │                 │
        │ Private IP      │
        │ HA              │
        │ Backup + PITR   │
        └─────────────────┘


              ─────── CI/CD ───────

Developer
    │
    ▼
GitHub
    │
    ▼
GitHub Actions
    │
    ├── Tests
    ├── Build
    ├── Security Checks
    ├── Terraform Plan
    └── Deployment
            │
            ▼
           GCP


              ───── Observability ─────

Cloud Run ──────┐
Cloud SQL ──────┤
Load Balancer ──┼──► Cloud Monitoring
Application ────┤
VPC ────────────┘
                      │
                      ├── Metrics
                      ├── Logs
                      ├── Dashboards
                      └── Alerts
```

> **Note:** The architecture above represents the target architecture. Components will be implemented incrementally and documented as they become operational.

---

# 🧩 Core GCP Services

The project is planned around the following services:

| Area           | GCP Service                         |
| -------------- | ----------------------------------- |
| Compute        | Cloud Run                           |
| Networking     | VPC                                 |
| Database       | Cloud SQL for PostgreSQL            |
| Storage        | Cloud Storage                       |
| DNS            | Cloud DNS                           |
| Ingress        | Global External HTTPS Load Balancer |
| Secrets        | Secret Manager                      |
| IAM            | Cloud IAM                           |
| Monitoring     | Cloud Monitoring                    |
| Logging        | Cloud Logging                       |
| Infrastructure | Terraform                           |
| CI/CD          | GitHub Actions                      |

Additional services may be introduced as the architecture evolves.

---

# 📦 Application

The application will be a lightweight REST API representing a simple cloud-based business service.

Planned API capabilities include:

```text
GET    /health

GET    /products
GET    /products/{id}

POST   /orders
GET    /orders/{id}

POST   /uploads
```

The application will interact with:

```text
Cloud Run
    │
    ├── Cloud SQL
    │
    ├── Cloud Storage
    │
    └── Secret Manager
```

The application is deliberately kept simple because the primary focus is the **cloud platform**.

---

# 🔐 Security Architecture

Security is a core part of the project.

The platform will follow:

### Least Privilege

Cloud Run will use a dedicated service account with only the permissions required by the application.

Example capabilities:

```text
Cloud Run Service Account
        │
        ├── Cloud SQL access
        ├── Cloud Storage access
        └── Secret Manager access
```

The project will avoid:

```text
❌ Owner / Editor permissions for workloads
❌ Long-lived service-account JSON keys
❌ Hard-coded credentials
❌ Secrets committed to Git
❌ Unnecessary public database access
```

### Network Security

The target design uses:

```text
Cloud Run
    │
    ▼
Direct VPC Egress
    │
    ▼
VPC
    │
    ▼
Private IP
    │
    ▼
Cloud SQL
```

The database is not intended to be directly exposed to the public internet.

---

# 🌐 Networking

The networking design will demonstrate:

* VPC
* Regional subnet
* Private IP connectivity
* Direct VPC Egress
* Firewall concepts
* DNS
* HTTPS Load Balancing
* Application ingress
* Private service communication

The project will also document the reasoning behind each networking decision.

---

# 🗄️ Database

The platform will use:

**Cloud SQL for PostgreSQL**

Planned capabilities:

* Private IP
* High availability
* Automated backups
* Point-in-time recovery
* Connection management
* Monitoring
* Application connectivity
* Database troubleshooting

The database will contain only the data required for the demonstration application.

---

# 📁 Object Storage

Cloud Storage will be used for application files.

Example structure:

```text
Cloud Storage Bucket
│
├── product-images/
├── documents/
└── uploads/
```

The bucket will remain private.

Where appropriate, the application will generate short-lived signed URLs so clients can interact with objects without making the bucket public.

---

# 🚀 Cloud Run Deployment Strategy

Cloud Run revisions will be used to demonstrate controlled deployments.

Example:

```text
Revision v1
    │
    └── 95% traffic

Revision v2
    │
    └── 5% traffic
```

The project will demonstrate:

* Revisions
* Traffic splitting
* Canary releases
* Rollbacks
* Autoscaling
* Concurrency
* Minimum and maximum instances
* Runtime service accounts
* Application troubleshooting

---

# 🏗️ Infrastructure as Code

Infrastructure will be managed using **Terraform**.

Planned structure:

```text
terraform/
│
├── modules/
│   ├── networking/
│   ├── cloud-run/
│   ├── cloud-sql/
│   ├── storage/
│   ├── iam/
│   ├── load-balancer/
│   └── monitoring/
│
└── environments/
    ├── dev/
    ├── staging/
    └── prod/
```

The objective is to make infrastructure:

* Repeatable
* Version controlled
* Reviewable
* Reproducible
* Environment-aware

---

# 🔄 CI/CD

GitHub Actions will eventually automate the application and infrastructure lifecycle.

Target workflow:

```text
Developer
    │
    ▼
Git Push / Pull Request
    │
    ▼
GitHub Actions
    │
    ├── Unit Tests
    ├── Linting
    ├── Security Checks
    ├── Container Build
    ├── Terraform Format
    ├── Terraform Validate
    └── Terraform Plan
            │
            ▼
        Review / Merge
            │
            ▼
        Deployment
            │
            ▼
           GCP
```

Production deployment controls will be added as the project evolves.

---

# 📊 Observability

The platform will implement monitoring and logging for the major components.

## Cloud Run

Planned metrics:

* Request count
* Request latency
* HTTP 4xx
* HTTP 5xx
* CPU utilization
* Memory utilization
* Instance count

## Cloud SQL

Planned metrics:

* CPU utilization
* Memory utilization
* Database connections
* Storage utilization
* Database performance

## Load Balancer

Planned monitoring:

* Request count
* Backend health
* HTTP errors
* Latency

## Application

Planned monitoring:

* Application errors
* Request latency
* Business-level failures

---

# 🚨 Failure Engineering & Troubleshooting

A major objective of this project is to demonstrate troubleshooting rather than only successful deployments.

Planned failure scenarios include:

### Scenario 1 — Cloud Run → Cloud SQL 403

Investigate:

```text
Cloud Run
    ↓
Runtime Service Account
    ↓
IAM Binding
    ↓
Cloud SQL permissions
```

### Scenario 2 — Cloud Run → Cloud SQL Connection Refused

Investigate:

```text
Application
    ↓
Cloud Run
    ↓
VPC
    ↓
Private Connectivity
    ↓
Cloud SQL
```

### Scenario 3 — Too Many Database Connections

Investigate:

```text
Cloud Run instances
        ×
Connections per instance
        =
Database connections
```

### Scenario 4 — Cloud Run 503

Investigate:

```text
Load Balancer
      ↓
Cloud Run
      ↓
Revision
      ↓
Container
      ↓
Application
      ↓
Dependencies
```

### Scenario 5 — Storage Upload Failure

Investigate:

```text
Application
    ↓
Service Account
    ↓
IAM
    ↓
Signed URL
    ↓
Cloud Storage
```

### Scenario 6 — Bad Deployment

Demonstrate:

```text
v1 → healthy

v2 → unhealthy

Traffic:
95% → v1
 5% → v2

        ↓

Detect issue

        ↓

Rollback
```

Each troubleshooting scenario will document:

```text
Symptom
   ↓
Possible Causes
   ↓
Investigation
   ↓
Root Cause
   ↓
Resolution
   ↓
Prevention
```

---

# 📚 Project Documentation

Documentation will be maintained under:

```text
docs/
│
├── architecture/
├── networking/
├── security/
├── deployment/
├── monitoring/
├── troubleshooting/
└── disaster-recovery/
```

The documentation will explain not only **how** something was implemented, but also **why** the architectural decision was made.

---

# 🗺️ Project Roadmap

## Phase 1 — Architecture

* [x] Define project objective
* [x] Define target architecture
* [x] Define major GCP components
* [ ] Create GitHub repository
* [ ] Create repository structure

## Phase 2 — Application

* [ ] Create REST API
* [ ] Add health endpoint
* [ ] Add product APIs
* [ ] Add order APIs
* [ ] Add upload functionality
* [ ] Add unit tests
* [ ] Containerize application

## Phase 3 — GCP Foundation

* [ ] Create GCP project structure
* [ ] Configure APIs
* [ ] Create VPC
* [ ] Configure subnet
* [ ] Configure IAM
* [ ] Configure service accounts

## Phase 4 — Cloud Run

* [ ] Deploy application
* [ ] Configure runtime service account
* [ ] Configure autoscaling
* [ ] Configure concurrency
* [ ] Configure revisions
* [ ] Implement traffic splitting
* [ ] Test rollback

## Phase 5 — Cloud SQL

* [ ] Create PostgreSQL instance
* [ ] Configure private connectivity
* [ ] Configure HA
* [ ] Configure backups
* [ ] Configure PITR
* [ ] Connect Cloud Run
* [ ] Test database failure scenarios

## Phase 6 — Cloud Storage

* [ ] Create private bucket
* [ ] Configure IAM
* [ ] Implement signed URLs
* [ ] Test upload/download
* [ ] Document security model

## Phase 7 — HTTPS & Networking

* [ ] Configure Cloud DNS
* [ ] Configure HTTPS Load Balancer
* [ ] Configure Cloud Run backend
* [ ] Configure TLS
* [ ] Test routing
* [ ] Document traffic flow

## Phase 8 — Terraform

* [ ] Create Terraform modules
* [ ] Create dev environment
* [ ] Create staging environment
* [ ] Create production structure
* [ ] Configure remote state
* [ ] Implement reusable infrastructure

## Phase 9 — CI/CD

* [ ] GitHub Actions CI
* [ ] Automated tests
* [ ] Container build
* [ ] Terraform validation
* [ ] Terraform plan
* [ ] Automated deployment
* [ ] Deployment strategy
* [ ] Rollback strategy

## Phase 10 — Observability

* [ ] Cloud Run dashboard
* [ ] Cloud SQL dashboard
* [ ] Load Balancer monitoring
* [ ] Application logging
* [ ] Alert policies
* [ ] SLO/SLI exploration

## Phase 11 — Failure Engineering

* [ ] IAM failure
* [ ] Network failure
* [ ] Database failure
* [ ] Connection exhaustion
* [ ] Application failure
* [ ] Deployment failure
* [ ] Load Balancer failure

## Phase 12 — Kubernetes Evolution

The same application will eventually be evaluated on GKE.

```text
Cloud Run
    │
    │
    ├──────────────┐
    │              │
    ▼              ▼
Serverless       GKE
Deployment      Deployment
```

The goal is to compare:

* Cloud Run vs GKE
* Operational overhead
* Scaling
* Networking
* Deployment
* Security
* Observability
* Cost considerations

## Phase 13 — AI / Agentic DevOps

Future evolution:

```text
                 CloudOps Platform
                        │
                        ▼
                ┌───────────────┐
                │ AI Incident   │
                │    Agent      │
                └───────┬───────┘
                        │
             ┌──────────┼──────────┐
             ▼          ▼          ▼
          Logs       Metrics    Incidents
             │          │          │
             └──────────┼──────────┘
                        ▼
                 Incident Analysis
                        │
                        ▼
                  Suggested RCA
```

This phase will be added after the core platform is stable.

---

# 🧠 Skills Demonstrated

By the end of the project, the repository is intended to demonstrate practical experience in:

### GCP

* Cloud Run
* Cloud SQL
* Cloud Storage
* VPC
* IAM
* Cloud DNS
* Load Balancing
* Secret Manager
* Cloud Monitoring
* Cloud Logging

### DevOps

* CI/CD
* GitHub Actions
* Infrastructure as Code
* Terraform
* Containerization
* Automated testing
* Deployment strategies
* Rollbacks

### Cloud Engineering

* Network design
* Security architecture
* Least privilege
* Private connectivity
* Managed services
* High availability
* Observability
* Troubleshooting

### Future

* Kubernetes / GKE
* AI agents
* AI-assisted incident management

---

# 🎓 Interview Perspective

This project is designed to support discussions such as:

> Why Cloud Run instead of GKE?

> Why Cloud SQL instead of PostgreSQL on a VM?

> Why private IP for Cloud SQL?

> How does Cloud Run access a private database?

> How would you secure the Cloud Run service?

> How do you handle application secrets?

> How would you perform a zero/minimal-impact deployment?

> How would you roll back a bad revision?

> What happens when Cloud Run scales to many instances?

> How can Cloud Run cause database connection exhaustion?

> How would you troubleshoot a 503?

> How would you troubleshoot a 403?

> How is the infrastructure managed across environments?

> How does your CI/CD pipeline work?

> How do you monitor the application?

> What would make you choose GKE over Cloud Run?

The answers to these questions will be backed by the implementation in this repository rather than being purely theoretical.

---

# 📌 Project Status

**Current Phase:** Phase 1 — Architecture Design

**Status:** 🟡 Architecture defined

**Next Step:** Repository structure and application design

---

# ⚠️ Security Notice

Never commit the following to this repository:

```text
❌ API keys
❌ Passwords
❌ Service-account JSON keys
❌ Private certificates
❌ Production credentials
❌ .env files containing secrets
❌ Terraform state containing sensitive information
```

Secrets and credentials must be managed through appropriate secret-management and CI/CD mechanisms.

---

# 📜 License

This project is intended primarily as a personal cloud engineering and DevOps portfolio project.

