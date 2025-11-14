# GIM: Geospatial Information Management System

## Project Name Proposal: GeoSemantics Bridge (GSB)

The proposed name, **GeoSemantics Bridge (GSB)**, reflects the project's core functionality: bridging traditional geospatial data management (PostgreSQL/PostGIS) with semantic web technologies (SPARQL/Fuseki).

## Overview

This project is a Spring Boot web application designed to integrate traditional relational database management with semantic web querying capabilities. It serves as a unified backend for managing and querying geospatial data stored in a PostgreSQL/PostGIS database while simultaneously providing access to a remote RDF store (Fuseki) via SPARQL.

The application is built with a focus on geospatial data, as evidenced by the use of PostGIS-related dependencies and controllers for handling BBOX and point queries.

## Key Technologies

| Category | Technology | Purpose |
| :--- | :--- | :--- |
| **Backend Framework** | Spring Boot (2.5.6) | Core application framework. |
| **Programming Language** | Java 11 | Primary development language. |
| **Relational Database** | PostgreSQL/PostGIS | Used for storing and querying structured geospatial data. |
| **ORM/Persistence** | Spring Data JPA / Hibernate | Handles database interactions. |
| **Semantic Web** | Apache Jena (3.13.1) | Library for working with RDF and executing SPARQL queries. |
| **RDF Store** | Apache Jena Fuseki | The remote SPARQL endpoint for semantic data. |
| **Connectivity** | JSch (0.1.55) | Used to establish a secure **SSH Tunnel** to the remote Fuseki server. |
| **Geospatial** | JTS (Java Topology Suite) | Used for geometric operations, such as point transformation. |

## Architecture and Mechanism

The project's architecture is characterized by its dual-connection approach:

### 1. Relational Database (PostgreSQL/PostGIS)

*   **Connection:** Configured via `application.properties` using standard Spring Data JPA settings.
    ```properties
    spring.datasource.url=jdbc:postgresql://131.220.71.164:5432/dm
    spring.datasource.username=dm
    spring.datasource.password=wise2023
    spring.datasource.driver-class-name=org.postgresql.Driver
    ```
*   **Functionality:** The `FieldsController` and related services (`FieldService`, `FieldsRepository`) handle typical CRUD and geospatial queries, such as retrieving fields by bounding box (`/api/fields/bbox`) or by a specific point (`/api/fields/point`).

### 2. Semantic Web (SPARQL/Fuseki)

*   **Secure Connection:** The application uses the `GIMConnector` class to establish a secure SSH tunnel to the remote Fuseki server. This is a critical component for accessing the RDF store, as it forwards a local port to the remote server's Fuseki port.
    *   **Mechanism:** JSch is used to connect to the SSH server using a private key (`src/main/resources/id_rsa`) and then sets up port forwarding.
*   **Query Execution:** The `GIMQuery` class, utilizing Apache Jena, handles the execution of SPARQL queries (SELECT, ASK, CONSTRUCT, DESCRIBE) against the tunneled Fuseki endpoint.
*   **Functionality:** The `FusekiController` exposes endpoints for semantic queries, such as translating German labels to English (`/api/fuseki/de2en`), retrieving images (`/api/fuseki/image`), and fetching descriptions (`/api/fuseki/description`), suggesting the RDF store contains a knowledge graph for linguistic or descriptive data.

## Project Structure

The project follows a standard Spring Boot structure with a clear separation of concerns:

```
GIM/
├── src/main/java/com/gim/project/
│   ├── Controller/          # REST API endpoints (Fields, Fuseki, Species, etc.)
│   ├── Entity/              # JPA Entities and SPARQL Query structure
│   ├── Repository/          # Spring Data JPA repositories
│   ├── Service/             # Business logic layer
│   ├── config/              # Configuration classes (GIMConnector, GIMConfig)
│   └── operations/          # Core operation logic (GIMQuery for SPARQL execution)
├── src/main/resources/
│   ├── application.properties # Configuration for DB and SSH/Fuseki
│   └── id_rsa               # Private key for SSH connection (Sensitive)
├── src/main/webapp/         # Frontend resources (index.html, script.js, styles.css)
└── pom.xml                  # Maven dependencies and build configuration
```

## Setup and Running

### Prerequisites

*   Java 11 or higher
*   Maven
*   Access to the remote PostgreSQL database and SSH server/Fuseki endpoint as configured in `application.properties`.

### Configuration

1.  **Database:** Ensure the PostgreSQL database at `131.220.71.164:5432/dm` is accessible with the provided credentials.
2.  **SSH/Fuseki:** The application requires a private key file (`id_rsa`) in `src/main/resources/` to establish the SSH tunnel to the Fuseki server. **This file is sensitive and should not be committed to a public repository.**

### Build and Run

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/Sefahmet/GIM.git
    cd GIM
    ```
2.  **Build the project:**
    ```bash
    ./mvnw clean install
    ```
3.  **Run the application:**
    ```bash
    ./mvnw spring-boot:run
    ```

The application will start on the default Spring Boot port (usually 8080). The `GIMConnector` will attempt to establish the SSH tunnel and open the Fuseki web interface in a browser upon successful connection.

## API Endpoints (Examples)

| Endpoint | Method | Description |
| :--- | :--- | :--- |
| `/api/fields/all` | `GET` | Retrieves all geospatial fields from the PostgreSQL database. |
| `/api/fields/bbox` | `GET` | Retrieves fields within a specified bounding box. |
| `/api/fuseki/de2en` | `GET` | Translates a German label to its English equivalent using the RDF store. |
| `/api/fuseki/image` | `GET` | Retrieves an image URL associated with a label from the RDF store. |
| `/api/fuseki/description` | `GET` | Retrieves a description for a label from the RDF store. |

---
*README.md generated by Manus AI.*
