# Architecture Diagram

A Spring Boot 2.7 Java web application for photo storage and gallery management, using Oracle Database for BLOB storage and Thymeleaf for server-side rendering.

## Application Architecture

```mermaid
flowchart TD
    subgraph Client["Client Layer"]
        Browser["Web Browser"]
    end
    subgraph App["Application Layer - Spring Boot 2.7 / Java 8"]
        Web["Spring MVC Controllers"]
        Tmpl["Thymeleaf Templates"]
        Service["Business Services"]
    end
    subgraph Data["Data Layer"]
        JPA["Spring Data JPA"]
        DB[("Oracle Database Free 23ai")]
    end
    subgraph Container["Container Infrastructure"]
        DockerApp["photoalbum-java-app"]
        DockerDB["photoalbum-oracle"]
        Network["photoalbum-network (bridge)"]
    end

    Browser -->|"HTTP requests"| Web
    Web -->|"renders"| Tmpl
    Tmpl -->|"HTML responses"| Browser
    Web -->|"delegates"| Service
    Service -->|"CRUD operations"| JPA
    JPA -->|"JDBC / SQL queries"| DB
    DockerApp -->|"container"| App
    DockerDB -->|"container"| DB
    DockerApp -.->|"bridge network"| Network
    DockerDB -.->|"bridge network"| Network
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
    end
    subgraph Domain["Domain Model"]
        PhotoEntity["Photo (Entity)"]
        UploadResult["UploadResult"]
    end

    HomeCtrl -->|"upload / list"| PhotoSvc
    DetailCtrl -->|"view / delete"| PhotoSvc
    PhotoFileCtrl -->|"serve BLOB"| PhotoSvc
    PhotoSvc -->|"implemented by"| PhotoSvcImpl
    PhotoSvcImpl -->|"queries"| PhotoRepo
    PhotoSvcImpl -->|"uses"| MathUtil
    PhotoRepo -->|"persists"| PhotoEntity
    PhotoSvcImpl -->|"returns"| UploadResult
    HomeCtrl -->|"uses"| UploadResult
```
