# Project Facts

Consolidated assessment findings for the **Photo Album** Java application, generated from 36 individual fact analyses.

---

## Application Overview

| Fact | Finding | Confidence |
|------|---------|------------|
| **Application Name** | Photo Album | High |
| **Application Type** | Web Application (Spring MVC + REST API) | High |
| **Version** | 1.0.0 | High |
| **Architecture Pattern** | Layered Monolith with MVC pattern | High |
| **Licensing** | MIT License (Copyright © Microsoft Corporation) | High |

---

## Runtime & Technology Stack

| Fact | Finding | Confidence |
|------|---------|------------|
| **Runtime Environment** | Java 8 (JDK 1.8) with Spring Boot 2.7.18, Eclipse Temurin JRE 8 | High |
| **Servlet Container** | Embedded Apache Tomcat 9.0.x (Servlet 4.0 / Java EE 8) via spring-boot-starter-web; JAR packaging | High |
| **Language Dependencies** | 8 runtime/compile Maven dependencies (Spring Boot starters, Oracle JDBC, Commons IO); bundled in fat JAR | High |
| **XML Configs** | None — annotation-based Spring Boot configuration exclusively | High |
| **Profile Settings** | 3 Spring profiles: default (dev/Oracle), docker (container/Oracle), test (H2 in-memory) | High |
| **Startup Instrumentation** | SLF4J + Logback (Spring Boot default); no APM/telemetry; no AOP | High |
| **Embedded Language Usage** | None — pure Java with standard JDBC/JPA | High |
| **Testing Framework** | JUnit 5 (Jupiter) + Spring Boot Test + H2 in-memory database (1 test class) | High |

---

## Container & Infrastructure

| Fact | Finding | Confidence |
|------|---------|------------|
| **Container Engine** | Docker with Docker Compose V2 | High |
| **Container Version** | Docker Engine 20.10+ implied (Compose V2 format, no version field) | Medium |
| **Base Image** | Final: `eclipse-temurin:8-jre`; Build: `maven:3.9.6-eclipse-temurin-8` | High |
| **Multi-stage Build** | Yes — 2-stage build (Maven build + JRE runtime) | High |
| **Image Size** | Estimated 300–340 MB (JRE ~220 MB + Spring Boot fat JAR ~80–120 MB) | Medium |
| **Image Layers** | ~7–10 layers in final image (eclipse-temurin base layers + 1 COPY + 1 ENV) | High |
| **System Packages** | None additionally installed — relies on eclipse-temurin:8-jre base packages | High |
| **Orchestration Tool** | Docker Compose (2 services: oracle-db, photoalbum-java-app) | High |

---

## Service & Network Configuration

| Fact | Finding | Confidence |
|------|---------|------------|
| **Service Definition** | docker-compose.yml: 2 services, 1 named volume, 1 bind mount, 1 bridge network | High |
| **Application Port** | 8080 (HTTP); Oracle port 1521 (JDBC) | High |
| **Network Settings** | Custom bridge network `photoalbum-network`; internal DNS for oracle-db | High |
| **Volume Mounts** | `oracle_data:/opt/oracle/oradata` (named); `./oracle-init:/container-entrypoint-initdb.d` (bind) | High |
| **Health Checks** | oracle-db: `healthcheck.sh` with 30s interval, 10s timeout, 15 retries, 180s start period | High |
| **Resource Limits** | None configured at container level; JVM heap limited to 512 MB max via `JAVA_OPTS` | High |
| **Environment Variables** | 8 variables: JAVA_OPTS, SPRING_PROFILES_ACTIVE, SPRING_DATASOURCE_*, ORACLE_PASSWORD, APP_USER* | High |

---

## External Dependencies & Services

| Fact | Finding | Confidence |
|------|---------|------------|
| **External Services** | Oracle Database Free 23ai (`gvenzl/oracle-free:latest`) — relational data + BLOB photo storage | High |
| **External Dependencies** | Oracle Database only — no Redis, RabbitMQ, Kafka, LDAP, S3, or other external systems | High |
| **Communication Protocols** | HTTP/REST (Spring MVC, port 8080); JDBC/TCP (Oracle, port 1521); no gRPC/queues/WebSocket | High |

---

## Operating Environment

| Fact | Finding | Confidence |
|------|---------|------------|
| **Operating System** | Linux (Ubuntu-based via eclipse-temurin:8-jre); PowerShell scripts for Windows management | High |
| **Hardware Requirements** | Min 4 GB RAM (Oracle DB requirement); 2 CPU threads (Oracle XE limit); 2 GB+ disk | High |

---

## Security & Compliance

| Fact | Finding | Confidence |
|------|---------|------------|
| **Security Implementation** | Minimal — file upload MIME type whitelist + 10 MB size limit only. No HTTPS, auth, encryption, or security headers. Credentials in plaintext environment variables. | High |
| **Compliance Requirements** | None detected (no GDPR, HIPAA, PCI-DSS, or SOX markers in code or documentation) | Medium |
| **Data Classification** | Internal/Public — photo BLOBs and metadata only; no PII, PHI, PCI, or financial data | Medium |

---

## Summary

**Photo Album** is a straightforward **Spring Boot 2.7 Java 8 web application** that provides a photo gallery with upload, view, and delete capabilities. Key characteristics:

- **Single deployable unit** (layered monolith): controllers → services → JPA repositories → Oracle DB
- **Photos stored as Oracle BLOBs** — the application container is stateless; all persistent data resides in Oracle
- **No authentication or authorization** — the application is fully open-access
- **Docker Compose** manages local deployment with Oracle Free 23ai as a companion service
- **Spring Boot auto-configuration** is used throughout — no XML config, embedded Tomcat, annotation-driven
- **Java 8 / Spring Boot 2.7** are end-of-life releases; upgrading to Java 17/21 and Spring Boot 3.x is the primary modernization opportunity
- **Oracle-specific JDBC features** (ROWNUM, TO_CHAR, analytical functions) in native queries represent a migration consideration for cloud-managed databases
