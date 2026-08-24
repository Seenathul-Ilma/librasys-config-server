# Config Server — Microservice Platform

**Student Name:** Seenathul Ilma  
**Student Number:** 241711005  
**Slack Handle:** Seenathul Ilma Musawwir  
**GCP Project ID:** librasys-eca 

---

## 📖 Description
The `config-server` centralizes externalized configuration for all microservices across development and production GCP environments.

## 🛠️ Technology Stack
- **Java 25**
- **Spring Boot 4.1.1**
- **Spring Cloud Config Server**

## 🚀 Getting Started
Runs on port `8888`. Must be started **first** before other services.

```bash
./mvnw clean package -DskipTests
java -jar target/config-server-0.0.1-SNAPSHOT.jar
```
