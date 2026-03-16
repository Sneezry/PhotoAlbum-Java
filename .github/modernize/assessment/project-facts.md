# Project Facts: Photo Album

> **Generated**: 2026-03-16 | **Source**: 36 automated fact assessments

## Application Identity

| Fact | Finding | Confidence |
|------|---------|------------|
| **Application Name** | Photo Album | High |
| **Application Version** | 1.0.0 (Maven artifact `com.photoalbum:photo-album`) | High |
| **Application Type** | Web App with REST API (Spring MVC + Thymeleaf SSR + JSON upload endpoint) | High |
| **Architecture Pattern** | Layered Monolith with MVC (Controller → Service → Repository → Model) | High |
| **License** | MIT License — Copyright (c) Microsoft Corporation | High |

## Runtime & Framework

| Fact | Finding | Confidence |
|------|---------|------------|
| **Runtime Environment** | Java 8 (Eclipse Temurin JRE 8) | High |
| **Framework** | Spring Boot 2.7.18 | High |
| **Servlet Container** | Embedded Apache Tomcat 9.x (Servlet API 4.0 / Java EE 8) | High |
| **Build Tool** | Maven 3.9.6 (executed in Docker build stage) | High |
| **Language Dependencies** | 10 declared Maven dependencies (8 runtime, 2 test-scope) — packaged as fat JAR | High |

## Container & Deployment

| Fact | Finding | Confidence |
|------|---------|------------|
| **Container Engine** | Docker with Docker Compose v2 | High |
| **Container Version** | Docker Compose Specification format (v2+); engine version not pinned | Medium |
| **Base Image** | Build: `maven:3.9.6-eclipse-temurin-8`; Runtime: `eclipse-temurin:8-jre` (Debian) | High |
| **Multi-stage Build** | ✅ Yes — 2 stages (build + runtime); Maven toolchain excluded from final image | High |
| **Image Layers** | ~9–11 layers in final image (1 COPY + base layers) | High |
| **Estimated Image Size** | 300–340 MB (eclipse-temurin:8-jre ~220 MB + Spring Boot fat JAR ~80–120 MB) | Medium |
| **Orchestration Tool** | Docker Compose (local development only); no Kubernetes or Swarm | High |
| **Service Definition** | 2 services: `oracle-db` + `photoalbum-java-app`; dependency gating via health check | High |

## Infrastructure & Networking

| Fact | Finding | Confidence |
|------|---------|------------|
| **Application Port** | `8080` (HTTP); Oracle DB `1521` (JDBC) | High |
| **Network Settings** | Custom bridge network `photoalbum-network`; DNS-based inter-service communication | High |
| **Volume Mounts** | `oracle_data` named volume (DB persistence); `./oracle-init` bind mount (init scripts) — app container has no volumes | High |
| **Resource Limits** | No container limits set; JVM heap capped via `JAVA_OPTS=-Xmx512m -Xms256m` | High |
| **Health Checks** | Oracle DB healthcheck in docker-compose (30s interval, 15 retries, 180s start period); no app-level health endpoint | High |

## Configuration & Profiles

| Fact | Finding | Confidence |
|------|---------|------------|
| **Environment Variables** | 8 total: `JAVA_OPTS`, `SPRING_PROFILES_ACTIVE`, `SPRING_DATASOURCE_URL/USERNAME/PASSWORD`, `ORACLE_PASSWORD`, `APP_USER`, `APP_USER_PASSWORD` | High |
| **Profile Settings** | 3 Spring profiles: `default` (Oracle), `docker` (Oracle/container), `test` (H2 in-memory) | High |
| **XML Configs** | None — pure annotation-based Spring Boot configuration (only `pom.xml` as build descriptor) | High |
| **Operating System** | Container: Debian/Ubuntu Linux (eclipse-temurin:8-jre); Dev workstations: cross-platform (Windows/macOS/Linux) | High |

## External Dependencies & Protocols

| Fact | Finding | Confidence |
|------|---------|------------|
| **External Services** | Oracle Database Free 23ai (`gvenzl/oracle-free:latest`) — sole external service | High |
| **External Dependencies** | Oracle DB only — no Redis, LDAP, S3, message queues, email, or third-party APIs | High |
| **Communication Protocols** | HTTP/REST inbound (port 8080); JDBC/TCP outbound to Oracle (port 1521) | High |

## Security & Compliance

| Fact | Finding | Confidence |
|------|---------|------------|
| **Security Implementation** | ⚠️ Minimal — no authentication, no authorization, no HTTPS/TLS, no Spring Security; only file upload MIME/size validation; credentials in plaintext env vars | High |
| **Compliance Requirements** | No regulatory compliance requirements detected (GDPR, HIPAA, PCI-DSS, SOX) | Medium |
| **Data Classification** | Public/Internal — stores photo images (BLOB) + technical metadata only; no PII, PHI, or financial data | High |
| **Licensing** | MIT (application); Apache 2.0 (Spring Boot, Commons IO); Oracle JTDN proprietary (ojdbc8); EPL/MPL (H2, test only) | High |

## Quality & Observability

| Fact | Finding | Confidence |
|------|---------|------------|
| **Testing Framework** | JUnit 5 (Jupiter) + Spring Boot Test + H2; minimal coverage: 1 test class, 1 context-load test | High |
| **Startup Instrumentation** | SLF4J + Logback (Spring Boot default); no APM, no AOP, no Spring Actuator | High |
| **Hardware Requirements** | Minimum 4 GB RAM, 2 CPU threads (Oracle DB limits); 12 GB storage recommended | High |

## Advanced Analysis

| Fact | Finding | Confidence |
|------|---------|------------|
| **Embedded Language Usage** | None — pure Java; client-side `upload.js` served as static asset only | High |
| **System Packages** | None explicitly installed; relies on packages bundled in `eclipse-temurin:8-jre` base | High |
| **Servlet Container** | Embedded Tomcat 9.x (Servlet 4.0), no external app server, JAR packaging | High |

## Key Observations & Modernization Notes

1. **Java 8 EOL**: Uses Java 8 (LTS support ended). Target upgrade to Java 17 or 21 for Azure Container Apps / AKS compatibility.
2. **Spring Boot 2.7.x EOL**: Version 2.7 reached end-of-life. Upgrade to Spring Boot 3.x requires Java 17+ and `jakarta.*` namespace migration.
3. **Oracle DB dependency**: Tight coupling to Oracle (ojdbc8, OracleDialect, BLOB storage). Azure migration may require transitioning to Azure Database for PostgreSQL or keeping Oracle on IaaS.
4. **No authentication/authorization**: The application is fully open — adding security is a prerequisite for any production deployment.
5. **No health endpoint**: Missing Spring Actuator means no `/actuator/health` for cloud-native health probes (required by AKS / Container Apps).
6. **No resource limits**: Container resource limits should be defined before deploying to Kubernetes or Azure Container Apps.
7. **Credentials in plaintext env vars**: Database passwords are in plaintext; Azure Key Vault integration is recommended.
8. **Minimal test coverage**: Only 1 context-load test exists — increasing test coverage before major refactoring is recommended.
