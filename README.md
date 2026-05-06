# ecommerce-spring-cloud-config-server

Centralized configuration server for the Spring Cloud e-commerce demo project.  
It reads YAML configuration files from a Git repository and exposes them to all other microservices via HTTP.

## How it works

On startup, this service clones the [config-repo](https://github.com/keviendamico/ecommerce-spring-cloud-config-repo) from GitHub and serves each service's configuration at:

```
http://localhost:8888/{application-name}/{profile}
```

Example — fetching the product-service default config:

```
GET http://localhost:8888/product-service/default
```

## Stack

| Layer | Technology |
|---|---|
| Language | Java 21 |
| Framework | Spring Boot 4.0.6 |
| Config Server | Spring Cloud Config Server 2025.1.1 |
| Config source | Git (GitHub) |

## Configuration (`application.yaml`)

```yaml
server:
  port: 8888
spring:
  application:
    name: config-server
  cloud:
    config:
      server:
        git:
          uri: https://github.com/keviendamico/ecommerce-spring-cloud-config-repo
          default-label: main
          clone-on-start: true
```

## Running

```bash
./mvnw spring-boot:run
```

The server starts on port **8888** and is the first service that must be up before any other microservice starts.

---

# Spring Cloud E-Commerce — Project Overview

A minimal microservices-based e-commerce system built as a learning project for Spring Cloud fundamentals.  
The goal is to cover the main Spring Cloud components hands-on: centralized config, service discovery, inter-service communication, API gateway, and resilience.

## Architecture

```
[HTTP Client]
      |
[API Gateway :8080]
      |
      ├──→ [Product Service   :8081]
      ├──→ [Inventory Service :8082]
      └──→ [Order Service     :8083]
                  |
                  ├──→ [Product Service]    (via OpenFeign)
                  └──→ [Inventory Service]  (via OpenFeign)

All services register on  → [Eureka Discovery Server :8761]
All services read config from → [Config Server :8888]
Config Server reads from      → [config-repo on GitHub]
```

## Repositories

| # | Repository | Purpose |
|---|---|---|
| 1 | `ecommerce-spring-cloud-config-repo` | YAML configuration files, read by Config Server via Git |
| 2 | `ecommerce-spring-cloud-config-server` | Reads config-repo and exposes it to all services |
| 3 | `ecommerce-spring-cloud-discovery-server` | Eureka — service registry |
| 4 | `ecommerce-spring-cloud-api-gateway` | Single entry point, routes requests to microservices |
| 5 | `ecommerce-spring-cloud-product-service` | Product CRUD |
| 6 | `ecommerce-spring-cloud-inventory-service` | Inventory CRUD |
| 7 | `ecommerce-spring-cloud-order-service` | Order orchestration, calls product and inventory |

## Startup Order

Services must be started in this order:

```
1. config-server        :8888
2. discovery-server     :8761
3. product-service      :8081
4. inventory-service    :8082
5. order-service        :8083
6. api-gateway          :8080
```

## Spring Cloud Concepts Covered

| Concept | Component | Repository |
|---|---|---|
| Centralized configuration | Spring Cloud Config | config-server + config-repo |
| Service discovery | Eureka | discovery-server |
| Client-side load balancing | Spring Cloud LoadBalancer | built into Feign and Gateway |
| Inter-service communication | OpenFeign | order-service |
| API Gateway / routing | Spring Cloud Gateway | api-gateway |
| Circuit Breaker | Resilience4j | order-service (optional) |

## Common Stack

- **Java 21**
- **Spring Boot 4.0.6**
- **Spring Cloud 2025.1.1**