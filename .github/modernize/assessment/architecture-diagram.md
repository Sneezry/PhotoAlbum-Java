# Architecture Diagram

This diagram illustrates the high-level architecture of the Photo Album application and its deployment environment.

```mermaid
graph TB
    subgraph Client["Client Layer"]
        Browser["🌐 Web Browser"]
    end

    subgraph AppLayer["Application Layer (Spring Boot 2.7.18 / Java 8)"]
        direction TB
        HomeCtrl["HomeController\n(GET / , POST /upload)"]
        DetailCtrl["DetailController\n(GET /detail/{id}, POST /detail/{id}/delete)"]
        PhotoFileCtrl["PhotoFileController\n(GET /photo/{id})"]
        PhotoSvc["PhotoService\n(Business Logic)"]
        PhotoRepo["PhotoRepository\n(Spring Data JPA)"]
        ThymeleafViews["Thymeleaf Templates\n(index.html, detail.html)"]
    end

    subgraph DataLayer["Data Layer"]
        OracleDB[("🗄️ Oracle Database\n(FREEPDB1:1521)\n• photos table\n• BLOB photo data")]
    end

    subgraph ContainerInfra["Container Infrastructure (Docker Compose)"]
        AppContainer["📦 photo-album container\n(eclipse-temurin:8-jre)\nPort: 8080"]
        DBContainer["📦 oracle-db container\n(Oracle Free)\nPort: 1521"]
    end

    Browser -->|"HTTP GET /\nHTTP GET /detail/{id}"| HomeCtrl
    Browser -->|"HTTP POST /upload\n(multipart/form-data)"| HomeCtrl
    Browser -->|"HTTP GET /photo/{id}"| PhotoFileCtrl
    Browser -->|"HTTP DELETE /detail/{id}/delete"| DetailCtrl

    HomeCtrl --> PhotoSvc
    DetailCtrl --> PhotoSvc
    PhotoFileCtrl --> PhotoSvc
    PhotoSvc --> PhotoRepo
    PhotoRepo -->|"JDBC / Hibernate ORM\nnative Oracle SQL"| OracleDB

    HomeCtrl --> ThymeleafViews
    DetailCtrl --> ThymeleafViews

    AppContainer -.->|"runs"| AppLayer
    DBContainer -.->|"runs"| OracleDB

    style Client fill:#e3f2fd,stroke:#1565c0
    style AppLayer fill:#e8f5e9,stroke:#2e7d32
    style DataLayer fill:#fff3e0,stroke:#e65100
    style ContainerInfra fill:#f3e5f5,stroke:#6a1b9a
```

## Architecture Notes

| Component | Technology | Details |
|-----------|-----------|---------|
| **Runtime** | Java 8 (Eclipse Temurin) | EOL – upgrade to Java 17/21 recommended |
| **Framework** | Spring Boot 2.7.18 | EOL (Nov 2023) – upgrade to Spring Boot 3.x recommended |
| **Web Layer** | Spring MVC + Thymeleaf 3 | Server-side rendering |
| **Persistence** | Spring Data JPA + Hibernate | Uses Oracle dialect and native queries |
| **Database** | Oracle Database Free (21c) | Connected via JDBC at `oracle-db:1521/FREEPDB1` |
| **Photo Storage** | Oracle BLOB column | Binary data stored directly in the `photos` table |
| **Containerization** | Docker + Docker Compose | Multi-stage build, `maven:3.9.6` for build, `eclipse-temurin:8-jre` for runtime |

## Cloud Migration Targets

The application is assessed for migration to the following Azure services:

- **Azure App Service** – Managed PaaS for web applications
- **Azure Kubernetes Service (AKS)** – Container orchestration
- **Azure Container Apps** – Serverless container hosting
