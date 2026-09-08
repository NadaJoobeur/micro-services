# 🏫 Micro-Services — School Management System

A **Spring Boot / Spring Cloud** microservices architecture demonstrating service discovery, centralized configuration, API gateway routing, inter-service communication, and distributed tracing.

This project simulates a simplified school management system where **Schools** and **Students** are managed as independent, loosely-coupled services that communicate through a discovery-based, config-driven microservices ecosystem.

---

## 📐 Architecture

```
                        ┌─────────────────────┐
                        │   Config Server      │  (port 8888)
                        │  (Spring Cloud Config │
                        │   - native profile)   │
                        └──────────┬───────────┘
                                   │ configs
        ┌──────────────────────────┼──────────────────────────┐
        │                          │                          │
┌───────▼────────┐        ┌────────▼─────────┐       ┌────────▼─────────┐
│  Discovery       │◄──────┤     Gateway       │       │   School Service  │
│ (Eureka Server)  │       │ (Spring Cloud     │──────►│  (port 8070)      │
│  port 8761       │       │  Gateway WebMVC)  │       │  PostgreSQL       │
└─────────▲────────┘       │  port 8222        │       └─────────┬─────────┘
          │                └────────┬──────────┘                 │ Feign
          │                         │                             ▼
          │                         │                   ┌───────────────────┐
          └─────────────────────────┴──────────────────►│  Student Service  │
                                                          │  (port 8090)      │
                                                          │  PostgreSQL       │
                                                          └───────────────────┘
```

- **Config Server** centralizes all configuration files (`native` profile, served from the classpath).
- **Discovery Server (Eureka)** allows every service to register itself and discover others dynamically.
- **Gateway** is the single entry point, routing incoming requests to the correct downstream service based on path predicates.
- **School Service** and **Student Service** are independent domain services, each with its own PostgreSQL database.
- **School Service** calls **Student Service** via a declarative **Feign Client** to enrich a school's response with its list of students.
- **Distributed tracing** (Zipkin / Micrometer Tracing) is enabled to trace requests across services.

---

## 🛠️ Tech Stack

| Category                | Technology                                             |
|-------------------------|---------------------------------------------------------|
| Language                | Java 17                                                  |
| Framework               | Spring Boot 4.1.x                                        |
| Microservices Toolkit   | Spring Cloud 2025.1.x                                    |
| Service Discovery       | Netflix Eureka (Spring Cloud Netflix)                    |
| Centralized Config      | Spring Cloud Config Server (native profile)              |
| API Gateway             | Spring Cloud Gateway (WebMVC)                            |
| Inter-Service Calls     | Spring Cloud OpenFeign                                   |
| Persistence             | Spring Data JPA / Hibernate                              |
| Database                | PostgreSQL                                               |
| Distributed Tracing     | Micrometer Tracing + Zipkin                              |
| Boilerplate Reduction   | Lombok                                                   |
| Build Tool              | Maven (multi-module, with Maven Wrapper)                 |
| API Style               | RESTful APIs (JSON over HTTP)                            |

---

## 📦 Project Structure

```
micro-services/
├── config-server/     # Centralized configuration server (Spring Cloud Config)
├── discovery/         # Eureka service registry
├── gateway/            # Spring Cloud Gateway - single entry point / routing
├── school/             # School microservice (JPA, PostgreSQL, Feign client)
└── student/            # Student microservice (JPA, PostgreSQL)
```

Each module is an independent Maven project with its own `pom.xml`, `mvnw` wrapper, and Spring Boot application.

---

## 🚀 Services Overview

| Service         | Port | Responsibility                                                                 |
|-----------------|------|---------------------------------------------------------------------------------|
| Config Server   | 8888 | Serves centralized configuration files to all services                          |
| Discovery       | 8761 | Eureka service registry for service discovery                                   |
| Gateway         | 8222 | Routes `/api/v1/schools/**` and `/api/v1/students/**` to the right service       |
| School Service  | 8070 | Manages schools, aggregates student data via Feign                              |
| Student Service | 8090 | Manages students, exposes students filtered by school                           |

---

## 🔌 API Endpoints

### School Service (`/api/v1/schools`)
| Method | Endpoint                                | Description                                  |
|--------|------------------------------------------|-----------------------------------------------|
| POST   | `/api/v1/schools`                        | Create a new school                            |
| GET    | `/api/v1/schools`                        | List all schools                               |
| GET    | `/api/v1/schools/with-students/{school-id}` | Get a school with its list of students (via Feign call to Student Service) |

### Student Service (`/api/v1/students`)
| Method | Endpoint                                     | Description                          |
|--------|------------------------------------------------|-----------------------------------------|
| POST   | `/api/v1/students`                             | Create a new student                     |
| GET    | `/api/v1/students`                             | List all students                        |
| GET    | `/api/v1/students/school/{school-id}`          | List all students of a given school      |

---

## ⚙️ Getting Started

### Prerequisites
- Java 17+
- Maven 3.9+
- PostgreSQL running locally with two databases: `schools` and `students`

### Run order
Services must be started in the following order so that discovery and configuration are available before dependent services start:

```bash
# 1. Config Server
cd config-server && ./mvnw spring-boot:run

# 2. Discovery Server (Eureka)
cd discovery && ./mvnw spring-boot:run

# 3. Gateway
cd gateway && ./mvnw spring-boot:run

# 4. School Service
cd school && ./mvnw spring-boot:run

# 5. Student Service
cd student && ./mvnw spring-boot:run
```

Once started:
- Eureka dashboard: `http://localhost:8761`
- Config Server: `http://localhost:8888`
- All API calls go through the Gateway: `http://localhost:8222`

---

## 🔭 Possible Improvements

- Add centralized authentication/authorization (e.g., Spring Security + OAuth2/JWT)
- Add circuit breakers (Resilience4j) around Feign calls
- Containerize services with Docker & orchestrate with Docker Compose / Kubernetes
- Move configuration to a Git-backed Config Server profile instead of native/classpath
- Add integration tests (Testcontainers for PostgreSQL)
- Centralized API documentation (springdoc-openapi / Swagger UI)

---

## 👤 Author

**Nada Joobeur**
Built as a hands-on microservices learning project using Spring Boot & Spring Cloud.
