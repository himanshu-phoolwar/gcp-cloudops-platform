# CloudOps API

CloudOps API is a containerized REST API built with **Python, FastAPI, and PostgreSQL**. It is the application workload for the `gcp-cloudops-platform` project.

The application is intentionally designed to be **cloud-ready and stateless**, so it can run locally using Docker Compose and later be deployed to **Google Cloud Run** with **Cloud SQL for PostgreSQL** and **Cloud Storage**.

---

## 1. Application Purpose

The purpose of this application is to provide a realistic backend workload that can demonstrate:

* REST API development
* PostgreSQL database integration
* Containerization
* Configuration through environment variables
* Health checks
* Automated testing
* Cloud Run deployment
* Cloud SQL connectivity
* Cloud Storage integration
* Authentication and authorization
* Observability
* CI/CD
* Failure troubleshooting

The application is not intended to be a complex business application.

Instead, it provides a realistic workload on which we can demonstrate **DevOps, GCP, Kubernetes, Terraform, CI/CD, security, observability, and AI-driven operations**.

---

# 2. Technology Stack

| Component           | Technology               |
| ------------------- | ------------------------ |
| Language            | Python 3.12              |
| API Framework       | FastAPI                  |
| API Server          | Uvicorn                  |
| Database            | PostgreSQL               |
| ORM                 | SQLAlchemy               |
| Database Driver     | psycopg                  |
| Validation          | Pydantic                 |
| Testing             | Pytest                   |
| Containerization    | Docker                   |
| Local Environment   | Docker Compose           |
| Production Runtime  | Google Cloud Run         |
| Production Database | Cloud SQL for PostgreSQL |
| Object Storage      | Google Cloud Storage     |

---

# 3. Application Architecture

## Local Development

```text
                    Developer
                        |
                        v
                 FastAPI Application
                        |
                +-------+-------+
                |               |
                v               v
            PostgreSQL      Local Storage
```

The local environment uses Docker Compose to run:

```text
+----------------------------+
|        Docker Compose      |
|                            |
|  +----------------------+  |
|  |   CloudOps API       |  |
|  |   FastAPI            |  |
|  +----------+-----------+  |
|             |              |
|             v              |
|  +----------------------+  |
|  |   PostgreSQL         |  |
|  +----------------------+  |
|                            |
+----------------------------+
```

---

# 4. Production Architecture

The application will eventually run on Google Cloud:

```text
                    Internet
                       |
                       v
             External HTTPS Load Balancer
                       |
                       v
                  Cloud Run
                       |
          +------------+------------+
          |            |            |
          v            v            v
       Cloud SQL   Secret Manager  Cloud Storage
       PostgreSQL
          ^
          |
     Private IP
          |
         VPC
```

Cloud Run will connect to Cloud SQL using **private connectivity** through the VPC.

Application secrets will be stored in **Secret Manager** rather than being hardcoded in the application.

Cloud Storage will be used for object/file-related operations.

---

# 5. API Endpoints

The initial application exposes the following APIs.

## Health

### `GET /health`

Used by:

* Docker health checks
* Cloud Run health/readiness validation
* Load balancer health checks where applicable
* Monitoring
* Deployment validation

Example response:

```json
{
  "status": "healthy"
}
```

---

## Products

### `GET /products`

Returns available products.

Example:

```json
[
  {
    "id": 1,
    "name": "Laptop",
    "description": "Developer laptop",
    "price": 75000,
    "stock": 10
  }
]
```

---

### `GET /products/{id}`

Returns a specific product.

Example:

```text
GET /products/1
```

---

### `POST /products`

Creates a new product.

Example request:

```json
{
  "name": "Laptop",
  "description": "Developer laptop",
  "price": 75000,
  "stock": 10
}
```

---

# 6. Orders

### `POST /orders`

Creates an order for a product.

Example:

```json
{
  "product_id": 1,
  "quantity": 2
}
```

The application will validate:

* Product exists
* Requested quantity is valid
* Sufficient stock is available

---

### `GET /orders/{id}`

Returns an order.

Example:

```text
GET /orders/1001
```

Example response:

```json
{
  "id": 1001,
  "product_id": 1,
  "quantity": 2,
  "status": "created"
}
```

---

# 7. Database Design

The initial database contains two primary tables.

## Products

```text
products
--------------------------------
id
name
description
price
stock
created_at
```

## Orders

```text
orders
--------------------------------
id
product_id
quantity
status
created_at
```

Relationship:

```text
products
    |
    | 1
    |
    | *
    v
orders
```

One product can be associated with multiple orders.

---

# 8. Configuration

The application must not contain environment-specific configuration in source code.

Configuration is provided through environment variables.

Example:

```text
DATABASE_HOST
DATABASE_PORT
DATABASE_NAME
DATABASE_USER
DATABASE_PASSWORD
```

Local development may use:

```text
DATABASE_HOST=postgres
DATABASE_PORT=5432
DATABASE_NAME=cloudops
DATABASE_USER=cloudops
DATABASE_PASSWORD=<local-password>
```

Production configuration will be provided through Google Cloud services.

Sensitive values must **never** be committed to Git.

---

# 9. Containerization

The application is designed to run as a Docker container.

The container must:

* Listen on the port provided through the `PORT` environment variable
* Not depend on local persistent storage
* Read configuration from environment variables
* Start reliably
* Handle application shutdown gracefully
* Write logs to stdout/stderr

Example:

```text
Docker Container
      |
      v
  FastAPI
      |
      v
  Uvicorn
      |
      v
    $PORT
```

This design allows the same container image to run locally and on Cloud Run.

---

# 10. Local Development

## Prerequisites

Install:

* Python 3.12+
* Docker
* Docker Compose
* Git

Verify:

```bash
python --version
docker --version
docker compose version
git --version
```

---

# 11. Running the Application Locally

Clone the repository:

```bash
git clone <repository-url>
cd gcp-cloudops-platform/application
```

Start the application:

```bash
docker compose up --build
```

The API will be available at:

```text
http://localhost:8080
```

FastAPI Swagger documentation:

```text
http://localhost:8080/docs
```

Alternative API documentation:

```text
http://localhost:8080/redoc
```

---

# 12. Running Without Docker

Create a virtual environment:

```bash
python3.12 -m venv .venv
```

Activate it:

### Linux/macOS

```bash
source .venv/bin/activate
```

### Windows

```powershell
.venv\Scripts\activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Set the required environment variables and start the application:

```bash
uvicorn app.main:app --host 0.0.0.0 --port 8080
```

---

# 13. Testing

Tests are located under:

```text
tests/
```

Run tests:

```bash
pytest
```

The test suite will gradually expand to cover:

* Health endpoint
* Product APIs
* Order APIs
* Database operations
* Validation
* Error handling
* Integration tests

---

# 14. Error Handling

The application should return meaningful HTTP status codes.

| Scenario                  | HTTP Status |
| ------------------------- | ----------: |
| Successful request        |         200 |
| Resource created          |         201 |
| Invalid request           |         400 |
| Authentication failure    |         401 |
| Authorization failure     |         403 |
| Resource not found        |         404 |
| Database/service failure  |         500 |
| Temporary backend failure |         503 |

Example:

```text
GET /products/999
        |
        v
Product not found
        |
        v
HTTP 404
```

---

# 15. Logging

Application logs will be written to stdout/stderr.

Example:

```text
INFO  Request received
INFO  Product lookup
INFO  Database query completed
ERROR Database connection failed
```

This allows Cloud Run to automatically collect application logs through Google Cloud's logging infrastructure.

Future improvements will include:

* Structured JSON logging
* Request correlation IDs
* Error tracking
* Latency logging
* Database operation metrics

---

# 16. Security Principles

The application follows these principles:

### No hardcoded credentials

Credentials must never be stored in:

```text
Python source code
Dockerfile
Git repository
README
```

### Environment-based configuration

Configuration is injected at runtime.

### Least privilege

The production application will use a dedicated Cloud Run service account with only the permissions required by the application.

### No service-account JSON keys

The application will use Google Cloud identity mechanisms instead of long-lived service-account keys.

### Private database connectivity

Cloud SQL will use private connectivity rather than exposing the database publicly.

### Secret Manager

Production secrets will be stored in Google Secret Manager.

---

# 17. Cloud Run Design Requirements

The application is intentionally designed for Cloud Run.

The application must be:

* Stateless
* Containerized
* Horizontally scalable
* Environment-configurable
* Independent of local persistent storage
* Able to handle concurrent requests
* Gracefully startable and stoppable

The application must not depend on:

* Local filesystem persistence
* A permanently running process
* A fixed hostname
* A fixed IP address
* A single application instance

---

# 18. Database Connection Management

Cloud Run can create multiple application instances as traffic increases.

For example:

```text
                Cloud Run
                    |
       +------------+------------+
       |            |            |
       v            v            v
   Instance 1   Instance 2   Instance 3
       |            |            |
       +------------+------------+
                    |
                    v
              Cloud SQL
```

Each instance can create database connections.

Therefore, database connection pooling must be configured carefully to prevent:

```text
Too many database connections
```

The application will eventually demonstrate connection-pool tuning and Cloud Run concurrency management.

---

# 19. Observability

The application will eventually expose operational information required for:

* Availability monitoring
* Request latency monitoring
* Error-rate monitoring
* Database monitoring
* Cloud Run instance monitoring
* Alerting

Important operational signals include:

```text
Request count
Request latency
HTTP 4xx
HTTP 5xx
Container instances
CPU utilization
Memory utilization
Database connections
Database latency
```

---

# 20. Failure Engineering

A major goal of this project is to intentionally introduce and troubleshoot failures.

Examples:

### Cloud Run → Cloud SQL 403

Possible areas:

```text
Cloud Run Service Account
        |
        v
IAM permissions
        |
        v
Cloud SQL Client role
        |
        v
Organization Policy / IAM Deny
```

---

### Connection Refused

Investigation:

```text
Application
    |
    v
Cloud Run networking
    |
    v
Direct VPC Egress
    |
    v
VPC
    |
    v
Private IP
    |
    v
Cloud SQL
```

---

### Too Many Connections

Investigation:

```text
Cloud Run instances
        |
        v
Concurrency
        |
        v
Connections per instance
        |
        v
Cloud SQL connection limit
```

---

### Cloud Run 503

Investigation:

```text
Request
   |
   v
Cloud Run
   |
   +--> Revision
   |
   +--> Container startup
   |
   +--> PORT
   |
   +--> Application logs
   |
   +--> Database dependency
```

These troubleshooting scenarios will become part of the project's interview documentation.

---

# 21. Project Development Phases

## Phase 1 — Application Foundation

* [x] Application architecture
* [ ] FastAPI project
* [ ] Health endpoint
* [ ] Product API
* [ ] Order API
* [ ] PostgreSQL integration
* [ ] Unit tests
* [ ] Dockerfile
* [ ] Docker Compose

---

## Phase 2 — Production Application

* [ ] Database migrations
* [ ] Better error handling
* [ ] Structured logging
* [ ] Configuration management
* [ ] Authentication
* [ ] Authorization
* [ ] Cloud Storage integration
* [ ] Signed URLs

---

## Phase 3 — GCP Deployment

* [ ] Cloud Run
* [ ] Cloud SQL
* [ ] Secret Manager
* [ ] Cloud Storage
* [ ] VPC
* [ ] Direct VPC Egress
* [ ] External HTTPS Load Balancer
* [ ] Cloud DNS

---

## Phase 4 — Infrastructure as Code

* [ ] Terraform
* [ ] Terraform modules
* [ ] Dev environment
* [ ] Staging environment
* [ ] Production environment
* [ ] IAM
* [ ] Monitoring

---

## Phase 5 — CI/CD

```text
Developer
    |
    v
GitHub
    |
    v
GitHub Actions
    |
    +--> Tests
    |
    +--> Lint
    |
    +--> Security checks
    |
    +--> Docker build
    |
    +--> Terraform plan
    |
    v
Deployment
```

---

## Phase 6 — Kubernetes

The same application will eventually be deployed to GKE.

This allows a practical comparison between:

```text
Cloud Run
   vs
GKE
```

The comparison will focus on:

* Operational complexity
* Scaling
* Deployment
* Networking
* Security
* Cost considerations
* Observability
* Workload requirements

---

## Phase 7 — AI Incident Agent

The final evolution of the application will introduce an AI-powered incident response component.

Conceptually:

```text
Cloud Monitoring
       |
       v
Logs / Metrics / Alerts
       |
       v
AI Incident Agent
       |
       +--> Analyze
       |
       +--> Correlate
       |
       +--> Identify possible root cause
       |
       +--> Suggest remediation
       |
       v
Engineer
```

The agent will be designed as an **operational assistant**, not as an unrestricted autonomous production operator.

---

# 22. What This Application Demonstrates

This application provides a single realistic workload through which the following engineering capabilities can be demonstrated:

```text
Python
  +
FastAPI
  +
REST APIs
  +
PostgreSQL
  +
Docker
  +
Cloud Run
  +
Cloud SQL
  +
Cloud Storage
  +
VPC Networking
  +
IAM
  +
Secret Manager
  +
Terraform
  +
GitHub Actions
  +
Monitoring
  +
Troubleshooting
  +
GKE
  +
AI Agents
```

The goal is not to build a large application.

The goal is to demonstrate how a real application is **designed, containerized, deployed, secured, monitored, scaled, broken, troubleshot, and automated**.

---

# 23. Repository Structure

The application is part of the larger `gcp-cloudops-platform` repository.

```text
gcp-cloudops-platform/
│
├── application/
│   ├── app/
│   │   ├── main.py
│   │   ├── database.py
│   │   ├── models/
│   │   ├── schemas/
│   │   ├── routes/
│   │   └── services/
│   │
│   ├── tests/
│   │
│   ├── Dockerfile
│   ├── docker-compose.yml
│   ├── requirements.txt
│   └── README.md
│
├── terraform/
│
├── .github/
│   └── workflows/
│
└── README.md
```

The **root README** describes the complete cloud platform.

This README describes only the **application workload**.

---

# 24. Engineering Principle

The application follows the project's overall learning philosophy:

```text
             LEARN
               |
               v
             BUILD
               |
               v
             BREAK
               |
               v
              FIX
               |
               v
            DOCUMENT
               |
               v
            AUTOMATE
               |
               +--------> REPEAT
```

The objective is to gain practical engineering experience rather than simply deploying a sample application.

---

## Current Status

**Application:** CloudOps API

**Current phase:** Application development

**Deployment target:** Google Cloud Run

**Database target:** Cloud SQL for PostgreSQL

**Infrastructure:** Terraform

**CI/CD:** GitHub Actions

**Observability:** Google Cloud Monitoring + Logging

**Future platform:** GKE + AI Incident Agent
