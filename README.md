# PRN232 Lab 3 - gRPC & Microservices Architecture

Welcome to the Learning Management System (LMS) project for **PRN232 Lab 3 - gRPC & Microservices Architecture**.

This version refactors the monolithic Lab 2 application into a **Microservices Architecture** with service-to-service communication via **gRPC**, an **API Gateway** (YARP), independent databases per service, **Serilog** logging, JWT Authentication, and full Docker Compose deployment.

---

## Architecture Overview

```
Client
  │
  ▼
API Gateway (YARP)          ← Routes & validates JWT
  ├── /api/auth/*    → Identity Service
  ├── /api/students/* → Student Service
  └── /api/courses/*  → Course Service
                              │
                         gRPC │
                              ▼
                       Student Service
```

---

## Services

| Service | Port | Database | Responsibilities |
| :--- | :--- | :--- | :--- |
| **API Gateway** | `8080` | — | Route requests, JWT validation |
| **Identity Service** | `5001` | `identity-db` | Auth, JWT generation, Refresh Token |
| **Student Service** | `5002` | `student-db` | Student management, gRPC server |
| **Course Service** | `5003` | `course-db` | Course & enrollment management, gRPC client |

---

## Key Features in Lab 3

1. **Microservices Architecture**: System decomposed into 3 independent ASP.NET Core Web API services, each with its own database.
2. **gRPC Communication**: Course Service communicates with Student Service via gRPC to verify student existence before enrollment.
3. **API Gateway (YARP)**: Reverse proxy routing all client requests to the appropriate service with JWT validation.
4. **JWT Authentication**: Centralized in Identity Service; all other services validate tokens independently.
5. **Serilog Logging**: Structured logging across all services (Request Path, HTTP Method, Status Code, Execution Time).
6. **Docker Compose**: Full multi-container deployment with 7 containers (4 services + 3 databases).
7. **Swagger UI**: Each service exposes its own Swagger documentation with JWT Bearer support.

---

## Pre-requisites

1. **Docker Desktop** installed and running.
2. **Duplicate `.env.example`** → rename to `.env` and fill in values:
   ```env
   MSSQL_SA_PASSWORD=YourStrongPassword123!
   JWT_SECRET=YourSuperLongSecureJwtSecretKeyWithMinimum256BitsOfStrength!
   ```

---

## How to Run

```bash
docker compose up --build -d
```

This starts all 7 containers:
- `api-gateway` → port **8080**
- `identity-service` → port **5001**
- `student-service` → port **5002**
- `course-service` → port **5003**
- `identity-db`, `student-db`, `course-db` → SQL Server instances

---

## Testing Credentials

| Username | Password | Role |
| :--- | :--- | :--- |
| **admin** | `123456` | **Admin** |
| **student** | `123456` | **Student** |

---

## Key Testing Workflow

1. **Login**: `POST /api/auth/login` → copy `accessToken`
2. **Authorize**: Paste token into Swagger Authorize button
3. **Test gRPC flow**: `POST /api/courses/{courseId}/enrollments` → Course Service calls Student Service via gRPC to verify student
4. **Test protected endpoints**: Admin-only endpoints return `403 Forbidden` for Student role
5. **Test unauthorized**: Missing token returns `401 Unauthorized`

---

## Swagger UI (per service)

| Service | URL |
| :--- | :--- |
| API Gateway | http://localhost:8080/swagger |
| Identity Service | http://localhost:5001/swagger |
| Student Service | http://localhost:5002/swagger |
| Course Service | http://localhost:5003/swagger |

---

## Student ID: SE182723
