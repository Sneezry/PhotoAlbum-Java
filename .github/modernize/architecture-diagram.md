# Architecture Diagram

A Spring Boot 2.7 photo gallery application with Oracle Database BLOB storage, serving photo uploads and retrieval via Thymeleaf-rendered web pages.

## Application Architecture

```mermaid
flowchart TD
    subgraph Client["Client Layer"]
        Browser["Web Browser"]
    end
    subgraph App["Application Layer - Spring Boot 2.7 / Java 8"]
        Web["Spring MVC Controllers"]
        Thymeleaf["Thymeleaf Templates"]
        Service["Business Services"]
    end
    subgraph Data["Data Layer"]
        JPA["Spring Data JPA"]
        DB[("Oracle Database Free\nBLOB Storage")]
    end
    subgraph Infra["Infrastructure"]
        Docker["Docker / Docker Compose"]
    end

    Browser -->|"HTTP requests"| Web
    Web -->|"renders views"| Thymeleaf
    Web -->|"delegates"| Service
    Service -->|"CRUD operations"| JPA
    JPA -->|"SQL queries + BLOB"| DB
    Docker -->|"hosts"| App
    Docker -->|"hosts"| DB
```

## Component Relationships

```mermaid
flowchart LR
    subgraph Presentation["Presentation"]
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
    end
    subgraph Model["Model"]
        PhotoEntity["Photo (Entity)"]
        UploadResult["UploadResult"]
    end

    HomeCtrl -->|"delegates upload/list"| PhotoSvc
    DetailCtrl -->|"delegates view/delete"| PhotoSvc
    PhotoFileCtrl -->|"delegates file serve"| PhotoSvc
    PhotoSvc -->|"implemented by"| PhotoSvcImpl
    PhotoSvcImpl -->|"queries"| PhotoRepo
    PhotoSvcImpl -->|"uses"| MathUtil
    PhotoRepo -->|"maps to"| PhotoEntity
    PhotoSvcImpl -->|"returns"| UploadResult
```
