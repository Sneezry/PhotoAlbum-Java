# App Modernization Assessment Summary

**Target Azure Services**: Azure App Service, Azure Kubernetes Service, Azure Container Apps

## Overall Statistics

**Total Applications**: 1

**Name: photo-album**
- Mandatory: 9 issues
- Potential: 2 issues
- Optional: 1 issue

> **Severity Levels Explained:**
> - **Mandatory**: The issue has to be resolved for the migration to be successful.
> - **Potential**: This issue may be blocking in some situations but not in others. These issues should be reviewed to determine whether a change is required or not.
> - **Optional**: The issue discovered is real issue fixing which could improve the app after migration, however it is not blocking.

## Applications Profile

### Name: photo-album
- **JDK Version**: 8
- **Frameworks**: Spring Boot 2.7.18, Spring Data JPA, Spring MVC, Hibernate ORM, Thymeleaf 3
- **Languages**: Java
- **Build Tools**: Maven 3

**Key Findings**:
- **Mandatory Issues (10 locations)**:
  - <!--ruleid=javaee-to-jakarta-01000-->Java EE javax.* namespace must migrate to Jakarta EE jakarta.* (2 locations found)
  - <!--ruleid=oracle-db-01003-->Oracle JDBC driver (ojdbc8) dependency (1 location found)
  - <!--ruleid=oracle-db-01004-->Oracle Hibernate dialect configured (1 location found)
  - <!--ruleid=oracle-db-01001-->Oracle-specific NVL function in native SQL queries (1 location found)
  - <!--ruleid=spring-boot-outdated-01000-->Spring Boot 2.x end-of-life - upgrade to Spring Boot 3.x (1 location found)
  - <!--ruleid=oracle-db-01002-->Oracle analytical functions (RANK OVER, SUM OVER) in native SQL (1 location found)
  - <!--ruleid=java-version-01000-->Java 8 end of public updates - upgrade to Java 17 or 21 LTS (1 location found)
  - <!--ruleid=oracle-db-01000-->Oracle-specific ROWNUM used in native SQL queries (1 location found)
  - <!--ruleid=secrets-in-config-01000-->Database credentials hardcoded in configuration files (1 location found)
- **Potential Issues (2 locations)**:
  - <!--ruleid=blob-storage-01000-->Binary data stored as database BLOB - migrate to cloud object storage (1 location found)
  - <!--ruleid=jpa-ddl-auto-01000-->JPA DDL auto=create drops and recreates schema on startup (1 location found)
- **Optional Issues (1 locations)**:
  - <!--ruleid=logging-01000-->Debug-level logging configured for production (1 location found)

## Next Steps

For comprehensive migration guidance and best practices, visit:
- [GitHub Copilot App Modernization](https://aka.ms/ghcp-appmod)

