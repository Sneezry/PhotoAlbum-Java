# Dependency Map

This diagram shows the external dependencies of the Photo Album application as defined in `pom.xml`.

```mermaid
graph LR
    App["📦 photo-album\n1.0.0"]

    subgraph SpringBoot["Spring Boot 2.7.18 (BOM)"]
        SBWeb["spring-boot-starter-web\n• Tomcat (embedded)\n• Spring MVC\n• Jackson JSON"]
        SBThymeleaf["spring-boot-starter-thymeleaf\n• Thymeleaf 3\n• Thymeleaf Spring5"]
        SBDataJPA["spring-boot-starter-data-jpa\n• Spring Data JPA\n• Hibernate 5.6\n• HikariCP (connection pool)"]
        SBValidation["spring-boot-starter-validation\n• Hibernate Validator\n• Jakarta Bean Validation API"]
        SBJson["spring-boot-starter-json\n• Jackson Databind\n• Jackson Datatype JSR310"]
        SBDevTools["spring-boot-devtools\n(optional / dev only)"]
        SBTest["spring-boot-starter-test\n• JUnit 5\n• Mockito\n• AssertJ\n(test scope)"]
    end

    subgraph Oracle["Oracle / Database"]
        OracleJDBC["ojdbc8\n(com.oracle.database.jdbc)\nruntime scope"]
    end

    subgraph ApacheCommons["Apache Commons"]
        CommonsIO["commons-io 2.11.0\nFile I/O utilities"]
    end

    subgraph Testing["Testing (test scope)"]
        H2DB["h2\n(H2 in-memory database)\ntest scope"]
    end

    App --> SBWeb
    App --> SBThymeleaf
    App --> SBDataJPA
    App --> SBValidation
    App --> SBJson
    App --> SBDevTools
    App --> SBTest
    App --> OracleJDBC
    App --> CommonsIO
    App --> H2DB

    style App fill:#4caf50,color:#fff,stroke:#388e3c
    style SpringBoot fill:#e8f5e9,stroke:#2e7d32
    style Oracle fill:#fff3e0,stroke:#e65100
    style ApacheCommons fill:#e3f2fd,stroke:#1565c0
    style Testing fill:#f3e5f5,stroke:#6a1b9a
```

## Dependency Details

### Runtime Dependencies

| Group | Artifact | Version | Scope | Notes |
|-------|----------|---------|-------|-------|
| `org.springframework.boot` | `spring-boot-starter-parent` | 2.7.18 | parent BOM | ⚠️ EOL Nov 2023 |
| `org.springframework.boot` | `spring-boot-starter-web` | (managed) | compile | Embedded Tomcat |
| `org.springframework.boot` | `spring-boot-starter-thymeleaf` | (managed) | compile | Server-side templating |
| `org.springframework.boot` | `spring-boot-starter-data-jpa` | (managed) | compile | JPA / Hibernate ORM |
| `org.springframework.boot` | `spring-boot-starter-validation` | (managed) | compile | Bean Validation |
| `org.springframework.boot` | `spring-boot-starter-json` | (managed) | compile | Jackson JSON |
| `com.oracle.database.jdbc` | `ojdbc8` | (managed) | runtime | ⚠️ Oracle proprietary JDBC |
| `commons-io` | `commons-io` | 2.11.0 | compile | Apache Commons I/O |

### Non-Production Dependencies

| Group | Artifact | Version | Scope | Notes |
|-------|----------|---------|-------|-------|
| `org.springframework.boot` | `spring-boot-devtools` | (managed) | optional | Dev live reload |
| `org.springframework.boot` | `spring-boot-starter-test` | (managed) | test | JUnit 5, Mockito |
| `com.h2database` | `h2` | (managed) | test | In-memory DB for tests |

## Key Dependency Concerns for Cloud Migration

| Concern | Dependency | Recommendation |
|---------|-----------|---------------|
| ⛔ **Java EE namespace** | `javax.persistence`, `javax.validation` | Upgrade to Spring Boot 3.x (uses Jakarta EE 10) |
| ⛔ **Spring Boot EOL** | `spring-boot-starter-parent:2.7.18` | Upgrade to Spring Boot 3.3.x or 3.4.x |
| ⛔ **Oracle proprietary driver** | `ojdbc8` | Migrate to PostgreSQL (`org.postgresql:postgresql`) or Azure SQL |
| ⚠️ **Binary BLOB storage** | Embedded in JPA/Hibernate | Consider Azure Blob Storage SDK (`azure-storage-blob`) |
| ✅ **Embedded web server** | Tomcat (via starter-web) | Good for containerized deployment |
| ✅ **Connection pooling** | HikariCP (via starter-data-jpa) | Good for cloud deployments |
| ✅ **Container-ready** | Docker + multi-stage build | Ready for AKS/ACA deployment |
