# Project Facts: Photo Album

> Consolidated assessment report generated from 36 automated fact analyses.

---

## Application Identity

| Fact | Value | Confidence |
|------|-------|------------|
| **Application Name** | Photo Album | High |
| **Artifact ID** | photo-album (com.photoalbum) | High |
| **Version** | 1.0.0 (release) | High |
| **Application Type** | Web Application + REST API (Spring MVC + Thymeleaf SSR) | High |
| **Architecture Pattern** | Layered Monolith with MVC (Controller / Service / Repository) | High |
| **License** | MIT License — Copyright Microsoft Corporation | High |

---

## Runtime & Build

| Fact | Value | Confidence |
|------|-------|------------|
| **Runtime** | Java 8 (JRE) — Eclipse Temurin (Adoptium) | High |
| **Framework** | Spring Boot 2.7.18 | High |
| **Build Tool** | Maven 3.9.6 | High |
| **Servlet Container** | Embedded Apache Tomcat 9.x (Servlet 4.0 / Java EE 8) | High |
| **Packaging** | Executable JAR (Spring Boot fat JAR) | High |
| **Startup** | `java $JAVA_OPTS -jar app.jar` | High |

---

## Dependencies

| Fact | Value | Confidence |
|------|-------|------------|
| **Language Dependencies** | 8 runtime dependencies via pom.xml (Spring Boot BOM 2.7.18) | High |
| **Key Runtime Deps** | spring-boot-starter-web, spring-boot-starter-thymeleaf, spring-boot-starter-data-jpa, ojdbc8, spring-boot-starter-validation, commons-io:2.11.0, spring-boot-starter-json, spring-boot-devtools (optional) | High |
| **Test Dependencies** | spring-boot-starter-test (JUnit 5 + Mockito), H2 | High |
| **XML Config Files** | None — annotation-based Spring Boot configuration only | High |
| **Embedded Language** | None — pure Java; client-side JS served to browser only | High |

---

## Containerization

| Fact | Value | Confidence |
|------|-------|------------|
| **Container Engine** | Docker with Docker Compose v2 | High |
| **Container Version** | Docker Compose v2+ (modern format, no version key); Docker Desktop required | Medium |
| **Base Image (build)** | `maven:3.9.6-eclipse-temurin-8` | High |
| **Base Image (runtime)** | `eclipse-temurin:8-jre` (Debian-based) | High |
| **Multi-stage Build** | Yes — 2 stages; build stage discarded | High |
| **Image Size (est.)** | ~280–350 MB (eclipse-temurin:8-jre ~220 MB + fat JAR ~60–100 MB) | Medium |
| **Image Layers (est.)** | 8–10 final layers | High |
| **System Packages** | None installed — relies on base image packages | High |
| **Operating System** | Debian Linux (container); cross-platform host (Docker Desktop) | High |

---

## Orchestration & Networking

| Fact | Value | Confidence |
|------|-------|------------|
| **Orchestration Tool** | Docker Compose (v2 format) — 2 services | High |
| **Service Definitions** | docker-compose.yml: oracle-db + photoalbum-java-app | High |
| **Network** | Custom bridge network `photoalbum-network` | High |
| **Application Port** | 8080 (HTTP) — host and container | High |
| **DB Port** | 1521 (Oracle, exposed to host) | High |
| **Volume Mounts** | `oracle_data:/opt/oracle/oradata` (named), `./oracle-init:/container-entrypoint-initdb.d` (bind mount) | High |
| **Resource Limits** | None configured (container-level); JVM: `-Xmx512m -Xms256m` | High |

---

## Configuration & Profiles

| Fact | Value | Confidence |
|------|-------|------------|
| **Spring Profiles** | 3 profiles: `default`, `docker`, `test` | High |
| **Profile Activation** | `SPRING_PROFILES_ACTIVE=docker` via docker-compose env var | High |
| **Environment Variables** | 8 total: JAVA_OPTS, SPRING_PROFILES_ACTIVE, SPRING_DATASOURCE_URL/USERNAME/PASSWORD, ORACLE_PASSWORD, APP_USER, APP_USER_PASSWORD | High |
| **Secret Management** | Passwords hardcoded in docker-compose.yml (no .env or secret manager) | High |

---

## Communication & External Services

| Fact | Value | Confidence |
|------|-------|------------|
| **Communication Protocols** | HTTP/REST (port 8080) + JDBC/TCP to Oracle (port 1521) | High |
| **External Services** | Oracle Database Free 23ai (`gvenzl/oracle-free:latest`) | High |
| **External Dependencies** | Oracle Database only — no Redis, Kafka, RabbitMQ, LDAP, S3, or other systems | High |
| **Storage Model** | Photos stored as BLOBs in Oracle DB (no filesystem/object storage) | High |

---

## Observability & Operations

| Fact | Value | Confidence |
|------|-------|------------|
| **Logging** | SLF4J + Logback (Spring Boot default); DEBUG level (dev), INFO level (docker) | High |
| **Telemetry / APM** | None configured | High |
| **AOP** | None | High |
| **Health Checks** | Oracle DB only (`healthcheck.sh`, 30s interval, 15 retries, 180s start); app container has no health check | High |
| **Testing Framework** | JUnit 5 (Jupiter) + Spring Boot Test; 1 smoke test; H2 in-memory DB for test isolation | High |
| **Startup Instrumentation** | Standard SLF4J logger initialization in 4 Java classes | High |

---

## Security

| Fact | Value | Confidence |
|------|-------|------------|
| **Authentication** | None | High |
| **Authorization** | None | High |
| **Transport Security** | HTTP only (no HTTPS/TLS) | High |
| **Encryption at Rest** | None | High |
| **Security Headers** | None configured (no CORS, CSP, HSTS) | High |
| **Input Validation** | File upload: MIME type whitelist (JPEG, PNG, GIF, WebP) + 10 MB size limit | High |
| **Secret Risk** | DB passwords hardcoded in docker-compose.yml | High |

---

## Data & Compliance

| Fact | Value | Confidence |
|------|-------|------------|
| **Data Classification** | Public/Internal — photo images (BLOB) + metadata (filename, size, dimensions, timestamp) | Medium |
| **PII / PHI / PCI** | None detected | Medium |
| **Compliance Requirements** | None (no GDPR, HIPAA, PCI-DSS, SOX indicators) | Medium |
| **Licensing Risk** | Oracle JDBC (ojdbc8) — Oracle Technology Network License requires acceptance for commercial use | High |

---

## Infrastructure & Hardware

| Fact | Value | Confidence |
|------|-------|------------|
| **Hardware Requirements** | Host: ≥4 GB RAM; Oracle Free/XE: max 2 CPU threads, 2 GB RAM, 12 GB storage | High |
| **JVM Heap** | Initial: 256 MB; Max: 512 MB (via `JAVA_OPTS`) | High |

---

## Summary

**Photo Album** is a **Spring Boot 2.7.18 Java 8 web application** serving as a photo gallery with upload, view, and delete functionality. It uses a **layered monolith** (MVC) architecture with Thymeleaf server-side rendering and REST endpoints for photo upload. Photos are stored as **BLOBs in Oracle Database Free (23ai)** — no file system or object storage is used.

The application is containerized using **Docker with Docker Compose** (multi-stage build), exposing port 8080. It has **no authentication, no HTTPS, and no health check** on the application container. Secrets are hardcoded in docker-compose.yml.

Key modernization considerations:
- **Java 8 → Java 17/21 upgrade** (Java 8 is End of Life)
- **Spring Boot 2.7 → 3.x upgrade** (`javax.*` → `jakarta.*` namespace migration required)
- **Oracle DB → managed cloud database** (e.g., Azure Database for PostgreSQL / Azure SQL)
- **BLOB storage → Azure Blob Storage** (photos should not be stored in the DB)
- **Security hardening**: add authentication, HTTPS, secret management, health checks
- **Container registry + Kubernetes or Azure Container Apps** for production orchestration
