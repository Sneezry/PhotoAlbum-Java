# Architecture Diagram

A Spring Boot 2.7 web application that stores and serves photos using Oracle Database BLOB storage, with Thymeleaf-based server-side rendering and a RESTful upload API.

## Application Architecture

```mermaid
flowchart TD
    subgraph Client["Client Layer"]
        Browser["Web Browser"]
    end
    subgraph App["Application Layer - Spring Boot 2.7 / Java 8"]
        Web["Spring MVC + Thymeleaf"]
        Service["Business Services"]
        FileHandler["File Upload Handler"]
    end
    subgraph Data["Data Layer"]
        JPA["Spring Data JPA"]
        DB[("Oracle Database Free\n(BLOB storage)")]
    end
    subgraph Infra["Infrastructure"]
        Docker["Docker / Docker Compose"]
    end

    Browser -->|"HTTP GET /photo/id"| Web
    Browser -->|"HTTP POST /upload"| FileHandler
    Web -->|"delegates"| Service
    FileHandler -->|"validates and saves"| Service
    Service -->|"CRUD operations"| JPA
    JPA -->|"SQL + BLOB queries"| DB
    Docker -->|"hosts"| App
    Docker -->|"hosts"| DB
```

## Component Relationships

```mermaid
flowchart LR
    subgraph Presentation["Presentation Layer"]
        HomeCtrl["HomeController"]
        DetailCtrl["DetailController"]
        PhotoFileCtrl["PhotoFileController"]
    end
    subgraph Business["Business Logic"]
        PhotoSvc["PhotoService (interface)"]
        PhotoSvcImpl["PhotoServiceImpl"]
        MathUtil["MathUtil"]
    end
    subgraph DataAccess["Data Access"]
        PhotoRepo["PhotoRepository"]
        PhotoEntity["Photo (Entity)"]
        UploadResult["UploadResult (Model)"]
    end

    HomeCtrl -->|"upload / list photos"| PhotoSvc
    DetailCtrl -->|"get / delete photo"| PhotoSvc
    PhotoFileCtrl -->|"serve photo bytes"| PhotoSvc
    PhotoSvc -->|"implemented by"| PhotoSvcImpl
    PhotoSvcImpl -->|"uses"| MathUtil
    PhotoSvcImpl -->|"queries"| PhotoRepo
    PhotoRepo -->|"maps to"| PhotoEntity
    PhotoSvcImpl -->|"returns"| UploadResult
```
