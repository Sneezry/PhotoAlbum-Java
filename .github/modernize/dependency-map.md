# Dependency Map

Photo Album is a Spring Boot 2.7 Java application with 8 declared runtime dependencies managed via the Spring Boot parent BOM.

## Dependencies

```mermaid
flowchart LR
    App["PhotoAlbum\nSpring Boot 2.7.18"]

    subgraph BOM["Parent BOM"]
        ParentBOM["spring-boot-starter-parent\nv2.7.18"]
    end
    subgraph Web["Web Frameworks"]
        SpringWeb["Spring Boot Web\n(Spring MVC)"]
        Thymeleaf["Spring Boot Thymeleaf\n(Thymeleaf 3.x)"]
    end
    subgraph DB["Database / ORM"]
        SpringJPA["Spring Boot Data JPA\n(Hibernate 5.x)"]
        OracleJDBC["Oracle JDBC Driver\nojdbc8 (runtime)"]
    end
    subgraph Util["Utilities"]
        Validation["Spring Boot Validation\n(Jakarta Validation)"]
        CommonsIO["Apache Commons IO\nv2.11.0"]
        SpringJSON["Spring Boot JSON\n(Jackson)"]
        DevTools["Spring Boot DevTools\n(optional)"]
    end

    App -->|"governed by"| BOM
    App -->|"web"| Web
    App -->|"persistence"| DB
    App -->|"utilities"| Util
    ParentBOM -.->|"manages versions"| Web
    ParentBOM -.->|"manages versions"| DB
    ParentBOM -.->|"manages versions"| Util
```
