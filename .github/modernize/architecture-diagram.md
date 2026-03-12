# Architecture Diagram

## Application Architecture

```mermaid
graph TB
    subgraph Client["Client (Browser)"]
        Browser["Web Browser"]
    end

    subgraph App["Photo Album Application (Spring Boot 2.7 / Java 8)"]
        direction TB
        subgraph Controllers["Controllers Layer"]
            HC["HomeController\n(GET / , POST /upload)"]
            DC["DetailController\n(GET /detail/{id}, POST /detail/{id}/delete)"]
            PFC["PhotoFileController\n(GET /photo/{id})"]
        end

        subgraph Services["Service Layer"]
            PS["PhotoService (Interface)"]
            PSI["PhotoServiceImpl"]
        end

        subgraph Repositories["Repository Layer"]
            PR["PhotoRepository\n(JpaRepository)"]
        end

        subgraph Models["Domain Model"]
            P["Photo (JPA Entity)"]
            UR["UploadResult"]
            MU["MathUtil"]
        end

        subgraph Views["Thymeleaf Templates"]
            IDX["index.html\n(Gallery View)"]
            DET["detail.html\n(Single Photo View)"]
            LAY["layout.html\n(Base Layout)"]
        end

        subgraph Static["Static Resources"]
            CSS["site.css"]
            JS["upload.js"]
        end
    end

    subgraph Data["Data Layer"]
        ORA["Oracle Database\n(Oracle Database Free 23ai)"]
        BLOB["BLOB Column\n(photo_data)"]
    end

    Browser -->|HTTP GET/POST| HC
    Browser -->|HTTP GET/POST| DC
    Browser -->|HTTP GET| PFC

    HC --> PS
    DC --> PS
    PFC --> PS

    PS --> PSI
    PSI --> PR
    PR -->|JPA / Native SQL| ORA
    ORA --- BLOB

    HC --> IDX
    DC --> DET
    IDX --- LAY
    DET --- LAY

    P --- BLOB
```

## Deployment Architecture

```mermaid
graph LR
    subgraph Docker["Docker Compose Environment"]
        direction TB
        subgraph AppContainer["photoalbum-java-app\n(Container)"]
            JApp["Spring Boot App\n:8080"]
        end

        subgraph DBContainer["photoalbum-oracle\n(Container)"]
            OraDB["Oracle Database Free 23ai\n:1521"]
            OraData[("oracle_data\n(Volume)")]
        end

        OraDB --- OraData
    end

    subgraph Network["photoalbum-network (bridge)"]
        AppContainer -->|JDBC :1521| DBContainer
    end

    Internet["Internet / User"] -->|HTTP :8080| AppContainer
```

## Technology Stack

| Layer | Technology | Version |
|-------|-----------|---------|
| Language | Java | 8 |
| Framework | Spring Boot | 2.7.18 |
| Web | Spring MVC + Thymeleaf | 2.7.18 |
| Persistence | Spring Data JPA + Hibernate | 2.7.18 |
| Database | Oracle Database Free | 23ai |
| JDBC Driver | ojdbc8 | runtime |
| Build Tool | Maven | 3.9.6 |
| Container | Docker + Docker Compose | latest |
| Base Image (build) | eclipse-temurin:8 | JDK 8 |
| Base Image (runtime) | eclipse-temurin:8-jre | JRE 8 |
