# Config Server (LibraSys - Enterprise Cloud Architecture 📚)

**Student Name:** Seenathul Ilma  
**Student Number:** 241711005  
**Slack Handle:** Seenathul Ilma Musawwir  
**GCP Project ID:** `librasys-eca`

---

## 📖 Overview

`config-server` is the **centralized configuration service** for LibraSys. Built with Spring Cloud Config, it serves environment-specific properties (database URLs, bucket names, ports, service credentials) to every other microservice at boot time, so no service ships hard-coded config.

This is a submodule of the parent repo **[`librasys-platform`](https://github.com/Seenathul-Ilma/librasys-platform)**.

---

## 🏗️ Architecture Breakdown

`config-server` is the **first** service to start in the whole stack — every other service (Eureka, Gateway, and all three business services) fetches its `application.yaml` from here before it can fully initialize.

```
config-server (8888)
   │  serves: user-service.yaml, book-service.yaml,
   │          loan-service.yaml, api-gateway.yaml,
   │          service-registry.yaml
   ▼
 ┌────────────┬────────────┬──────────────┬──────────────┬──────────────┐
 │ eureka     │ api-gateway│ user-service │ book-service │ loan-service │
 │ (8761)     │ (8080)     │ (8081)       │ (8082)       │ (8083)       │
 └────────────┴────────────┴──────────────┴──────────────┴──────────────┘
```

Each service is configured with `spring.config.import=configserver:http://localhost:8888` (or the deployed config-server address) and its own `spring.application.name`, which config-server uses to pick the matching `<service-name>.yaml`.

---

## 🧰 Tech Stack

| Item | Value |
|---|---|
| Framework | Spring Cloud Config Server, Java 25 |
| Property source | Native file system / classpath `resources/configurations` (per-service YAML files) |
| Build tool | Maven |
| Port | `8080 8888` |
| Process manager (prod) | PM2 (`autorestart: true`) |

---

## ⚙️ Setup & Local Run

### Build
```bash
git clone https://github.com/Seenathul-Ilma/librasys-config-server.git
cd librasys-config-server
mvn clean package -DskipTests
```

### Run
```bash
java -jar target/config-server-*.jar
```

### Verify
```bash
curl http://localhost:8888/user-service/default
curl http://localhost:8888/book-service/default
curl http://localhost:8888/loan-service/default
```
Each call should return the merged property document for that service, confirming config-server is serving correctly.

### Updating a service's configuration
Edit the matching YAML under `src/main/resources/configurations/` (e.g. `user-service.yaml` for the Cloud SQL public IP, `book-service.yaml` for the GCS bucket name), rebuild, and restart the dependent service to pick up the change.

---

## ☁️ Live Deployment

Runs as one of the 6 PM2-managed processes on the `librasys-backend-group` Managed Instance Group in project `librasys-eca` (`us-central1-a` / `us-central1-b`), fronted by the Global HTTP(S) Load Balancer at `https://librasys-eca.duckdns.org`.

---

## 🔗 Related Repositories
- Parent repo: https://github.com/Seenathul-Ilma/librasys-platform
- Business services: https://github.com/Seenathul-Ilma/librasys-services
- Frontend: https://github.com/Seenathul-Ilma/librasys-frontend